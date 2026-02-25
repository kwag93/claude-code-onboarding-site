# 16. Hooks 실전 가이드

git hooks가 커밋을 지키듯, Claude hooks가 AI 작업을 지킵니다. 자동으로, 조용하게.

---

## Hooks란?

Claude Code의 **라이프사이클 이벤트**에 맞춰 실행되는 스크립트예요.

```
[도구 실행 전] → PreToolUse hook 실행
[도구 실행]
[도구 실행 후] → PostToolUse hook 실행

[사용자 입력] → UserPromptSubmit hook 실행
[작업 완료]   → Stop hook 실행
```

AI가 뭔가를 하려고 할 때마다, 또는 끝날 때마다 **여러분이 지정한 스크립트가 먼저/나중에 돌아갑니다.**

> "훅은 AI에게 규칙을 심는 장치입니다. 매번 말 안 해도, 항상 지켜집니다."

역할별로 정리하면:

| 역할 | 예시 |
| --- | --- |
| **품질 게이트** | 커밋 전 타입 체크 강제 |
| **자동 검증** | 파일 편집 후 린팅 자동 실행 |
| **컨텍스트 주입** | 프롬프트마다 브랜치 정보 자동 첨부 |
| **보안 가드** | `.env` 파일 수정 차단 |

---

## 4가지 Hook 이벤트

| 이벤트 | 실행 시점 | 주요 용도 |
| --- | --- | --- |
| **PreToolUse** | 도구 실행 직전 | 위험한 작업 차단, 사전 검증 |
| **PostToolUse** | 도구 실행 직후 | 자동 린팅, 포맷팅, 로깅 |
| **UserPromptSubmit** | 사용자 입력 시 | 컨텍스트 자동 주입 |
| **Stop** | 작업 완료 시 | 최종 검증, 정리 작업 |

PreToolUse는 차단도 가능해요. 스크립트가 exit 코드 2를 반환하면 Claude가 그 도구 실행을 중단합니다.

---

## 설정 위치

훅은 `settings.json`에 정의합니다.

| 범위 | 파일 위치 | 적용 대상 |
| --- | --- | --- |
| **프로젝트** | `.claude/settings.json` | 해당 프로젝트만 |
| **전역** | `~/.claude/settings.json` | 모든 프로젝트 |

기본 구조:

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

`matcher`는 도구 이름을 정규식으로 매칭합니다. `Edit|Write`는 Edit 또는 Write 도구가 실행될 때 동작합니다.

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

특정 파일만 린팅하고 싶다면, 훅 스크립트에서 `$TOOL_INPUT_FILE_PATH` 환경변수를 활용합니다.

```bash
#!/bin/bash
# .claude/hooks/lint-on-save.sh
FILE="${TOOL_INPUT_FILE_PATH}"
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

FILE="${TOOL_INPUT_FILE_PATH}"
PROTECTED=(".env" ".env.local" ".env.production" "credentials.json" "*.pem" "*.key")

for pattern in "${PROTECTED[@]}"; do
  if [[ "$FILE" == *"$pattern"* ]]; then
    echo "🚫 보호된 파일입니다: $FILE"
    echo "   이 파일은 Claude가 수정할 수 없습니다."
    exit 2  # exit 2 = Claude에게 도구 실행 중단 신호
  fi
done

exit 0
```

`exit 2`가 핵심이에요. 이 코드를 반환하면 Claude가 그 도구 실행을 취소합니다.

---

### 레시피 3: 커밋 전 타입 체크 강제

`git commit`을 실행하기 전에 TypeScript 타입 에러를 알아서 검사해요.
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

# Bash 도구의 실제 명령어를 확인
COMMAND="${TOOL_INPUT_COMMAND}"

if [[ "$COMMAND" == git\ commit* ]]; then
  echo "🔍 타입 체크 중..."
  if ! pnpm exec tsc --noEmit; then
    echo "❌ 타입 에러가 있습니다. 커밋을 중단합니다."
    exit 2
  fi
  echo "✅ 타입 체크 통과"
fi

exit 0
```

---

### 레시피 4: 사용자 입력 시 컨텍스트 자동 주입

매 프롬프트마다 현재 브랜치, 최근 에러 로그 등을 Claude에게 알아서 전달해요.
"지금 어떤 브랜치야?" 같은 질문을 반복할 필요가 없어지죠.

```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "matcher": "",
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

훅 스크립트의 stdout 출력이 Claude의 컨텍스트 창에 바로 추가돼요.

---

### 레시피 5: 작업 완료 시 자동 검증

Claude가 작업을 끝냈다고 선언할 때 테스트와 빌드를 돌려서 실제로 동작하는지 확인해요.

