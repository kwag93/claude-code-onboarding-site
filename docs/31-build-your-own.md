# 31. 직접 만들기 — 나만의 도구 만들기

워크플로우는 저마다 달라요. 범용 도구로 부족하다면 직접 만들면 됩니다.

> "Claude Code의 진짜 힘은 쓰는 것이 아니라, 확장하는 것에 있습니다"

---

## 실전 사례: spot-orbit-plugin

실제로 만들어진 플러그인을 먼저 볼까요? [spot-orbit-plugin](https://github.com/kwag93/spot-orbit-plugin)은 Boston Dynamics의 Spot 로봇과 Orbit API 개발을 위해 만든 Claude Code 플러그인이에요.

### 무엇을 할 수 있나?

| 스킬 | 기능 |
|------|------|
| `/orbit-api` | Orbit REST API 탐색 및 테스트 |
| `/spx-build` | SPX 확장 패키지 빌드 및 검증 |
| `/spot-explore` | Spot SDK 문서 및 예제 검색 |
| `/cert-setup` | HTTPS 인증서 생성 |
| `/spot-troubleshoot` | 연결, 미션, 하드웨어 문제 진단 |

### 어떻게 구성되어 있나?

```text
spot-orbit-plugin/
├── skills/          # 5개의 스킬 (위 테이블)
├── agents/          # 전문 에이전트 (orbit-explorer, spx-architect 등)
├── hooks/           # PreToolUse 안전 가드레일
├── mcp-server/      # Orbit API 직접 접근
└── docs/            # 32개 참조 문서
```

핵심 포인트:
- **MCP 서버**로 Orbit API에 직접 접근 (읽기/쓰기)
- **PreToolUse Hook**으로 SPX 패키지 유효성 자동 검증
- **32개 참조 문서**로 Claude에게 도메인 지식 제공
- MIT 라이선스, 오픈소스

> 이런 수준의 플러그인을 직접 만들 수 있어요. 아래에서 단계별로 알아봅시다.

---

## 첫 번째 스킬 만들기 (5분 튜토리얼)

가장 간단한 확장부터 시작해요. 스킬 하나라면 5분이면 충분해요.

### 1단계: 반복 패턴 식별

매번 비슷하게 요청하는 게 있나요?

```text
# 이런 걸 매번 타이핑하고 있다면...
> "변경사항을 conventional commits 형식으로 커밋해줘.
> 한국어로, scope는 모듈명으로, 타입은 자동 판단."
```

### 2단계: 스킬 파일 생성

```bash
mkdir -p .claude/skills
```

### 3단계: SKILL.md 작성

```markdown
# .claude/skills/korean-commit.md
---
name: Korean Commit
description: 한국어 conventional commits 형식으로 커밋 메시지 생성
triggers:
  - "커밋해줘"
  - "commit"
  - "/korean-commit"
---

## 규칙
1. 변경된 파일을 분석합니다
2. Conventional Commits 형식으로 커밋 메시지를 생성합니다
   - type: feat, fix, refactor, chore, docs, test, perf 중 선택
   - scope: 변경된 주요 모듈 또는 디렉토리명
   - description: 한국어로 변경 내용 요약
3. 사용자 확인 후 커밋합니다

## 예시
feat(auth): JWT 토큰 검증 로직 추가
fix(api): 사용자 조회 시 null 체크 누락 수정
```

### 4단계: 테스트

```text
> "커밋해줘"

# 또는 직접 슬래시 명령으로
> /korean-commit
```

트리거 키워드가 포함되면 스킬이 자동으로 활성화돼요. `/korean-commit`처럼 슬래시 명령으로도 호출할 수 있어요.

정상 동작하면 이런 흐름이에요:
```text
> "커밋해줘"

Claude: "변경 사항을 분석했습니다:
- src/auth/login.ts: JWT 검증 로직 추가

제안 커밋 메시지:
feat(auth): JWT 토큰 검증 로직 추가

이 메시지로 커밋할까요?"
```

### 5단계: 반복 개선

쓰다가 부족한 점이 보이면 스킬 파일을 수정하세요. 마크다운 파일이라 언제든 편집할 수 있어요.

---

## 첫 번째 플러그인 만들기

스킬 하나로는 부족할 때는 여러 스킬 + Hook + 에이전트를 묶은 **플러그인**을 만들 수 있어요.

### 플러그인 구조

```text
my-plugin/
├── skills/
│   ├── skill-one.md
│   └── skill-two.md
├── agents/
│   └── specialist.md
├── hooks/
│   └── safety-check.sh
├── docs/
│   └── reference.md
└── README.md
```

### 스텝별 가이드

**1. 목적 정의**: 어떤 워크플로우를 자동화할 건지 명확히

```text
# 예: "우리 팀의 코드 리뷰 프로세스를 자동화하고 싶다"
# → 스킬: /review (코드 분석), /checklist (체크리스트 생성)
# → Hook: PR 생성 시 자동 리뷰
# → 에이전트: 보안 전문 리뷰어
```

**2. 스킬 작성**: 위의 5분 튜토리얼 방식으로 각 스킬 생성

**3. Hook 추가** (선택):

```json
// .claude/settings.json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "Bash",
      "command": "bash hooks/safety-check.sh"
    }]
  }
}
```

**4. 에이전트 추가** (선택):

```markdown
# agents/security-reviewer.md
---
name: security-reviewer
description: 보안 관점에서 코드를 리뷰하는 전문 에이전트
model: sonnet
---

당신은 보안 전문 코드 리뷰어입니다.
OWASP Top 10을 기준으로 코드를 분석하세요.
```

**5. skill-creator 활용**:

```text
/oh-my-claudecode:skill
```

이 메타 스킬이 플러그인 구조를 자동으로 생성해줄 수 있어요.

---

## 채널 만들기 가이드

외부 시스템 이벤트를 Claude Code에 전달하는 채널도 만들 수 있어요. 개념은 [Channels 가이드](28-channels.md)에서 먼저 확인하세요.

### 간단한 단방향 웹훅 채널

```typescript
// my-webhook.ts
import { Server } from '@modelcontextprotocol/sdk/server/index.js'
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js'

const mcp = new Server(
  { name: 'my-webhook', version: '0.0.1' },
  {
    capabilities: { experimental: { 'claude/channel': {} } },
    instructions: 'CI/CD 알림이 도착하면 분석하고 적절히 대응하세요.',
  },
)

await mcp.connect(new StdioServerTransport())

// HTTP 서버로 웹훅 수신
Bun.serve({
  port: 9000,
  hostname: '127.0.0.1',
  async fetch(req) {
    const body = await req.text()
    await mcp.notification({
      method: 'notifications/claude/channel',
      params: { content: body },
    })
    return new Response('ok')
  },
})
```

### 양방향 채팅 브릿지

양방향으로 만들려면 `tools: {}` capability와 Reply Tool을 추가하세요. 자세한 구현은 [Channels 가이드의 Reply Tool 섹션](28-channels.md#양방향-reply-tool-추가)을 참고하세요.

---

## 자체 API 연결하기

사내 API나 자체 서비스를 Claude Code에 연결하려면 MCP 서버를 만들면 돼요.

### MCP 서버로 REST API 연결

```typescript
// my-api-server.ts
import { Server } from '@modelcontextprotocol/sdk/server/index.js'
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js'
import { ListToolsRequestSchema, CallToolRequestSchema } from '@modelcontextprotocol/sdk/types.js'

const mcp = new Server(
  { name: 'my-api', version: '0.0.1' },
  { capabilities: { tools: {} } },
)

// 도구 정의
mcp.setRequestHandler(ListToolsRequestSchema, async () => ({
  tools: [{
    name: 'get_users',
    description: '사용자 목록을 조회합니다',
    inputSchema: {
      type: 'object',
      properties: {
        page: { type: 'number', description: '페이지 번호' },
      },
    },
  }],
}))

// 도구 실행
mcp.setRequestHandler(CallToolRequestSchema, async req => {
  if (req.params.name === 'get_users') {
    const { page = 1 } = req.params.arguments
    const res = await fetch(`https://api.mycompany.com/users?page=${page}`, {
      headers: { 'Authorization': `Bearer ${process.env.API_TOKEN}` },
    })
    const data = await res.json()
    return { content: [{ type: 'text', text: JSON.stringify(data, null, 2) }] }
  }
  throw new Error(`unknown tool: ${req.params.name}`)
})

