# 16. Hooks 실전 가이드

git hooks가 커밋을 지키듯, Claude hooks가 AI 작업을 지킵니다. 자동으로, 조용하게.

---

## Hooks란?

Claude Code의 **라이프사이클 이벤트**에 맞춰 실행되는 셸 명령, HTTP 요청, LLM 프롬프트, 에이전트예요.

```text
[세션 시작]      → SessionStart hook
[사용자 입력]    → UserPromptSubmit hook
[도구 실행 전]   → PreToolUse hook
[권한 요청]      → PermissionRequest hook
[도구 실행]
[도구 실행 후]   → PostToolUse / PostToolUseFailure hook
[알림 발생]      → Notification hook
[서브에이전트]   → SubagentStart / SubagentStop hook
[작업 완료]      → Stop / TaskCompleted hook
[컨텍스트 압축]  → PreCompact hook
[세션 종료]      → SessionEnd hook
```

AI가 뭔가를 하려고 할 때마다, 또는 끝날 때마다 **여러분이 지정한 로직이 먼저/나중에 돌아갑니다.**

> "훅은 AI에게 규칙을 심는 장치입니다. 매번 말 안 해도, 항상 지켜집니다."

역할별로 정리하면

| 역할 | 예시 |
| --- | --- |
| **품질 게이트** | 커밋 전 타입 체크 강제, 작업 완료 시 테스트 통과 확인 |
| **자동 검증** | 파일 편집 후 린팅 자동 실행 |
| **컨텍스트 주입** | 세션 시작 시 브랜치·이슈 정보 자동 첨부 |
| **보안 가드** | `.env` 파일 수정 차단, 위험 명령 자동 거부 |
| **외부 연동** | Slack/Discord 알림, 외부 API 호출 |

---

## 17가지 Hook 이벤트

### 세션 라이프사이클

| 이벤트 | 실행 시점 | matcher | 차단 | 주요 용도 |
| --- | --- | --- | --- | --- |
| **SessionStart** | 세션 시작/재개 시 | `startup`, `resume`, `clear` | - | 환경 초기화, 컨텍스트 주입 |
| **SessionEnd** | 세션 종료 시 | `clear`, `logout` 등 | - | 정리 작업, 아카이브 |

### 사용자 입력

| 이벤트 | 실행 시점 | matcher | 차단 | 주요 용도 |
| --- | --- | --- | --- | --- |
| **UserPromptSubmit** | 프롬프트 제출 시 | ❌ 없음 | decision | 프롬프트 검증, 컨텍스트 주입 |

### 도구 라이프사이클

| 이벤트 | 실행 시점 | matcher | 차단 | 주요 용도 |
| --- | --- | --- | --- | --- |
| **PreToolUse** | 도구 실행 직전 | 도구 이름 | exit 2 | 위험 작업 차단, 사전 검증 |
| **PermissionRequest** | 권한 확인 시 | 도구 이름 | - | 안전한 명령 자동 허용 |
| **PostToolUse** | 도구 실행 성공 후 | 도구 이름 | decision | 자동 린팅, 로깅 |
| **PostToolUseFailure** | 도구 실행 실패 후 | 도구 이름 | decision | 복구 힌트, 대안 제시 |

### 에이전트

| 이벤트 | 실행 시점 | matcher | 차단 | 주요 용도 |
| --- | --- | --- | --- | --- |
| **SubagentStart** | 서브에이전트 생성 시 | 에이전트 타입 | - | 역할별 체크리스트 주입 |
| **SubagentStop** | 서브에이전트 완료 시 | 에이전트 타입 | decision | 결과 검증, 재시도 |
| **TeammateIdle** | 팀원이 유휴 상태 | ❌ 없음 | - | 추가 작업 할당 |

### 완료

| 이벤트 | 실행 시점 | matcher | 차단 | 주요 용도 |
| --- | --- | --- | --- | --- |
| **Stop** | Claude 응답 완료 시 | ❌ 없음 | decision | 최종 검증, 정리 |
| **TaskCompleted** | 작업 완료 표시 시 | ❌ 없음 | decision | 품질 게이트 (lint/test) |

