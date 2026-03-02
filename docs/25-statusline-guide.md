# 25. Status Line: 나만의 대시보드 만들기

하단 바 하나로 모델, 컨텍스트, 비용, Git 상태를 실시간으로 볼 수 있어요.

> **Status Line**은 Claude Code 하단에 표시되는 커스터마이징 가능한 상태 바예요.
> 셸 스크립트 하나면 원하는 정보를 자유롭게 표시할 수 있습니다.
>
> 공식 문서: [code.claude.com/docs/en/statusline](https://code.claude.com/docs/en/statusline)

---

## Status Line이 뭔가요?

터미널의 PS1 프롬프트를 커스터마이징해본 적 있다면 같은 개념이에요. Claude Code 하단에 한 줄(또는 여러 줄)로 정보를 보여줘요.

```text
[Opus] 📁 my-project | 🌿 main | $0.42 | Context: 37%
```

이 한 줄이면 지금 쓰는 모델, 프로젝트 폴더, Git 브랜치, 세션 비용, 컨텍스트 사용량을 한눈에 파악할 수 있어요.

**이런 분에게 유용해요.**

- 컨텍스트 윈도우 사용량을 실시간으로 확인하고 싶다
- 세션 비용이 얼마나 나가는지 궁금하다
- 여러 세션을 오가며 작업하는데 지금 어디인지 헷갈린다
- Git 브랜치와 상태를 항상 눈에 두고 싶다

!!! tip "API 토큰 소모 없음"
    Status Line은 로컬에서 실행되는 셸 스크립트예요. API 토큰을 전혀 사용하지 않습니다.

---

## 30초 설정: /statusline 명령어

가장 빠른 방법이에요. Claude Code에서 자연어로 원하는 걸 말하면 됩니다.

```text
/statusline show model name and context percentage with a progress bar
```

Claude가 알아서

1. `~/.claude/` 폴더에 스크립트 파일 생성
2. `~/.claude/settings.json`에 설정 추가
3. 바로 적용

**한국어도 돼요:**

```text
/statusline 모델명, Git 브랜치, 컨텍스트 퍼센트를 프로그레스 바로 보여줘
```

!!! info "삭제하기"
    `/statusline delete` 또는 `/statusline clear`로 제거할 수 있어요.

---

## 수동 설정: settings.json

직접 설정하고 싶다면 `~/.claude/settings.json`에 `statusLine` 필드를 추가하세요.

### 스크립트 파일 방식

```json
{
  "statusLine": {
    "type": "command",
    "command": "~/.claude/statusline.sh",
    "padding": 2
  }
}
```

### 인라인 명령어 방식

간단한 내용이라면 스크립트 파일 없이 바로 `jq` 명령으로도 돼요.

```json
{
  "statusLine": {
    "type": "command",
    "command": "jq -r '\"[\\(.model.display_name)] \\(.context_window.used_percentage // 0)% context\"'"
  }
}
```

| 필드 | 설명 | 기본값 |
|------|------|--------|
| `type` | 항상 `"command"` | 필수 |
| `command` | 스크립트 경로 또는 인라인 셸 명령 | 필수 |
| `padding` | 좌우 여백 (문자 수) | `0` |

---

## 스크립트 작성: 단계별 가이드

내부적으로 어떻게 동작하는지 이해하면서 직접 만들어봐요.

### Step 1: 스크립트 파일 생성

Claude Code가 JSON 데이터를 stdin으로 보내줘요. `jq`로 필요한 필드를 추출하면 됩니다.

`~/.claude/statusline.sh`:

```bash
#!/bin/bash
# Claude Code가 stdin으로 보내는 JSON 데이터 읽기
input=$(cat)

# jq로 필요한 필드 추출
MODEL=$(echo "$input" | jq -r '.model.display_name')
DIR=$(echo "$input" | jq -r '.workspace.current_dir')
# "// 0"은 null일 때 기본값
PCT=$(echo "$input" | jq -r '.context_window.used_percentage // 0' | cut -d. -f1)

# 출력 - ${DIR##*/}는 폴더명만 추출
echo "[$MODEL] 📁 ${DIR##*/} | ${PCT}% context"
```

### Step 2: 실행 권한 부여

```bash
chmod +x ~/.claude/statusline.sh
```

### Step 3: settings.json에 등록

```json
{
  "statusLine": {
    "type": "command",
    "command": "~/.claude/statusline.sh"
  }
}
```

설정은 자동으로 리로드돼요. 하지만 스크립트 내용을 바꾸면 다음 Claude 응답 이후에 반영됩니다.

---

## 동작 원리

```text
Claude Code → JSON 데이터 (stdin) → 스크립트 실행 → stdout 출력 → 하단 바 표시
```

**언제 업데이트되나요?**

- 새 어시스턴트 메시지가 올 때
- 권한 모드가 바뀔 때
- Vim 모드가 토글될 때
- 300ms 디바운싱 (빠른 변화는 묶어서 한 번만 실행)

**출력 가능한 것**

| 기능 | 설명 |
|------|------|
| **멀티라인** | `echo`를 여러 번 쓰면 여러 줄 표시 |
| **ANSI 컬러** | `\033[32m` 같은 이스케이프 코드로 색상 적용 |
| **클릭 가능한 링크** | OSC 8 시퀀스로 하이퍼링크 생성 (iTerm2, Kitty, WezTerm) |

---

## 사용 가능한 데이터

스크립트가 stdin으로 받는 JSON 필드 전체 목록이에요. 필요한 것만 골라 쓰면 돼요.

### 모델 정보

| 필드 | 설명 | 예시 |
|------|------|------|
| `model.id` | 모델 식별자 | `claude-opus-4-6` |
| `model.display_name` | 표시 이름 | `Opus` |

### 작업 공간

| 필드 | 설명 |
|------|------|
| `workspace.current_dir` | 현재 작업 디렉토리 |
| `workspace.project_dir` | Claude Code를 실행한 원래 디렉토리 |

### 비용 & 시간

| 필드 | 설명 |
|------|------|
| `cost.total_cost_usd` | 세션 총 비용 (USD) |
| `cost.total_duration_ms` | 세션 경과 시간 (ms) |
| `cost.total_api_duration_ms` | API 응답 대기 시간 (ms) |
| `cost.total_lines_added` | 추가된 코드 줄 수 |
| `cost.total_lines_removed` | 삭제된 코드 줄 수 |

### 컨텍스트 윈도우

| 필드 | 설명 |
|------|------|
| `context_window.context_window_size` | 최대 크기 (기본 200,000, 확장 1,000,000) |
| `context_window.used_percentage` | 사용 비율 (%) |
| `context_window.remaining_percentage` | 남은 비율 (%) |
| `context_window.total_input_tokens` | 누적 입력 토큰 |
| `context_window.total_output_tokens` | 누적 출력 토큰 |

### 기타

| 필드 | 설명 |
|------|------|
| `session_id` | 고유 세션 ID |
| `version` | Claude Code 버전 |
| `vim.mode` | Vim 모드 상태 (`NORMAL` / `INSERT`, 활성화 시) |
| `agent.name` | 에이전트 이름 (`--agent` 사용 시) |
| `exceeds_200k_tokens` | 200k 토큰 초과 여부 |

??? note "전체 JSON 스키마 보기"
    ```json
    {
      "cwd": "/current/working/directory",
      "session_id": "abc123...",
      "transcript_path": "/path/to/transcript.jsonl",
      "model": {
        "id": "claude-opus-4-6",
        "display_name": "Opus"
      },
      "workspace": {
        "current_dir": "/current/working/directory",
        "project_dir": "/original/project/directory"
      },
      "version": "1.0.80",
      "cost": {
        "total_cost_usd": 0.01234,
        "total_duration_ms": 45000,
        "total_api_duration_ms": 2300,
        "total_lines_added": 156,
        "total_lines_removed": 23
      },
      "context_window": {
        "total_input_tokens": 15234,
        "total_output_tokens": 4521,
        "context_window_size": 200000,
        "used_percentage": 8,
        "remaining_percentage": 92,
        "current_usage": {
          "input_tokens": 8500,
          "output_tokens": 1200,
          "cache_creation_input_tokens": 5000,
          "cache_read_input_tokens": 2000
        }
      },
      "exceeds_200k_tokens": false,
      "vim": { "mode": "NORMAL" },
      "agent": { "name": "security-reviewer" }
    }
    ```

    **없을 수 있는 필드:** `vim` (Vim 모드 비활성화 시), `agent` (`--agent` 미사용 시)

    **null일 수 있는 필드:** `context_window.current_usage` (첫 API 호출 전), `used_percentage` (세션 초반)

---

## 실전 예제

모든 예제는 같은 방식으로 적용해요.

1. 스크립트를 `~/.claude/statusline.sh`에 저장
2. `chmod +x ~/.claude/statusline.sh`로 실행 권한 부여
3. settings.json에 경로 등록

### 예제 1: 컨텍스트 프로그레스 바

가장 기본이 되는 예제예요. 컨텍스트를 얼마나 썼는지 바로 보여줘요.

```text
[Opus] ▓▓▓░░░░░░░ 25%
```

=== "Bash"

    ```bash
    #!/bin/bash
    input=$(cat)

    MODEL=$(echo "$input" | jq -r '.model.display_name')
    PCT=$(echo "$input" | jq -r '.context_window.used_percentage // 0' | cut -d. -f1)

    # 프로그레스 바 생성
    BAR_WIDTH=10
    FILLED=$((PCT * BAR_WIDTH / 100))
    EMPTY=$((BAR_WIDTH - FILLED))
    BAR=""
    [ "$FILLED" -gt 0 ] && BAR=$(printf "%${FILLED}s" | tr ' ' '▓')
    [ "$EMPTY" -gt 0 ] && BAR="${BAR}$(printf "%${EMPTY}s" | tr ' ' '░')"

    echo "[$MODEL] $BAR $PCT%"
    ```

=== "Python"

    ```python
    #!/usr/bin/env python3
    import json, sys

    data = json.load(sys.stdin)
    model = data['model']['display_name']
    pct = int(data.get('context_window', {}).get('used_percentage', 0) or 0)

    filled = pct * 10 // 100
    bar = '▓' * filled + '░' * (10 - filled)

    print(f"[{model}] {bar} {pct}%")
    ```

### 예제 2: Git 상태 + ANSI 컬러

스테이징된 파일은 초록색, 수정된 파일은 노란색으로 표시해요.

```text
[Opus] 📁 my-project | 🌿 main +2 ~3
```

```bash
#!/bin/bash
input=$(cat)

MODEL=$(echo "$input" | jq -r '.model.display_name')
DIR=$(echo "$input" | jq -r '.workspace.current_dir')

GREEN='\033[32m'
YELLOW='\033[33m'
RESET='\033[0m'

if git rev-parse --git-dir > /dev/null 2>&1; then
    BRANCH=$(git branch --show-current 2>/dev/null)
    STAGED=$(git diff --cached --numstat 2>/dev/null | wc -l | tr -d ' ')
    MODIFIED=$(git diff --numstat 2>/dev/null | wc -l | tr -d ' ')

    GIT_STATUS=""
    [ "$STAGED" -gt 0 ] && GIT_STATUS="${GREEN}+${STAGED}${RESET}"
    [ "$MODIFIED" -gt 0 ] && GIT_STATUS="${GIT_STATUS}${YELLOW}~${MODIFIED}${RESET}"

    echo -e "[$MODEL] 📁 ${DIR##*/} | 🌿 $BRANCH $GIT_STATUS"
else
    echo "[$MODEL] 📁 ${DIR##*/}"
fi
```

### 예제 3: 비용 & 시간 추적

세션 비용과 경과 시간을 바로 확인할 수 있어요.

```text
[Opus] 💰 $0.42 | ⏱️ 5m 23s
```

```bash
#!/bin/bash
input=$(cat)

MODEL=$(echo "$input" | jq -r '.model.display_name')
COST=$(echo "$input" | jq -r '.cost.total_cost_usd // 0')
DURATION_MS=$(echo "$input" | jq -r '.cost.total_duration_ms // 0')

COST_FMT=$(printf '$%.2f' "$COST")
DURATION_SEC=$((DURATION_MS / 1000))
MINS=$((DURATION_SEC / 60))
SECS=$((DURATION_SEC % 60))

echo "[$MODEL] 💰 $COST_FMT | ⏱️ ${MINS}m ${SECS}s"
```

### 예제 4: 멀티라인 대시보드 (추천)

`echo`를 여러 번 쓰면 여러 줄로 표시돼요. 정보가 제일 많은 구성이에요.

```text
[Opus] 📁 my-project | 🌿 main
████░░░░░░ 37% | $0.42 | ⏱️ 5m 23s
```

```bash
#!/bin/bash
input=$(cat)

MODEL=$(echo "$input" | jq -r '.model.display_name')
DIR=$(echo "$input" | jq -r '.workspace.current_dir')
COST=$(echo "$input" | jq -r '.cost.total_cost_usd // 0')
PCT=$(echo "$input" | jq -r '.context_window.used_percentage // 0' | cut -d. -f1)
DURATION_MS=$(echo "$input" | jq -r '.cost.total_duration_ms // 0')

CYAN='\033[36m'; GREEN='\033[32m'; YELLOW='\033[33m'; RED='\033[31m'; RESET='\033[0m'

# 사용량에 따라 색상 변경
if [ "$PCT" -ge 90 ]; then BAR_COLOR="$RED"
elif [ "$PCT" -ge 70 ]; then BAR_COLOR="$YELLOW"
else BAR_COLOR="$GREEN"; fi

FILLED=$((PCT / 10)); EMPTY=$((10 - FILLED))
BAR=$(printf "%${FILLED}s" | tr ' ' '█')$(printf "%${EMPTY}s" | tr ' ' '░')

MINS=$((DURATION_MS / 60000)); SECS=$(((DURATION_MS % 60000) / 1000))

BRANCH=""
git rev-parse --git-dir > /dev/null 2>&1 && BRANCH=" | 🌿 $(git branch --show-current 2>/dev/null)"

# 첫째 줄: 모델 + 디렉토리 + Git
echo -e "${CYAN}[$MODEL]${RESET} 📁 ${DIR##*/}$BRANCH"
# 둘째 줄: 컨텍스트 바 + 비용 + 시간
COST_FMT=$(printf '$%.2f' "$COST")
echo -e "${BAR_COLOR}${BAR}${RESET} ${PCT}% | ${YELLOW}${COST_FMT}${RESET} | ⏱️ ${MINS}m ${SECS}s"
```

!!! tip "색상 임계값"
    70% 미만은 초록, 70~89%는 노란색, 90% 이상은 빨간색이에요. 빨간색이 보이면 `/compact` 또는 `/clear`를 고려하세요.

### 예제 5: 클릭 가능한 GitHub 링크

Git remote URL을 클릭 가능한 하이퍼링크로 만들어요. Cmd+클릭(macOS) 또는 Ctrl+클릭으로 브라우저에서 열 수 있어요.

```bash
#!/bin/bash
input=$(cat)

MODEL=$(echo "$input" | jq -r '.model.display_name')

# SSH URL을 HTTPS로 변환
REMOTE=$(git remote get-url origin 2>/dev/null | sed 's/git@github.com:/https:\/\/github.com\//' | sed 's/\.git$//')

if [ -n "$REMOTE" ]; then
    REPO_NAME=$(basename "$REMOTE")
    # OSC 8 하이퍼링크 형식
    printf '%b' "[$MODEL] 🔗 \e]8;;${REMOTE}\a${REPO_NAME}\e]8;;\a\n"
else
    echo "[$MODEL]"
fi
```

!!! warning "터미널 호환성"
    OSC 8 링크는 iTerm2, Kitty, WezTerm에서 작동해요. 기본 Terminal.app에서는 안 됩니다.

### 예제 6: 캐싱으로 성능 최적화

큰 레포에서 `git diff`가 느릴 수 있어요. 5초마다 한 번만 갱신하는 캐시를 추가하세요.

```bash
#!/bin/bash
input=$(cat)

MODEL=$(echo "$input" | jq -r '.model.display_name')
DIR=$(echo "$input" | jq -r '.workspace.current_dir')

# 고정된 캐시 파일명 사용 (PID 사용 금지 - 매번 새 프로세스라 캐시가 안 됨)
CACHE_FILE="/tmp/statusline-git-cache"
CACHE_MAX_AGE=5  # 초

cache_is_stale() {
    [ ! -f "$CACHE_FILE" ] || \
    [ $(($(date +%s) - $(stat -f %m "$CACHE_FILE" 2>/dev/null || stat -c %Y "$CACHE_FILE" 2>/dev/null || echo 0))) -gt $CACHE_MAX_AGE ]
}

if cache_is_stale; then
    if git rev-parse --git-dir > /dev/null 2>&1; then
        BRANCH=$(git branch --show-current 2>/dev/null)
        STAGED=$(git diff --cached --numstat 2>/dev/null | wc -l | tr -d ' ')
        MODIFIED=$(git diff --numstat 2>/dev/null | wc -l | tr -d ' ')
        echo "$BRANCH|$STAGED|$MODIFIED" > "$CACHE_FILE"
    else
        echo "||" > "$CACHE_FILE"
    fi
fi

IFS='|' read -r BRANCH STAGED MODIFIED < "$CACHE_FILE"

if [ -n "$BRANCH" ]; then
    echo "[$MODEL] 📁 ${DIR##*/} | 🌿 $BRANCH +$STAGED ~$MODIFIED"
else
    echo "[$MODEL] 📁 ${DIR##*/}"
fi
```

!!! warning "캐시 파일명 주의"
    `$$`나 `os.getpid()`를 캐시 파일명에 쓰지 마세요. Status Line은 매번 새 프로세스로 실행되기 때문에 PID가 매번 달라져서 캐시가 작동하지 않아요.

---

## 테스트하기

스크립트를 적용하기 전에 가짜 데이터로 테스트할 수 있어요.

```bash
echo '{"model":{"display_name":"Opus"},"context_window":{"used_percentage":25},"workspace":{"current_dir":"/home/user/project"},"cost":{"total_cost_usd":0.42,"total_duration_ms":300000}}' | ~/.claude/statusline.sh
```

---

## 트러블슈팅

### Status Line이 안 보여요

- 스크립트에 실행 권한이 있는지 확인: `chmod +x ~/.claude/statusline.sh`
- stdout으로 출력하는지 확인 (stderr로 보내면 안 돼요)
- `disableAllHooks`가 `true`이면 Status Line도 비활성화돼요

### `--` 또는 빈 값이 표시돼요

- 첫 API 응답 전에는 일부 필드가 `null`이에요
- `jq`에서 `// 0` 같은 기본값 처리를 추가하세요
- 여러 메시지 후에도 비어 있으면 Claude Code를 재시작해보세요

### 컨텍스트 퍼센트가 이상해요

- `used_percentage`를 쓰세요 (정확한 현재 상태 반영)
- `total_input_tokens`는 누적값이라 컨텍스트 크기를 초과할 수 있어요
- `/context`와 약간 다를 수 있는데, 계산 시점 차이 때문이에요

### 이스케이프 시퀀스가 깨져요

- `echo -e` 대신 `printf '%b'`를 쓰면 더 안정적이에요
- 멀티라인 + ANSI 색상 조합에서 간헐적 깨짐이 있을 수 있어요
- 심하면 일단 단순 텍스트로 줄여보세요

---

## 커뮤니티 프로젝트

직접 만들기 귀찮다면 커뮤니티 프로젝트를 써보세요.

| 프로젝트 | 설명 |
|----------|------|
| [ccstatusline](https://github.com/sirmalloc/ccstatusline) | 테마 지원, 다양한 프리셋 |
| [starship-claude](https://github.com/martinemde/starship-claude) | Starship 스타일 Status Line |

---

## 다음 단계

- [26. 키보드 단축키 커스터마이징](./26-keybindings-guide.md) — 단축키도 내 맘대로
- [16. Hooks 실전 가이드](./16-hooks-practical.md) — 이벤트 기반 자동화
- [13. 파워 유저 70가지 팁](./13-power-user-tips.md) — 더 많은 생산성 팁
