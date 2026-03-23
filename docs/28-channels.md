# 28. Channels — 외부 이벤트를 Claude Code로

Claude Code 세션에 외부 이벤트를 밀어 넣을 수 있는 Channels 기능이에요.

> "터미널 밖에서 일어나는 일을 Claude가 실시간으로 알 수 있다면?"

---

## Channels이란?

Channel은 **MCP 서버**가 Claude Code 세션에 이벤트를 푸시하는 메커니즘이에요.
CI가 실패했을 때, Slack에서 메시지가 왔을 때, 모니터링 알림이 뜰 때 — Claude가 자동으로 반응할 수 있습니다.

> ⚠️ Channels은 **리서치 프리뷰** 상태이며 Claude Code v2.1.80 이상이 필요해요.
> claude.ai 로그인이 필요하고, Console/API 키 인증은 지원되지 않습니다.

---

## 단방향 vs 양방향

| 유형 | 동작 | 예시 |
|------|------|------|
| 단방향 | 이벤트를 Claude에게 전달만 | CI 알림, 모니터링, 웹훅 |
| 양방향 | 이벤트 전달 + Claude가 응답 | Telegram 봇, Discord 봇 |

### 동작 원리

```text
외부 시스템 (CI, Slack, 웹훅)
    ↓ POST / 메시지
채널 서버 (로컬 MCP 서버)
    ↓ notifications/claude/channel
Claude Code 세션
    ↓ (양방향일 때) reply tool
채널 서버 → 외부 시스템
```

채널 서버는 로컬에서 실행되며, Claude Code가 서브프로세스로 관리해요.

---

## 기본 제공 채널

리서치 프리뷰에 포함된 채널:

| 채널 | 유형 | 설명 |
|------|------|------|
| Telegram | 양방향 | 텔레그램 봇으로 Claude와 대화 |
| Discord | 양방향 | 디스코드 봇으로 Claude와 대화 |
| fakechat | 양방향 | 로컬 테스트용 웹 UI 채팅 |

### 사용 방법

```bash
# 채널과 함께 Claude Code 시작
claude --channels plugin:telegram@anthropic

# 여러 채널 동시 사용
claude --channels plugin:telegram@anthropic,plugin:discord@anthropic
```

---

## 웹훅 수신기 만들기 (예제)

가장 간단한 단방향 채널을 직접 만들어 봅시다. CI 파이프라인이나 모니터링 시스템의 웹훅을 Claude에게 전달하는 서버예요.

### 1단계: 프로젝트 생성

```bash
mkdir webhook-channel && cd webhook-channel
bun add @modelcontextprotocol/sdk
```

### 2단계: 채널 서버 작성

```typescript
// webhook.ts
import { Server } from '@modelcontextprotocol/sdk/server/index.js'
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js'

// MCP 서버 생성 — claude/channel이 핵심!
const mcp = new Server(
  { name: 'webhook', version: '0.0.1' },
  {
    capabilities: {
      experimental: { 'claude/channel': {} }  // 이게 채널로 등록시켜요
    },
    instructions: '웹훅 이벤트가 <channel source="webhook">로 도착합니다. 읽고 적절히 대응하세요.',
  },
)

// Claude Code와 stdio로 연결
await mcp.connect(new StdioServerTransport())

// HTTP 서버: 외부 웹훅을 받아서 Claude에게 전달
Bun.serve({
  port: 8788,
  hostname: '127.0.0.1',  // 로컬만 접근 가능
  async fetch(req) {
    const body = await req.text()
    await mcp.notification({
      method: 'notifications/claude/channel',
      params: {
        content: body,
        meta: { path: new URL(req.url).pathname, method: req.method },
      },
    })
    return new Response('ok')
  },
})
```

핵심 3가지:
1. `claude/channel` capability → Claude Code에 채널로 등록
2. `instructions` → Claude의 시스템 프롬프트에 추가
3. `mcp.notification()` → 이벤트를 Claude에게 전달

### 3단계: MCP 설정에 등록

```json
// .mcp.json
{
  "mcpServers": {
    "webhook": { "command": "bun", "args": ["./webhook.ts"] }
  }
}
```

### 4단계: 테스트

```bash
# 터미널 1: 개발 모드로 Claude Code 시작
claude --dangerously-load-development-channels server:webhook

# 터미널 2: 웹훅 시뮬레이션
curl -X POST localhost:8788 -d "build failed on main: https://ci.example.com/run/1234"
```

Claude에게 다음과 같이 도착해요:

```text
<channel source="webhook" path="/" method="POST">
build failed on main: https://ci.example.com/run/1234
</channel>
```

---

## 양방향: Reply Tool 추가

단방향 채널을 양방향으로 만들려면 **Reply Tool**을 추가하세요.

### capabilities에 tools 추가