### 설정 & 워크트리

| 이벤트 | 실행 시점 | matcher | 차단 | 주요 용도 |
| --- | --- | --- | --- | --- |
| **ConfigChange** | 설정 파일 변경 시 | `user_settings` 등 | decision | 감사 로그 |
| **WorktreeCreate** | 워크트리 생성 시 | ❌ 없음 | - | 표준 디렉토리 구조 |
| **WorktreeRemove** | 워크트리 제거 시 | ❌ 없음 | - | 임시 파일 정리 |

### 기타

| 이벤트 | 실행 시점 | matcher | 차단 | 주요 용도 |
| --- | --- | --- | --- | --- |
| **PreCompact** | 컨텍스트 압축 전 | `manual`, `auto` | - | TODO/검증 결과 저장 |
| **Notification** | 알림 발생 시 | `permission_prompt` 등 | - | Slack/Discord 알림 |

> **matcher**는 이벤트를 필터링하는 정규식이에요. `Edit|Write`처럼 쓰면 해당 도구에서만 훅이 실행됩니다. "❌ 없음"인 이벤트는 항상 모든 발생에서 실행돼요.

---

## 4가지 훅 타입

| 타입 | 설명 | 적합한 경우 |
| --- | --- | --- |
| **command** | 셸 명령 실행 | 린팅, 파일 보호, 로깅 |
| **http** | HTTP POST 요청 | 외부 API 연동, 웹훅 |
| **prompt** | LLM에 단일 질문 | 코드 검증, 규칙 준수 확인 |
| **agent** | 서브에이전트 생성 | 복잡한 검증, 다단계 확인 |

### command 타입

가장 기본적인 타입이에요. 셸 스크립트를 실행합니다.

```json
{
  "type": "command",
  "command": "pnpm lint --fix"
}
```

### http 타입

이벤트 데이터를 HTTP POST로 전송해요. 외부 서비스 연동에 유용합니다.

```json
{
  "type": "http",
  "url": "http://localhost:8080/hooks/tool-use",
  "headers": {
    "Authorization": "Bearer $MY_TOKEN"
  },
  "allowedEnvVars": ["MY_TOKEN"]
}
```

### prompt 타입

LLM에게 yes/no 판단을 요청해요. 코드 검증에 활용합니다.

```json
{
  "type": "prompt",
  "prompt": "이 변경이 보안 규칙을 준수하는지 확인해주세요"
}
```

### agent 타입

Read, Grep, Glob 같은 도구를 쓸 수 있는 서브에이전트를 생성해요. 복잡한 검증에 적합합니다.

```json
{
  "type": "agent",
  "prompt": "변경된 파일이 프로젝트 컨벤션을 따르는지 검증해주세요"
}
```

---

## 설정 위치

훅은 `settings.json`에 정의합니다.

| 범위 | 파일 위치 | 적용 대상 |
| --- | --- | --- |
| **프로젝트** | `.claude/settings.json` | 해당 프로젝트만 |
| **프로젝트 (개인)** | `.claude/settings.local.json` | 해당 프로젝트, git 무시 |
| **전역** | `~/.claude/settings.json` | 모든 프로젝트 |
| **관리형** | 조직 관리 설정 | 조직 전체 정책 |
| **플러그인** | 플러그인 `hooks/hooks.json` | 플러그인 범위 |
| **에이전트** | 에이전트 frontmatter | 해당 에이전트만 |

기본 구조

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "echo '파일이 수정되었습니다'"
          }
        ]
      }
    ]
  }
}
```

`matcher`는 도구 이름을 정규식으로 매칭합니다. `Edit|Write`는 Edit 또는 Write 도구가 실행될 때 동작해요.

---

## 실전 레시피

### 레시피 1: 파일 편집 후 자동 린팅

파일을 고칠 때마다 린트 + 포맷팅을 알아서 적용해요.
실수로 규칙 위반 코드를 남기는 일이 없어지죠.

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "pnpm lint --fix"
          }
        ]
      }
    ]
  }
}
```