```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
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

echo "🏁 작업 완료 검증 시작..."

# 타입 체크
if ! pnpm exec tsc --noEmit 2>/dev/null; then
  echo "⚠️  타입 에러가 남아있습니다"
fi

# 테스트 실행
if ! pnpm test:run 2>/dev/null; then
  echo "⚠️  실패한 테스트가 있습니다"
else
  echo "✅ 모든 테스트 통과"
fi
```

---

## Hook 입력 형식

훅 스크립트는 환경 변수로 도구 정보를 받습니다.
필드명은 모두 **snake_case**예요.

| 환경 변수 | 내용 | 예시 |
| --- | --- | --- |
| `TOOL_NAME` | 실행된 도구 이름 | `Edit`, `Bash` |
| `TOOL_INPUT_COMMAND` | Bash 도구의 명령어 | `git commit -m "..."` |
| `TOOL_INPUT_FILE_PATH` | 편집 대상 파일 경로 | `/src/app.ts` |
| `SESSION_ID` | 현재 세션 ID | `sess_abc123` |
| `CWD` | 현재 작업 디렉토리 | `/Users/me/project` |

JSON으로 파싱해야 하는 경우 `jq`를 활용합니다:

```bash
#!/bin/bash
# stdin으로 JSON이 넘어오는 경우 (일부 훅)
INPUT=$(cat)

TOOL=$(echo "$INPUT" | jq -r '.tool_name')
FILE=$(echo "$INPUT" | jq -r '.tool_input.file_path // empty')

echo "도구: $TOOL, 파일: $FILE"
```

---

## 디버깅 팁

### 훅이 안 돌아갈 때

```bash
# 스크립트 실행 권한 확인
chmod +x .claude/hooks/*.sh

# 직접 실행해서 동작 확인
TOOL_INPUT_FILE_PATH="src/test.ts" .claude/hooks/lint-on-save.sh

# 로그 남기기
echo "$(date): 훅 실행됨" >> /tmp/claude-hooks.log
```

### 훅 로그 추가

```bash
#!/bin/bash
LOG="/tmp/claude-hooks-$(date +%Y%m%d).log"

echo "[$(date +%H:%M:%S)] 도구: ${TOOL_NAME}, 파일: ${TOOL_INPUT_FILE_PATH}" >> "$LOG"

# 실제 로직 실행
pnpm lint --fix "${TOOL_INPUT_FILE_PATH}"
```

### 자주 발생하는 실수

| 실수 | 증상 | 해결 |
| --- | --- | --- |
| 실행 권한 없음 | 훅이 조용히 실패 | `chmod +x` 추가 |
| exit 코드 혼동 | 정상인데 차단됨 | 성공은 `exit 0`, 차단은 `exit 2` |
| 경로 문제 | 명령어를 못 찾음 | 절대 경로 사용 또는 `PATH` 설정 |
| 무한 루프 | 훅이 파일을 수정 → 다시 훅 실행 | 조건 체크 추가 |

---

## Hooks vs 스킬 vs CLAUDE.md

세 가지 모두 Claude의 동작을 제어하지만, 용도가 다릅니다.

| | **Hooks** | **스킬** | **CLAUDE.md** |
| --- | --- | --- | --- |
| **실행 방식** | 자동 (이벤트 기반) | 수동 또는 자동 | 항상 로드 |
| **주요 용도** | 품질 게이트, 보안 | 반복 작업 자동화 | 컨텍스트 제공 |
| **언어** | 쉘 스크립트 | Markdown | Markdown |
| **예시** | 린팅 강제, 파일 보호 | PR 생성, 커밋 | 빌드 명령, 팀 규칙 |
| **차단 가능** | 가능 (exit 2) | 불가 | 불가 |

세 가지를 함께 쓰면 가장 강력합니다:

```
CLAUDE.md  → 기본 규칙과 컨텍스트 (항상 인식)
스킬       → 자주 쓰는 작업 패턴 (필요할 때 호출)
Hooks      → 규칙 위반 자동 차단 (항상 감시)
```

---

## 실천 체크리스트

- [ ] `.claude/settings.json` 파일 생성하고 훅 구조 만들기
- [ ] PostToolUse 훅으로 자동 린팅 설정
- [ ] 민감 파일 목록 정의하고 보호 훅 추가
- [ ] Stop 훅으로 작업 완료 후 자동 검증 설정
- [ ] 훅 스크립트에 로그 추가해서 동작 확인

---

## 다음 단계

Hooks로 품질을 자동화했다면 [17. 흔한 실수 피하기](./17-anti-patterns.md)에서 자주 빠지는 함정을 미리 알아두세요.

---

**참고 자료**:

- [Claude Code Hooks 공식 문서](https://docs.anthropic.com/ko/docs/claude-code/hooks) - 전체 이벤트 목록과 옵션
- [oh-my-claudecode Hooks 가이드](https://github.com/Yeachan-Heo/oh-my-claudecode) - OMC 훅 연동 방법
