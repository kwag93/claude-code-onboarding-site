# 13. 파워 유저 가이드: 해커톤 우승자의 70가지 팁

Anthropic 해커톤 우승자 [ykdojo](https://github.com/ykdojo)와 Anthropic DevRel [Ado Kukic](https://adocomplete.com/advent-of-claude-2025/)의 실전 노하우를 모았습니다.

> **ykdojo**는 10억 토큰 넘게 쓰면서 Claude Code를 극한까지 파본 사람이에요.
> 시스템 프롬프트를 19k에서 10k 토큰으로 절반 가까이 줄이고, 음성 코딩 시스템을 직접 만들고, 컨테이너에서 멀티 에이전트 오케스트레이션까지 돌렸습니다.
>
> 원본 저장소: [github.com/ykdojo/claude-code-tips](https://github.com/ykdojo/claude-code-tips)

---

## 이 가이드의 구성

온보딩 단계에 따라 4단계로 나눴어요.

| 단계 | 시기 | 핵심 |
|------|------|------|
| **Phase 1** | 첫 주 | 환경 세팅, 필수 명령어 |
| **Phase 2** | 1-4주 | 생산성 부스트, 컨텍스트 관리 |
| **Phase 3** | 1-3개월 | Git 워크플로우, 고급 기능 |
| **Phase 4** | 3개월+ | 시스템 최적화, 자동화의 자동화 |

**자기 단계에 맞는 것부터 시작하세요.** 전부 읽을 필요 없어요.

---

## 이 가이드의 다른 문서와 함께 보기

파워 유저 팁은 다른 문서의 내용과 연결될 때 더 효과적이에요.

| 팁 주제 | 연결 문서 | 핵심 |
|---------|-----------|------|
| CLAUDE.md 작성 | [15. CLAUDE.md 마스터하기](./15-claude-md-mastery.md) | 60줄 원칙, 3가지 계층 |
| Hooks 설정 | [16. Hooks 실전 가이드](./16-hooks-practical.md) | 레시피 5개, exit 코드 |
| 팀 세션 활용 | [06. 팀 세션 가이드](./06-agent-teams-cowork.md) | 병렬 작업, 토큰 비용 |
| /init 활용 | [14. 프로젝트 초기화](./14-init-guide.md) | /init vs deep-init |
| 안티패턴 | [17. 흔한 실수 피하기](./17-anti-patterns.md) | 8가지 함정 |
| MCP 연동 | [10. MCP 생태계](./10-mcp-ecosystem.md) | Context7, Figma, Stitch |

> **추천**: Phase 1~2를 마친 후 위 문서들을 순서대로 읽으면 시너지가 납니다.

---

## Phase 1: 온보딩 첫 주

### 1. CLAUDE.md부터 만들기

`/init` 한 줄이면 됩니다.

```
/init
```

Claude가 코드베이스를 분석해서 CLAUDE.md 초안을 만들어줘요. 기술 스택, 코딩 스타일, 금지 사항이 자동으로 들어갑니다.

!!! tip "ykdojo의 조언"
    "처음에는 CLAUDE.md 없이 시작하세요. 같은 말을 반복하게 되면 그때 추가하세요. 과도한 정보는 컨텍스트를 낭비합니다."

좋은 CLAUDE.md는 **간결해요**.

```markdown
## Authentication
- NextAuth.js with Credentials provider
- JWT session strategy
- **DO NOT**: Bypass auth checks, expose session secrets
```

장황한 설명 대신 핵심만 넣으세요.

### 2. 필수 슬래시 명령어 5개

매일 쓰게 될 명령어예요.

| 명령어 | 하는 일 | 언제 쓰나 |
|--------|---------|-----------|
| `/usage` | 토큰 사용량, 리셋 시간 표시 | 세션 시작할 때 |
| `/clear` | 대화 초기화, 새 컨텍스트 | 작업 전환할 때 |
| `/context` | 컨텍스트 사용 현황 X-Ray | 성능 느려질 때 |
| `/stats` | 활동 그래프, 사용 패턴 | 주간 회고할 때 |
| `/init` | CLAUDE.md 자동 생성 | 새 프로젝트 시작할 때 |

### 3. `!` Prefix로 즉시 실행

`!`를 붙이면 Claude의 처리 없이 셸 명령을 바로 실행해요. 토큰 낭비를 막아줍니다.

```bash
# 느린 방법 (토큰 낭비)
> "git status 실행해줘"

# 빠른 방법
> !git status
```

Ado는 이걸 가장 자주 쓴다고 해요. `!git diff`, `!pnpm test`, `!docker ps` 같은 상태 확인에 딱이에요.

### 4. 터미널 별칭 설정

`~/.zshrc`에 추가하세요.

```bash
# Claude Code 별칭
alias c='claude'
alias cc='claude --continue'    # 마지막 대화 이어가기
alias cr='claude --resume'      # 대화 목록에서 선택
```

하루에 수십 번 실행하는 명령어라면 별칭을 만드세요. 타이핑 시간이 쌓이면 꽤 큰 차이거든요.

### 4-1. 모델 선택 전략

`/model`로 상황에 맞는 모델을 고르세요.

| 모델 | 용도 | 특징 |
|------|------|------|
| **Opus** | 설계, 디버깅, 리팩토링 | 가장 강력 |
| **Opus [1m]** | 수백 파일 프로젝트 분석 | 100만 토큰 컨텍스트 |
| **Sonnet** | 일상 코딩, 기능 추가 | 균형 잡힌 성능 |
| **Haiku** | 간단한 질문, 빠른 수정 | 가장 빠름 |
| **opusplan** | Plan은 Opus, 실행은 Sonnet | **추천**: 자동 분배 |

**Effort Level 조절**: 모델 선택기에서 `←` `→` 화살표로 조절해요.

- **low**: 빠르고 저렴. 단순 작업에.
- **high**: 깊은 추론. 복잡한 문제에.

`/fast`를 켜면 출력 속도가 **2.5배** 빨라져요. 단, 비용이 6배 증가하니 급할 때만 쓰세요.

### 5. 키보드 단축키 4개

마우스 없이 작업하는 게 목표예요.

| 단축키 | 기능 | 설명 |
|--------|------|------|
| `Esc Esc` | 되감기 | 잘못된 변경 즉시 되돌리기 |
| `Ctrl+R` | 역방향 검색 | 이전 프롬프트 재사용 |
| `Shift+Tab` ×2 | Plan 모드 | 실행 전에 계획 먼저 세우기 |
| `Tab` / `Enter` | 제안 수락 | Claude의 다음 작업 제안 수락 |

!!! warning "Esc Esc는 생명줄"
    Claude가 잘못된 코드를 작성했거나, 원치 않는 파일을 삭제했을 때 `Esc`를 두 번 빠르게 누르세요. `git reset --hard`보다 빠르고 안전해요.

---

## Phase 2: 생산성 부스트 (1-4주)

### 6. 큰 문제를 쪼개라

ykdojo가 가장 강조하는 원칙이에요.

```
# ❌ 막연한 요청
> "로그인 페이지 만들어줘"

# ✅ 단계별 요청
> 1. "users 테이블 스키마 설계해줘. 필드: username, email, password_hash"
> 2. "Drizzle ORM 마이그레이션 파일 생성해줘"
> 3. "React + Tailwind로 로그인 폼 UI 만들어줘"
> 4. "/api/auth/login으로 POST 요청 보내는 로직 작성해줘"
> 5. "로그인 실패 시나리오 테스트 코드 작성해줘"
```

```
직접 경로 (실패 확률 높음):   A ──────────→ B
단계적 경로 (성공 확률 높음): A → A1 → A2 → A3 → B
```

각 단계에서 검증하고 문제가 있으면 그 단계만 고치면 돼요.

### 7. 컨텍스트는 우유다

> "신선하고 압축된 상태를 유지하는 것이 핵심입니다. 오래된 우유는 상하듯이, 오래된 컨텍스트는 AI의 성능을 저하시킵니다." — ykdojo

여러 주제를 한 대화에서 섞으면 성능이 **39%까지 저하**될 수 있어요.

**규칙 1: 하나의 대화 = 하나의 목적**

로그인 기능 구현하다가 "아, 그리고 대시보드 UI도 개선해줘"라고 하면 컨텍스트가 오염돼요. 새 터미널 탭을 여세요.

**규칙 2: HANDOFF.md로 컨텍스트 인계**

대화가 길어지면 다음 에이전트를 위한 인수인계 문서를 만드세요.

```
> "지금까지의 작업 내용을 HANDOFF.md로 정리해줘.
> 시도한 것, 성공한 것, 실패한 것, 다음 단계를 명확히 작성해줘."
```

그다음

```
> /clear
> "@HANDOFF.md 읽고 작업 이어가줘"
```

HANDOFF.md 예시:

```markdown
# Stripe 결제 통합 - Handoff

## 완료된 작업
✅ Stripe SDK 설치 및 API 키 설정
✅ /api/create-checkout-session 엔드포인트 구현

## 실패한 것
❌ Webhook 서명 검증 - 로컬에서 테스트 불가 → ngrok 필요

## 다음 단계
1. ngrok으로 로컬 서버 노출
2. Stripe 대시보드에서 Webhook URL 등록
3. payment_intent.succeeded 이벤트 핸들러 구현
```

**규칙 3: `/context`로 상태 확인**

```
Context Usage: 87,432 / 200,000 tokens (43.7%)
- System Prompt: 10,234 tokens (11.7%)
- MCP Servers: 15,678 tokens (18.0%)
- Conversation History: 57,941 tokens (66.3%)
```

MCP가 많은 공간을 차지하면 `/mcp`로 안 쓰는 것을 비활성화하세요.

!!! tip "최적 수준"
    10개 미만의 MCP 서버, 80개 미만의 활성 도구를 유지하세요.

### 8. 계획 모드 vs 욜로 모드

**Plan 모드** (`Shift+Tab` ×2):

Claude가 코드를 읽고 분석하되, 승인 전까지 아무것도 수정 안 해요. "두 번 생각하고, 한 번 실행하라."

쓸 때

- 처음 해보는 복잡한 작업
- 여러 파일에 걸친 리팩토링
- 실수하면 복구 비용이 큰 작업

**YOLO 모드** (`--dangerously-skip-permissions`):

모든 작업을 자동 승인해요. 이름에 "dangerously"가 들어간 이유가 있겠죠.

사용할 때

- 컨테이너 안에서만
- 간단하고 명확한 작업
- 실험적인 프로토타입

!!! danger "경고"
    YOLO 모드를 호스트 시스템에서 직접 사용하지 마세요. ykdojo는 반드시 컨테이너 안에서 사용할 것을 권장해요.

> "Ado: 저는 90%의 시간 동안 계획 모드를 기본으로 사용합니다."

### 9. 세션 관리 기술

**이어가기:**

```bash
claude --continue    # 마지막 세션
claude --resume      # 세션 목록에서 선택
```

**이름 붙이기:**

```
> /rename auth-system-refactor
```

나중에 이름으로 바로 돌아갈 수 있어요.

```bash
claude --resume auth-system-refactor
```

"2025-01-15 14:30"보다 "stripe-integration"이 훨씬 기억하기 쉽겠죠.

**대화 기록 검색:**

과거 대화에서 유용한 정보를 찾아야 할 때가 있어요.

```bash
# 대화 목록 확인
$ claude -r
# 출력:
1. [2025-01-15 14:30] stripe-integration
2. [2025-01-14 09:15] auth-system-refactor
3. [2025-01-13 16:45] database-migration
...
```

대화 내용까지 검색하고 싶다면 `grep`을 활용하세요.

```bash
# 모든 대화 파일에서 "Stripe" 검색
$ grep -r "Stripe" ~/.claude/conversations/

# 결과:
~/.claude/conversations/abc123.json: "Stripe API 키를 환경 변수로 설정..."
~/.claude/conversations/def456.json: "Stripe Webhook 서명 검증..."
```

"그때 Stripe 연동 어떻게 했더라?" 싶을 때, 과거 대화를 grep으로 바로 찾을 수 있어요.

**PR에서 세션 재개:**

```bash
# PR을 만들면 세션이 자동 연결됨
gh pr create

# 나중에 그 PR 컨텍스트로 바로 재개
claude --from-pr 123
```

**세션 이동 (인터페이스 간):**

| 명령어 | 방향 | 설명 |
|--------|------|------|
| `/teleport` | 터미널 → 웹 | 웹 브라우저에서 이어서 |
| `/desktop` | CLI → 데스크톱 앱 | 데스크톱 앱에서 이어서 |

모바일에서 코드 리뷰를 보거나, 이동 중에 작업을 확인할 때 유용해요.

**내보내기:**

```
> /export
```

마크다운 파일로 내보내서 팀과 공유하거나 문서화에 활용하세요.

### 10. 작성-테스트 사이클

코드만 작성하면 안 돼요. 테스트까지 한 사이클이에요.

```
> "사용자 인증 미들웨어를 작성하고, 테스트도 함께 작성해줘.
> 테스트를 실행해서 모두 통과하는지 확인해줘."
```

Claude가 알아서

1. 코드 작성
2. 테스트 작성
3. 테스트 실행
4. 실패하면 수정
5. 통과할 때까지 반복

### 11. 음성으로 코딩하기

타이핑은 분당 40단어, 말은 분당 150단어. **3.75배** 차이예요.

!!! tip "공식 보이스 모드 출시"
    2026년 3월, Claude Code에 **공식 보이스 모드**가 추가되었습니다. `/voice`로 바로 시작하세요. 자세한 내용은 [보이스 모드 가이드](./27-voice-mode.md)를 참고하세요.

```text
/voice
```

생각이 정리가 안 되거나, 복잡한 요구사항을 설명할 때 말로 하면 훨씬 명확해져요.

```text
[음성] "Claude, JWT 토큰을 검증하고 유효하지 않으면
401 에러를 반환하는 인증 미들웨어를 만들어줘.
Express.js, TypeScript로 작성해줘."
```

발음이 좀 틀려도 Claude가 맥락으로 알아들으니까 완벽할 필요 없어요. 익숙해지면 오히려 타자보다 훨씬 빠릅니다.

---

## Phase 3: 워크플로우 마스터 (1-3개월)

### 12. Git + GitHub CLI 자동화

**자동 커밋:**

```
> "변경 사항을 분석하고 적절한 커밋 메시지를 작성한 후 커밋해줘"
```

**Draft PR 자동 생성:**

```
> "현재 브랜치의 변경 사항으로 draft PR을 만들어줘.
> 제목은 변경 내용을 요약하고, 본문에는 주요 변경 사항을 리스트로 작성해줘."
```

### 13. Git Worktree로 병렬 작업

브랜치 전환 없이 여러 작업을 동시에 할 수 있어요.

```
~/projects/myapp               → main 브랜치 (유지)
~/projects/myapp-feature-auth  → feature/auth 브랜치 (새 기능)
~/projects/myapp-hotfix        → hotfix/critical-bug (긴급 수정)
```

```
> "git worktree를 사용해서 feature/auth 브랜치를
> ../myapp-feature-auth에 체크아웃해줘"
```

각 worktree에서 별도 터미널 탭으로 Claude를 실행하면, `node_modules` 재설치나 빌드 재실행 없이 병렬 작업이 가능해요.

### 14. 대화형 PR 리뷰

Claude는 훌륭한 코드 리뷰어예요.

```
> "gh pr checkout 123 실행하고 이 PR의 변경 사항을 요약해줘"
> "보안 이슈나 성능 문제가 있는지 확인해줘"
> "네가 제안한 개선 사항을 적용하고 테스트를 실행해줘"
```

ykdojo의 실제 사례: 한 PR에서 Claude가 토큰 만료 시간의 타임존 버그, N+1 쿼리 문제, 에러 핸들링 누락을 발견하고 수정과 테스트까지 완료했어요.

### 15. 승인된 명령어 감사

위험한 명령어를 자동 승인하는 건 조심해야 해요. 실제로 `rm -rf ~/`를 승인해서 홈 디렉토리를 삭제한 사례가 있어요.

ykdojo가 만든 **cc-safe**:

```bash
npx cc-safe .           # 현재 디렉토리 스캔
npx cc-safe ~/projects  # 전체 프로젝트 스캔
```

`sudo`, `rm -rf`, `chmod 777`, `curl | sh`, `git push --force` 같은 위험 패턴을 감지해요.

한 달에 한 번은 실행하세요.

### 16. MCP로 외부 세계 연결

MCP(Model Context Protocol)는 Claude가 외부 서비스와 직접 소통하게 해줘요.

```bash
# Playwright (브라우저 자동화)
claude mcp add -s user playwright npx @playwright/mcp@latest

# Supabase (데이터베이스 직접 쿼리)
claude mcp add -s user supabase npx @supabase/mcp@latest
```

**Playwright 활용:**

```
> "Playwright로 로그인 폼 테스트해줘.
> 잘못된 비밀번호 입력 시 에러 메시지가 표시되는지 확인해"
```

**Supabase 활용:**

```
> "Supabase에서 지난 7일간 가입한 사용자 수 조회해줘"
```

### 17. Hooks로 가드레일 설정

Hooks는 특정 이벤트 시 자동 실행되는 셸 명령어예요. AI의 확률적 실수에 결정론적 제어를 더해줘요.

| Hook | 실행 시점 | 사용 사례 |
|------|-----------|-----------|
| `SessionStart` | 세션 시작 시 | 브랜치/이슈 컨텍스트 자동 주입 |
| `PreToolUse` | 도구 실행 전 | 위험한 명령어 차단 |
| `PostToolUse` | 도구 실행 후 | 자동 린팅, 로깅 |
| `PostToolUseFailure` | 도구 실패 후 | 복구 힌트 제공 |
| `PermissionRequest` | 권한 확인 시 | 안전한 명령 자동 허용 |
| `Notification` | 알림 시 | Slack/Discord 연동 |
| `Stop` | 작업 완료 시 | 최종 검증 게이트 |
| `TaskCompleted` | 작업 완료 표시 시 | lint/test 통과 강제 |

위험한 명령어 차단 예시:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/block-dangerous.sh"
          }
        ]
      }
    ]
  }
}
```

4가지 훅 타입(command, http, prompt, agent)과 17가지 이벤트에 대한 자세한 내용은 [16. Hooks 실전 가이드](./16-hooks-practical.md)를 참고하세요.

**가장 유용한 Hook: macOS 데스크톱 알림**

Claude가 작업을 끝내거나 승인이 필요할 때 맥 알림을 받을 수 있어요. 긴 작업을 시키고 다른 일 하세요.

```text
/hooks → Notification → 명령어 입력:
```

```bash
osascript -e 'display notification "$CLAUDE_NOTIFICATION_MESSAGE" with title "Claude Code"'
```

### 18. Skills와 Agents 활용

**Skills**: Claude가 필요할 때 자동으로 호출하는 재사용 가능한 지식 조각

```markdown
<!-- ~/.claude/skills/google-translate/skill.md -->
# Google Translate Skill
When asked about pronunciation, generate:
https://translate.google.com/?sl=auto&tl={lang}&text={word}
```

**Agents (서브에이전트)**: 전문화된 독립 AI 프로세스

- 각자 독립적인 200k 컨텍스트
- 병렬 실행 가능
- 완료 후 결과를 메인에게 반환

```
> "security-auditor 에이전트를 실행하여
> 이 코드베이스의 보안 취약점을 찾아줘"
```

### 19. CLAUDE.md vs Skills vs Slash Commands vs Plugins

| 기능 | 로딩 시점 | 누가 사용 | 토큰 효율 |
|------|-----------|-----------|-----------|
| **CLAUDE.md** | 모든 대화 시작 시 | 개발자가 설정 | 낮음 (항상 로드) |
| **Skills** | 필요 시 자동 | Claude가 판단 | 높음 |
| **Slash Commands** | 수동 호출 시 | 개발자가 호출 | 높음 |
| **Plugins** | 설치 시 | 개발자가 설치 | 혼합 |

**CLAUDE.md에는 항상 필요한 것만**, 나머지는 Skills/Commands로 분리하세요.

### 20. 터미널 탭으로 멀티태스킹

| 탭 | 용도 | 예시 |
|----|------|------|
| 탭 1 | 메인 개발 | 새 기능 구현 |
| 탭 2 | 버그 수정 | 긴급 핫픽스 |
| 탭 3 | 리서치 | 새 라이브러리 테스트 |
| 탭 4 | DevOps | CI/CD 디버깅 |

> "각 탭은 독립적인 '두뇌'입니다. 작업을 섞지 마세요." — ykdojo

---

## Phase 4: 마스터리 (3개월+)

### 21. 컨테이너로 위험한 작업 격리

YOLO 모드를 쓰고 싶다면, 컨테이너 안에서:

```dockerfile
FROM ubuntu:22.04
RUN apt-get update && apt-get install -y curl git nodejs npm
RUN curl -fsSL https://claude.ai/install.sh | sh
WORKDIR /workspace
CMD ["/bin/bash"]
```

```bash
docker build -t claude-sandbox .
docker run -it --rm \
  -v $(pwd):/workspace \
  -e ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY \
  claude-sandbox