```typescript
capabilities: {
  experimental: { 'claude/channel': {} },
  tools: {},  // 이것만 추가하면 도구 탐색 활성화
},
```

### 도구 핸들러 등록

```typescript
import { ListToolsRequestSchema, CallToolRequestSchema } from '@modelcontextprotocol/sdk/types.js'

// 도구 목록 제공
mcp.setRequestHandler(ListToolsRequestSchema, async () => ({
  tools: [{
    name: 'reply',
    description: '채널을 통해 메시지를 회신합니다',
    inputSchema: {
      type: 'object',
      properties: {
        chat_id: { type: 'string', description: '회신할 대화 ID' },
        text: { type: 'string', description: '보낼 메시지' },
      },
      required: ['chat_id', 'text'],
    },
  }],
}))

// 도구 호출 처리
mcp.setRequestHandler(CallToolRequestSchema, async req => {
  if (req.params.name === 'reply') {
    const { chat_id, text } = req.params.arguments
    // 여기서 실제 플랫폼 API로 메시지 전송
    await sendToplatform(chat_id, text)
    return { content: [{ type: 'text', text: 'sent' }] }
  }
  throw new Error(`unknown tool: ${req.params.name}`)
})
```

### instructions 업데이트

```typescript
instructions: '메시지가 <channel source="webhook" chat_id="...">로 도착합니다. reply 도구로 회신하세요. chat_id를 전달해야 합니다.',
```

---

## 권한 릴레이 (Permission Relay)

양방향 채널은 **도구 승인 요청을 원격으로 전달**할 수 있어요. 예를 들어 모바일에서 "이 Bash 명령 실행해도 돼?"라는 질문에 승인/거부할 수 있습니다.

> v2.1.81 이상 필요

### 동작 흐름

1. Claude가 도구 호출 시도 → 로컬 터미널에 승인 다이얼로그
2. 동시에 채널 서버에 `permission_request` 알림
3. 채널 서버가 채팅 앱으로 전달: "Claude가 Bash를 실행하려 합니다. yes abcde 또는 no abcde"
4. 원격 응답을 받아 `permission` 알림으로 회신
5. **로컬과 원격 중 먼저 응답한 쪽이 적용**

### 설정

```typescript
capabilities: {
  experimental: {
    'claude/channel': {},
    'claude/channel/permission': {},  // 권한 릴레이 활성화
  },
  tools: {},
},
```

> ⚠️ 권한 릴레이는 반드시 **발신자 인증(센더 게이팅)**과 함께 사용하세요. 인증 없이 열어두면 누구나 도구 실행을 승인할 수 있어요.

---

## 센더 게이팅 (보안)

게이팅 없는 채널은 **프롬프트 인젝션 벡터**예요. 엔드포인트에 접근할 수 있는 누구나 Claude에게 텍스트를 전달할 수 있습니다.

### allowlist 기반 발신자 확인

```typescript
const allowed = new Set(['user123', 'admin456'])

// 메시지 핸들러 안에서
if (!allowed.has(message.from.id)) {
  return  // 무시
}
await mcp.notification({ ... })
```

> **중요**: `message.from.id`(발신자)로 확인하세요. `message.chat.id`(채팅방)로 하면 그룹 채팅에서 아무나 메시지를 보낼 수 있어요.

---

## 트러블슈팅

| 증상 | 원인 | 해결 |
|------|------|------|
| curl은 성공하지만 Claude에 안 도착 | MCP 서버 연결 실패 | `/mcp`로 서버 상태 확인, `~/.claude/debug/` 로그 확인 |
| curl이 connection refused | 포트 미바인딩 또는 이전 프로세스 | `lsof -i :<port>`로 확인, 이전 프로세스 kill |
| "blocked by org policy" | Team/Enterprise 설정 | 관리자가 channels 활성화 필요 |
| 이벤트가 지연됨 | Claude가 다른 요청 처리 중 | 정상 동작 — 유휴 시 실행됨 |

디버그 로그 위치: `~/.claude/debug/<session-id>.txt`

---

## 제한사항

- **리서치 프리뷰**: 기능이 변경될 수 있어요
- **커스텀 채널**: `--dangerously-load-development-channels` 플래그 필요
- **인증**: claude.ai 로그인 필수 (API 키 미지원)
- **Team/Enterprise**: 관리자가 명시적으로 활성화해야 함

---

## 다음 단계

- [직접 만들기](31-build-your-own.md) — 나만의 채널을 처음부터 만드는 튜토리얼
- [공식 Channels 문서](https://code.claude.com/docs/en/channels-reference) — 전체 API 레퍼런스
- [공식 채널 구현체](https://github.com/anthropics/claude-plugins-official/tree/main/external_plugins) — Telegram, Discord 소스 코드