특정 파일만 린팅하고 싶다면, stdin JSON에서 파일 경로를 추출합니다:

```bash
#!/bin/bash
# .claude/hooks/lint-on-save.sh
INPUT=$(cat)
FILE=$(echo "$INPUT" | jq -r '.tool_input.file_path // empty')

if [[ "$FILE" == *.ts || "$FILE" == *.tsx ]]; then
  pnpm eslint --fix "$FILE"
fi
```

---

### 레시피 2: 민감 파일 보호

`.env`, `credentials.json` 같은 민감한 파일을 AI가 수정하려 하면 바로 차단해요.

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/protect-files.sh"
          }
        ]
      }
    ]
  }
}
```

```bash
#!/bin/bash
# .claude/hooks/protect-files.sh
INPUT=$(cat)
FILE=$(echo "$INPUT" | jq -r '.tool_input.file_path // empty')

PROTECTED=(".env" ".env.local" ".env.production" "credentials.json")

for pattern in "${PROTECTED[@]}"; do
  if [[ "$FILE" == *"$pattern"* ]]; then
    echo "보호된 파일입니다: $FILE"
    exit 2  # exit 2 = Claude에게 도구 실행 중단 신호
  fi
done

exit 0
```

`exit 2`가 핵심이에요. PreToolUse에서 이 코드를 반환하면 Claude가 그 도구 실행을 취소합니다.

---

### 레시피 3: 커밋 전 타입 체크 강제

`git commit`을 실행하기 전에 TypeScript 타입 에러를 검사해요.
타입 에러가 있으면 커밋이 차단되죠.

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/pre-commit-check.sh"
          }
        ]
      }
    ]
  }
}
```

```bash
#!/bin/bash
# .claude/hooks/pre-commit-check.sh
INPUT=$(cat)
COMMAND=$(echo "$INPUT" | jq -r '.tool_input.command // empty')

if [[ "$COMMAND" == git\ commit* ]]; then
  echo "타입 체크 중..."
  if ! pnpm exec tsc --noEmit; then
    echo "타입 에러가 있습니다. 커밋을 중단합니다."
    exit 2
  fi
  echo "타입 체크 통과"
fi

exit 0
```

---

### 레시피 4: 세션 시작 시 컨텍스트 자동 주입

세션이 시작되면 현재 브랜치, 최근 에러 등을 Claude에게 자동으로 전달해요.
"지금 어떤 브랜치야?" 같은 질문을 반복할 필요가 없어지죠.

```json
{
  "hooks": {
    "SessionStart": [
      {
        "matcher": "startup",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/inject-context.sh"
          }
        ]
      }
    ]
  }
}
```

```bash
#!/bin/bash
# .claude/hooks/inject-context.sh
BRANCH=$(git branch --show-current 2>/dev/null)
LAST_ERROR=$(cat /tmp/last-build-error.txt 2>/dev/null | tail -5)

# stdout으로 출력하면 Claude의 컨텍스트에 자동 추가됨
echo "=== 자동 주입 컨텍스트 ==="
echo "현재 브랜치: ${BRANCH:-알 수 없음}"
if [ -n "$LAST_ERROR" ]; then
  echo "최근 빌드 에러:"
  echo "$LAST_ERROR"
fi
echo "========================="
```

> SessionStart는 `startup`, `resume`, `clear`로 matcher를 필터링할 수 있어요. 새 세션 시작 시에만 실행하려면 `startup`을 쓰세요.

---

### 레시피 5: 작업 완료 시 자동 검증

Claude가 작업을 끝냈다고 선언할 때 테스트와 빌드를 돌려서 확인해요.

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/final-check.sh"
          }
        ]
      }
    ]
  }
}
```

```bash
#!/bin/bash
# .claude/hooks/final-check.sh
echo "작업 완료 검증 시작..."

ERRORS=""

if ! pnpm exec tsc --noEmit 2>/dev/null; then
  ERRORS="${ERRORS}타입 에러가 남아있습니다. "