# 컨테이너 안에서
claude --dangerously-skip-permissions
```

ykdojo는 한 걸음 더 나아가서, 로컬의 메인 Claude가 컨테이너 안의 "워커" Claude에게 tmux로 작업을 지시하는 오케스트레이션까지 구현했어요.

### 22. Headless 모드로 CI/CD 통합

`-p` 플래그로 비대화형 실행:

```bash
# 기본
claude -p "Fix the lint errors"

# 파이프라인 통합
git diff | claude -p "Explain these changes"

# JSON 출력
echo "Review this PR" | claude -p --json
```

GitHub Actions 예시:

```yaml
name: Claude Code Review
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Review PR
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          git diff origin/main...HEAD | \
          claude -p "Review this PR and identify potential issues" \
          > review.md
```

### 23. Extended Thinking

`ultrathink` 키워드로 깊은 추론을 활성화하세요:

```
> "ultrathink: 이 아키텍처 결정의 장단점을 깊이 분석해줘"
```

Claude가 응답 전에 최대 32k 토큰을 내부 추론에 할당해요. 복잡한 아키텍처 결정이나 까다로운 디버깅에 유용해요.

### 24. 자동화의 자동화

ykdojo의 자동화 레벨 진화:

```
Level 0: ChatGPT에서 복사 → 붙여넣기
Level 1: 터미널 통합 (Claude Code)
Level 2: 음성 전사 시스템 (타이핑 자동화)
Level 3: CLAUDE.md (반복 지시 자동화)
Level 4: 커스텀 슬래시 명령어 (워크플로우 자동화)
Level 5: Skills (Claude의 자동 판단 자동화)
Level 6: Hooks (규칙 강제 자동화)
```

> "같은 작업을 3번 이상 반복한다면, 자동화할 방법을 찾으세요. 그리고 그 자동화 과정 자체도 자동화하세요." — ykdojo

### 24-1. /loop과 Cron — 반복 자동화

**`/loop`**: 프롬프트를 일정 간격으로 반복 실행해요.

```text
# 5분마다 빌드 상태 확인
/loop 5m "빌드 상태 확인하고 실패하면 수정해줘"