await mcp.connect(new StdioServerTransport())
```

### .mcp.json에 등록

```json
{
  "mcpServers": {
    "my-api": {
      "command": "bun",
      "args": ["./my-api-server.ts"],
      "env": {
        "API_TOKEN": "your-token-here"
      }
    }
  }
}
```

### 인증 패턴

| 방법 | 장점 | 사용 시점 |
|------|------|-----------|
| 환경변수 (env) | 간단, .mcp.json에 설정 | API 키, 토큰 |
| 키체인/시크릿 매니저 | 보안, 코드에 노출 안 됨 | 프로덕션 API |
| OAuth 흐름 | 표준화된 인증 | 외부 서비스 연동 |

> API 토큰을 절대 코드에 하드코딩하지 마세요. 환경변수나 시크릿 매니저를 사용하세요.

---

## 로컬 테스트와 디버깅

만든 도구가 제대로 동작하는지 확인하는 방법이에요.

### 스킬 테스트

```text
# 트리거 키워드로 호출해보기
> "커밋해줘"  # triggers에 "커밋" 포함 시

# 직접 호출
> "/korean-commit"
```

### 플러그인/채널 테스트

```bash
# 개발 모드로 시작 (allowlist 우회)
claude --dangerously-load-development-channels server:my-webhook
```

### MCP 서버 디버깅

```bash
# MCP 서버 상태 확인 (Claude Code 안에서)
/mcp