fi

if ! pnpm test:run 2>/dev/null; then
  ERRORS="${ERRORS}실패한 테스트가 있습니다."
fi

if [ -n "$ERRORS" ]; then
  # JSON으로 차단 결정 반환
  echo "{\"decision\": \"block\", \"reason\": \"검증 실패: ${ERRORS}\"}"
  exit 0
fi

echo "모든 검증 통과"
```

> Stop 이벤트는 matcher를 지원하지 않아요. 항상 실행됩니다. `decision: "block"`을 반환하면 Claude가 작업을 중단하지 않고 계속 수정합니다.

---

### 레시피 6: Slack/Discord 알림

Claude가 알림을 보낼 때 외부 서비스로 전달해요. http 타입을 활용합니다.

```json
{
  "hooks": {
    "Notification": [
      {
        "hooks": [
          {
            "type": "http",
            "url": "https://hooks.slack.com/services/YOUR/WEBHOOK/URL",
            "headers": {
              "Content-Type": "application/json"
            }
          }
        ]
      }
    ]
  }
}
```

macOS 데스크톱 알림도 간단해요:

```json
{
  "hooks": {
    "Notification": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "osascript -e 'display notification \"Claude Code 알림\" with title \"Claude\"'"
          }
        ]
      }
    ]
  }
}
```

---

### 레시피 7: 안전한 명령 자동 허용

PermissionRequest 훅으로 안전한 명령을 자동 승인해요. 매번 Y를 누를 필요가 없어지죠.

```json
{
  "hooks": {
    "PermissionRequest": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/auto-approve.sh"
          }
        ]
      }
    ]
  }
}
```

```bash
#!/bin/bash
# .claude/hooks/auto-approve.sh
INPUT=$(cat)
COMMAND=$(echo "$INPUT" | jq -r '.tool_input.command // empty')

# 읽기 전용 명령은 자동 허용
SAFE_PATTERNS=("^git status" "^git log" "^git diff" "^pnpm test" "^pnpm lint" "^ls " "^cat ")

for pattern in "${SAFE_PATTERNS[@]}"; do
  if [[ "$COMMAND" =~ $pattern ]]; then
    echo '{"decision": "approve"}'
    exit 0
  fi
done

# 나머지는 기본 동작 (사용자에게 확인)
exit 0
```

---

### 레시피 8: 도구 실패 시 복구 힌트

도구가 실패했을 때 Claude에게 복구 방법을 알려줘요.

```json
{
  "hooks": {
    "PostToolUseFailure": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/recovery-hint.sh"
          }
        ]
      }
    ]
  }
}
```

```bash
#!/bin/bash
# .claude/hooks/recovery-hint.sh
INPUT=$(cat)
ERROR=$(echo "$INPUT" | jq -r '.error // empty')

if echo "$ERROR" | grep -q "ENOSPC"; then
  echo "디스크 공간 부족입니다. 'docker system prune'으로 정리해보세요."
elif echo "$ERROR" | grep -q "ECONNREFUSED"; then
  echo "연결 거부. 서버가 실행 중인지 확인하세요."
elif echo "$ERROR" | grep -q "MODULE_NOT_FOUND"; then
  echo "모듈을 찾을 수 없습니다. 'pnpm install'을 먼저 실행하세요."
fi
```

---

### 레시피 9: 작업 완료 품질 게이트

TaskCompleted 훅으로 lint/test를 통과해야만 작업 완료를 허용해요.

```json
{
  "hooks": {
    "TaskCompleted": [
      {
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/quality-gate.sh"
          }
        ]
      }
    ]
  }
}
```

```bash
#!/bin/bash
# .claude/hooks/quality-gate.sh

if ! pnpm lint 2>/dev/null; then
  echo '{"decision": "block", "reason": "lint 에러가 있습니다. 수정 후 다시 시도하세요."}'
  exit 0
fi