# 10분마다 PR 모니터링 (기본 간격: 10분)
/loop /commit
```

**Cron**: 스케줄 기반 자동화. Claude Code가 실행 중이지 않아도 동작해요.

```text
/cron
> "매일 오전 9시에 어제 올라온 PR을 리뷰해줘"
```

`/loop`은 세션 내 반복, Cron은 세션 밖 스케줄링이에요. 상황에 맞게 쓰세요.

### 24-2. /sandbox — 안전한 자율 모드

`--dangerously-skip-permissions`가 불안하다면, `/sandbox`가 대안이에요.

```text
/sandbox
```

승인 없이 실행하되, **파일과 네트워크를 격리**해요. 컨테이너 설정 없이 간편하게 안전한 자율 모드를 쓸 수 있어요.

| 방식 | 안전성 | 편의성 | 용도 |
|------|--------|--------|------|
| `/permissions` | 세밀한 제어 | 초기 설정 필요 | 일상 작업 |
| `/sandbox` | 격리된 환경 | 즉시 사용 | 실험, 프로토타입 |
| `--dangerously-skip-permissions` | 없음 | 최고 | 컨테이너 안에서만 |

### 24-3. Boris Cherny(Claude Code 창시자)의 습관

[howborisusesclaudecode.com](https://howborisusesclaudecode.com)에서 선별한 핵심 5가지:

**1. 검증이 핵심**

Claude에게 테스트를 돌릴 수 있게 하세요. 품질이 2~3배 올라갑니다.

**2. 5개 Worktree 병렬**

기능 A + 버그 B + 리팩토링 C를 동시에 진행해요.

```bash
claude -w feature-auth     # worktree 1
claude -w fix-payment      # worktree 2
claude -w refactor-api     # worktree 3
```

**3. CLAUDE.md 계속 갱신**

실수할 때마다 CLAUDE.md에 적으세요. 같은 실수를 두 번 안 합니다.

**4. Plan을 Plan으로 리뷰**

한 Claude가 계획하고, 다른 Claude가 그 계획을 리뷰해요.

**5. 다시 시키세요**

"더 우아하게 구현해" 한 마디면 됩니다. Claude는 같은 걸 더 잘 만들 수 있어요.

### 25. 출력 검증 습관

AI가 생성한 코드를 맹목적으로 신뢰하지 마세요.

| 검증 방법 | 설명 |
|-----------|------|
| **테스트 코드** | 엣지 케이스 포함해서 작성 |
| **GitHub Desktop** | 변경 사항 시각적으로 diff 확인 |
| **Draft PR** | PR 화면에서 검토 |
| **자기 검증** | "방금 생성한 코드를 다시 검토하고, 검증 결과를 표로 정리해줘" |

**자기 검증 요청 예시:**

Claude에게 자신의 출력을 스스로 검증하게 하면 누락을 잡아낼 수 있어요.

```
> "방금 생성한 코드를 다시 검토해줘.
> 모든 주장을 검증하고, 끝에 검증 결과를 표로 정리해줘."
```

Claude가 이런 식으로 자기 검증을 수행해요:

| 주장 | 검증 방법 | 결과 |
|------|-----------|------|
| "이 함수는 O(n) 시간 복잡도" | 코드 분석 | ✅ 확인됨 |
| "모든 엣지 케이스 처리" | 테스트 코드 검토 | ❌ null 케이스 누락 |
| "XSS 공격에 안전" | 입력 검증 확인 | ⚠️ sanitize 추가 필요 |

ykdojo의 프롬프트:

```
> "모든 것을 다시 확인하고,
> 검증 가능한 것과 불가능한 것을 표로 만들어줘"
```

!!! tip "ykdojo의 조언"
    "제가 가장 좋아하는 프롬프트예요. Claude가 자신의 출력을 비판적으로 재검토하면, 처음에 놓쳤던 것들을 스스로 찾아냅니다."

### 26. 범용 인터페이스로 활용

Claude Code는 코딩 도구가 아니라 **컴퓨터에 대한 범용 인터페이스**예요.

```
> "이 비디오 파일의 처음 30초를 잘라내고 1080p로 리사이즈해줘"
→ Claude가 ffmpeg 사용