# 디버그 로그 확인
cat ~/.claude/debug/<session-id>.txt

# 서버가 도구를 제공하는지 확인
> "사용 가능한 MCP 도구를 보여줘"
```

### 흔한 문제와 해결

| 증상 | 원인 | 해결 |
|------|------|------|
| 스킬이 트리거 안 됨 | triggers 키워드 불일치 | frontmatter의 triggers 확인 |
| MCP 도구가 안 보임 | 서버 시작 실패 | /mcp로 상태 확인, 로그 확인 |
| 채널 이벤트 안 도착 | capability 미선언 | `claude/channel` 확인 |
| 권한 오류 | settings.json 미설정 | allow 목록에 도구 추가 |

---

## 배포와 공유

### 팀 내 공유 (Git)

가장 간단한 방법이에요:

```bash
# 스킬과 설정을 git으로 추적
git add .claude/skills/ CLAUDE.md
git commit -m "feat: 팀 커밋 스킬 및 프로젝트 규칙 추가"
git push
```

팀원이 pull하면 동일한 스킬을 바로 사용할 수 있어요.

### 플러그인 마켓플레이스

더 넓게 공유하려면 플러그인으로 패키징하세요:

```text
# 플러그인 생성 도구 활용
/oh-my-claudecode:skill

# 또는 공식 마켓플레이스에 제출
# https://code.claude.com/docs/en/plugins#submit-your-plugin
```

---

## 다음 단계

- [스킬과 플러그인](11-skills-plugins.md) — 스킬 시스템 상세 가이드
- [Channels](28-channels.md) — 채널 아키텍처 전체 가이드
- [MCP 생태계](10-mcp-ecosystem.md) — MCP 서버 활용법
- [Hooks 실전 가이드](16-hooks-practical.md) — Hook 작성 상세 가이드