if ! pnpm test:run 2>/dev/null; then
  echo '{"decision": "block", "reason": "테스트 실패. 수정 후 다시 시도하세요."}'
  exit 0
fi

echo "품질 게이트 통과"
```

---

## Hook 입력/출력 형식

### 입력: stdin JSON

훅 스크립트는 **stdin으로 JSON**을 받아요. 이것이 표준 입력 방식입니다.

```bash
#!/bin/bash
INPUT=$(cat)

TOOL=$(echo "$INPUT" | jq -r '.tool_name // empty')
FILE=$(echo "$INPUT" | jq -r '.tool_input.file_path // empty')
COMMAND=$(echo "$INPUT" | jq -r '.tool_input.command // empty')
SESSION=$(echo "$INPUT" | jq -r '.session_id // empty')

echo "도구: $TOOL, 파일: $FILE"
```

환경 변수도 편의를 위해 제공되지만, stdin JSON이 더 정확하고 완전한 정보를 담고 있어요.

| 환경 변수 | 내용 | 비고 |
| --- | --- | --- |
| `CLAUDE_PROJECT_DIR` | 프로젝트 루트 경로 | 스크립트 경로 참조에 유용 |
| `SESSION_ID` | 현재 세션 ID | 로그 추적용 |

### 출력: JSON decision

일부 이벤트는 JSON으로 **차단 결정**을 반환할 수 있어요.

| 이벤트 | 차단 방식 |
| --- | --- |
| **PreToolUse** | `exit 2`로 차단 (JSON 불필요) |
| **UserPromptSubmit** | `{"decision": "block", "reason": "..."}` |
| **PostToolUse** | `{"decision": "block", "reason": "..."}` |
| **PostToolUseFailure** | `{"decision": "block", "reason": "..."}` |
| **Stop** | `{"decision": "block", "reason": "..."}` |
| **SubagentStop** | `{"decision": "block", "reason": "..."}` |
| **ConfigChange** | `{"decision": "block", "reason": "..."}` |
| **TaskCompleted** | `{"decision": "block", "reason": "..."}` |

!!! warning "PreToolUse만 exit 2"
    PreToolUse만 `exit 2`로 차단하고, 나머지는 JSON `decision` 필드를 사용합니다. 혼동하지 마세요!

---

## MCP 도구 매칭

MCP 도구를 매칭하려면 `mcp__` 접두사를 사용하세요.

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "mcp__.*",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'MCP 도구가 실행됩니다'"
          }
        ]
      }
    ]
  }
}
```

특정 MCP 서버만 매칭하려면

```json
{
  "matcher": "mcp__playwright__.*"
}
```

> MCP 도구 이름 형식은 `mcp__서버이름__도구이름`이에요. 정규식으로 서버 단위 또는 도구 단위로 필터링할 수 있어요.

---

## 비동기 훅