> "이 폴더의 모든 mp3 파일을 전사해줘"
→ Claude가 Whisper 사용

> "디스크 공간 분석하고 정리 방법 제안해줘"
→ Claude가 du, docker system prune 등 활용
```

> "컴퓨터에서 하고 싶은 일이 있으면, 일단 Claude Code에게 물어보세요." — ykdojo

---

## 추상화 수준 선택하기

ykdojo는 Claude Code 사용을 "거대한 빙산 탐험"에 비유해요.

**Vibe Coding (높은 추상화)**

- 전체 구조만 파악, 개별 코드는 신경 X
- 일회성 프로젝트, 프로토타입, 빠른 실험

**Deep Dive (낮은 추상화)**

- 파일 구조, 함수, 의존성까지 꼼꼼히 검토
- 프로덕션 코드, 보안, 성능 최적화

```
새 기능 추가할 때:
1. 높은 추상화: "사용자 프로필 페이지 만들어줘"
2. 중간 추상화: "프로필 편집 폼의 유효성 검사 로직 보여줘"
3. 낮은 추상화: "이 정규식이 왜 이메일 검사에 실패하는지 설명해줘"
```

> "Vibe Coding이 나쁘다고 말하는 사람이 있지만, 때로는 완전히 괜찮습니다. 핵심은 상황에 맞는 추상화 수준을 선택하는 것입니다." — ykdojo

---

## 에이전틱 개발자의 마인드셋

### 미지의 영역에서 용감하게

ykdojo는 React 전문가가 아니었지만 Claude와 프론트엔드 버그를 해결했고, Rust를 처음 접했지만 Python-Rust 혼합 문제를 풀었어요.

반복적 문제 해결 과정:

```
1. 초기 시도 → 작동하지 않음
2. 속도 조절 → "이 줄이 정확히 무슨 의미인지 설명해줘"
3. 방향 전환 → 막다른 골목이면 다른 접근
4. 깊이 조절 → 추상화 수준 높이거나 낮춤
5. 최종 해결
```

### 사용이 최고의 학습

> "암벽 등반을 잘하려면 어떻게 해야 하나요?" — "암벽 등반을 하세요."

ykdojo는 "10,000시간 규칙" 대신 **"10억 토큰 규칙"**을 제안해요. 많이 써야 직관이 생기거든요.

### 지식 공유의 선순환

ykdojo는 팁을 공유하면서 `--system-prompt` 플래그의 존재를 알게 됐고, Reddit 댓글에서 새로운 활용법을 배웠어요.

> "지식 공유는 일방통행이 아닙니다. 공유 과정에서 새로운 것을 배웁니다." — ykdojo

---

## 빠른 참조 치트시트

### 명령어

| 명령어 | 설명 |
|--------|------|
| `/init` | CLAUDE.md 자동 생성 |
| `/model` | 모델 선택 (← → 로 effort 조절) |
| `/fast` | 2.5배 빠른 출력 (비용 6배) |
| `/usage` | 토큰 사용량 확인 |
| `/context` | 컨텍스트 X-Ray |
| `/clear` | 대화 초기화 |
| `/compact` | 컨텍스트 압축 (지시 포함 가능) |
| `/stats` | 사용 통계, 스트릭, 모델별 현황 |
| `/export` | 대화 마크다운 내보내기 |
| `/mcp` | MCP 서버 관리 |
| `/permissions` | 승인 명령어 관리 |
| `/rename` | 세션 이름 지정 |
| `/teleport` | 터미널 → 웹 세션 이동 |
| `/desktop` | CLI → 데스크톱 앱 이동 |
| `/statusline` | 하단 상태 바 설정 |
| `/keybindings` | 키보드 단축키 커스터마이징 |
| `/vim` | Vim 키바인딩 활성화 |
| `/doctor` | 설치 상태 건강 검진 |
| `/sandbox` | 격리된 자율 모드 |
| `/hooks` | Hook 관리 |

### 키보드 단축키

| 단축키 | 기능 |
|--------|------|
| `!command` | 셸 명령 즉시 실행 |
| `Esc` | 생성 중지 |
| `Esc Esc` | 되감기 (Undo) |
| `Ctrl+R` | 역방향 검색 |
| `Ctrl+S` | 프롬프트 임시 저장 |
| `Ctrl+V` | 이미지 붙여넣기 |
| `Ctrl+G` | 외부 에디터로 편집 |
| `Ctrl+O` | thinking 과정 실시간 표시 |
| `Ctrl+B` | 백그라운드로 보내기 |
| `Ctrl+T` | 할 일 목록 토글 |
| `Shift+Tab` | 모드 전환 (Normal/Plan/Auto) |
| `Cmd+P` | 모델 선택기 |
| `Cmd+T` | Extended Thinking 토글 |
| `Tab` | 자동완성 제안 수락 |
| `\` + Enter | 여러 줄 입력 |

### CLI 플래그

| 플래그 | 설명 |
|--------|------|
| `-p "prompt"` | Headless 모드 |
| `--continue` | 마지막 세션 이어가기 |
| `--resume` | 세션 목록에서 선택 |
| `--resume name` | 이름으로 세션 재개 |
| `--from-pr 123` | PR 컨텍스트로 재개 |
| `--chrome` | Chrome 통합 |
| `-w feature` | 독립 Worktree에서 작업 |
| `--max-budget-usd 5` | 비용 제한 |
| `--max-turns 3` | 턴 수 제한 |

---

## 학습 로드맵

### 초급 (1-3개월)

| 주차 | 학습 내용 | 실습 과제 |
|------|-----------|-----------|
| 1주 | 설치, `/usage`, `/clear` | 간단한 함수 작성 |
| 2주 | CLAUDE.md, `/init` | 프로젝트에 CLAUDE.md 추가 |
| 3-4주 | 컨텍스트 관리, 단일 목적 대화 | 독립 작업 별도 세션 수행 |
| 5-6주 | Git 통합, 자동 커밋 | Draft PR 5개 생성 |
| 7-8주 | 별칭, 단축키 | 별칭 3개 만들기 |

**성공 지표**: 모든 새 프로젝트에 `/init` 실행, 주 3회 이상 사용

### 중급 (3-12개월)

| 월 | 학습 내용 |
|----|-----------|
| 3-4월 | MCP 서버 1개 이상 연동 |
| 5-6월 | Hooks 설정, 위험 명령어 차단 |
| 7-8월 | Skills 및 커스텀 명령어 제작 |
| 9-10월 | 컨테이너 워크플로우 |
| 11-12월 | 서브에이전트 병렬 작업 |

**성공 지표**: 생산성 50%+ 향상, 자신만의 커스텀 명령어 5개+

### 고급 (1년+)

| 분기 | 학습 내용 |
|------|-----------|
| Q1 | 시스템 프롬프트 분석 및 패치 |
| Q2 | 맞춤형 MCP 서버 개발 |
| Q3 | 멀티 에이전트 오케스트레이션 |
| Q4 | Claude Agent SDK 활용, CI/CD 통합 |

**성공 지표**: 팀 전체의 Claude Code 도입 주도, 커뮤니티 기여

---

## 참고 자료

| 자료 | 링크 |
|------|------|
| **ykdojo의 claude-code-tips** | [github.com/ykdojo/claude-code-tips](https://github.com/ykdojo/claude-code-tips) |
| **Ado의 Advent of Claude** | [adocomplete.com/advent-of-claude-2025](https://adocomplete.com/advent-of-claude-2025/) |
| **Claude Code 공식 문서** | [code.claude.com/docs](https://code.claude.com/docs/en/overview) |
| **Anthropic 베스트 프랙티스** | [anthropic.com/engineering/claude-code-best-practices](https://www.anthropic.com/engineering/claude-code-best-practices) |
| **r/ClaudeAI** | [reddit.com/r/ClaudeAI](https://www.reddit.com/r/ClaudeAI/) |
| **ykdojo의 dx 플러그인** | `claude plugin marketplace add ykdojo/claude-code-tips` |
| **cc-safe (보안 감사)** | `npx cc-safe .` |