`async: true`를 설정하면 Claude가 훅 완료를 기다리지 않고 다음 작업을 진행해요.

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/run-tests-async.sh",
            "async": true,
            "timeout": 300
          }
        ]
      }
    ]
  }
}
```

비동기 훅의 결과는 다음 턴에 표시돼요. 테스트 실행처럼 오래 걸리는 작업에 유용합니다.

!!! warning "비동기 훅 제한"
    - **command 타입에서만** 사용 가능 (http, prompt, agent는 불가)
    - 차단(blocking) 결정을 반환할 수 없음
    - 긴 작업은 `timeout`을 명시적으로 설정하세요

---

## 디버깅 팁

### 훅이 안 돌아갈 때

```bash
# 스크립트 실행 권한 확인
chmod +x .claude/hooks/*.sh

# stdin JSON을 직접 넘겨서 동작 확인
echo '{"tool_name":"Edit","tool_input":{"file_path":"src/test.ts"}}' \
  | .claude/hooks/lint-on-save.sh

# 로그 남기기
echo "$(date): 훅 실행됨" >> /tmp/claude-hooks.log
```

### 훅 로그 추가

```bash
#!/bin/bash
LOG="/tmp/claude-hooks-$(date +%Y%m%d).log"
INPUT=$(cat)

TOOL=$(echo "$INPUT" | jq -r '.tool_name // empty')
FILE=$(echo "$INPUT" | jq -r '.tool_input.file_path // empty')

echo "[$(date +%H:%M:%S)] 도구: ${TOOL}, 파일: ${FILE}" >> "$LOG"

# 실제 로직 실행
# ...
```

### 자주 발생하는 실수

!!! warning "흔한 함정들"
    **exit 코드 혼동**: PreToolUse에서 `exit 1`은 에러 로그만 남기고, `exit 2`만 도구를 차단합니다. 다른 이벤트는 JSON `decision`을 사용해요.

    **무한 루프**: PostToolUse 훅에서 파일을 수정하면 → 다시 훅이 실행 → 또 수정... 반드시 **조건 체크**로 불필요한 재실행을 막으세요.

    **stdin 미사용**: 환경 변수에만 의존하면 정보가 부족할 수 있어요. stdin JSON을 우선 사용하세요.

| 실수 | 증상 | 해결 |
| --- | --- | --- |
| 실행 권한 없음 | 훅이 조용히 실패 | `chmod +x` 추가 |
| exit 코드 혼동 | PreToolUse에서 정상인데 차단됨 | `exit 0` (통과), `exit 2` (차단) |
| decision 혼동 | Stop에서 exit 2가 안 먹힘 | JSON `{"decision": "block"}` 사용 |
| 경로 문제 | 명령어를 못 찾음 | `$CLAUDE_PROJECT_DIR` 활용 |
| 무한 루프 | 훅이 파일 수정 → 다시 훅 실행 | 조건 체크 추가 |

---

## Hooks vs 스킬 vs CLAUDE.md

세 가지 모두 Claude의 동작을 제어하지만, 용도가 달라요.

| | **Hooks** | **스킬** | **CLAUDE.md** |
| --- | --- | --- | --- |
| **실행 방식** | 자동 (이벤트 기반) | 수동 또는 자동 | 항상 로드 |
| **주요 용도** | 품질 게이트, 보안, 외부 연동 | 반복 작업 자동화 | 컨텍스트 제공 |
| **타입** | command, http, prompt, agent | Markdown | Markdown |
| **예시** | 린팅, 파일 보호, Slack 알림 | PR 생성, 커밋 | 빌드 명령, 팀 규칙 |
| **차단 가능** | 가능 (exit 2 / decision) | 불가 | 불가 |

세 가지를 함께 쓰면 가장 강력합니다:

```text
CLAUDE.md  → 기본 규칙과 컨텍스트 (항상 인식)
스킬       → 자주 쓰는 작업 패턴 (필요할 때 호출)
Hooks      → 규칙 위반 자동 차단 (항상 감시)
             + 외부 서비스 연동 (http)
             + AI 기반 검증 (prompt/agent)
```

---

## 실천 체크리스트

- [ ] `.claude/settings.json`에 훅 구조 만들기
- [ ] PostToolUse 훅으로 자동 린팅 설정
- [ ] PreToolUse 훅으로 민감 파일 보호
- [ ] Stop 또는 TaskCompleted 훅으로 품질 게이트 설정
- [ ] Notification 훅으로 알림 연동 (macOS/Slack)
- [ ] 비동기 훅으로 테스트 자동 실행
- [ ] 훅 스크립트에 로그 추가해서 동작 확인

---

## 다음 단계

Hooks로 품질을 자동화했다면 [17. 흔한 실수 피하기](./17-anti-patterns.md)에서 자주 빠지는 함정을 미리 알아두세요.

---

**참고 자료**:

- [Claude Code Hooks 레퍼런스](https://code.claude.com/docs/en/hooks) - 전체 이벤트 목록과 옵션
- [Claude Code Hooks 가이드](https://code.claude.com/docs/en/hooks-guide) - 단계별 설정 방법
- [oh-my-claudecode Hooks 가이드](https://github.com/Yeachan-Heo/oh-my-claudecode) - OMC 훅 연동 방법
