# 32. Superpowers 활용 가이드

Claude Code가 똑똑한 건 맞지만, 가끔 "대충 넘어가는" 습관이 있어요. 테스트 없이 "됐습니다"라고 하거나, 계획 없이 바로 코드부터 쓰거나, 디버깅할 때 원인 파악 대신 이것저것 바꿔보거나. Superpowers는 이런 문제를 해결하는 **개발 방법론 프레임워크**예요.

에이전트에게 "일하는 방식"을 가르쳐서, brainstorming → 계획 → TDD → 리뷰까지 체계적으로 진행하게 만듭니다.

---

## 왜 Superpowers인가

### 에이전트에게 규율을 심어준다

Claude Code는 지시에 잘 따르지만, **스스로 체계적인 프로세스를 지키지는 않아요.** Superpowers는 스킬 시스템을 통해 에이전트가 작업 전 반드시 관련 스킬을 확인하고, 정해진 워크플로우대로 움직이게 강제합니다.

예를 들어:

- 기능 구현 요청 → brainstorming 스킬이 먼저 동작해서 설계부터 잡음
- 버그 수정 요청 → systematic-debugging 스킬이 원인 분석부터 시킴
- "다 했습니다" 하기 전 → verification 스킬이 실제 테스트 통과 여부를 확인

### 검증된 생태계

- **GitHub 110,000+ Stars** — 커뮤니티 플러그인 중 압도적 규모
- **MIT 라이선스** — 자유롭게 사용, 수정, 배포 가능
- **멀티 플랫폼** — Claude Code, Cursor, Codex, Gemini CLI, OpenCode 모두 지원
- **Jesse Vincent** — 오랜 경력의 오픈소스 개발자가 행동과학 연구를 기반으로 설계

### 행동과학이 녹아든 설계

제작자는 Robert Cialdini의 설득 원리를 LLM에 적용했어요. "프로덕션 다운이라 급해요"같은 압박 상황에서도 에이전트가 프로세스를 건너뛰지 않도록 설계되어 있습니다. 이 접근은 Wharton 연구팀이 과학적으로 검증하기도 했어요.

> 핵심 철학: 에이전트가 "빠르게"보다 "제대로" 일하게 만든다.

---

## 설치와 업데이트

### Claude Code

```bash
# 설치
/plugin install superpowers@claude-plugins-official

# 업데이트
/plugin update superpowers
```

### 다른 플랫폼

| 플랫폼 | 설치 방법 |
| --- | --- |
| **Cursor** | `/add-plugin superpowers` 또는 마켓플레이스 검색 |
| **Gemini CLI** | `gemini extensions install https://github.com/obra/superpowers` |
| **Codex** | INSTALL.md 참조 요청 |
| **OpenCode** | INSTALL.md 참조 요청 |

---

## 핵심 워크플로우: 7단계 사이클

Superpowers의 가장 큰 가치는 **구조화된 개발 사이클**이에요. 각 단계마다 전용 스킬이 품질을 보장합니다.

```text
[요청 입력]
    ↓
1. Brainstorming — 아이디어 정제, 2-3가지 접근법 비교
    ↓
2. Git Worktree — 격리된 작업 공간 생성
    ↓
3. Writing Plans — 2-5분 단위 태스크로 분해
    ↓
4. Subagent Development — 태스크별 서브에이전트 할당
    ↓
5. TDD — RED → GREEN → REFACTOR 강제
    ↓
6. Code Review — 계획 대비 검증
    ↓
7. Finishing — 테스트 확인, 병합/PR 옵션 제시
```

### 실전 예시

```text
> 사용자 인증 API를 TypeScript로 만들어줘. JWT 기반.

Superpowers가 동작하면:

1. [brainstorming] "세션 vs JWT vs OAuth 중 어떤 방식이 좋을까요?"
   → 사용자와 대화하며 설계 확정

2. [git worktree] feature/auth-api 브랜치를 격리된 워크트리에서 작업

3. [writing-plans] 6개 태스크로 분해:
   - Task 1: 토큰 생성/검증 유틸리티
   - Task 2: 로그인 엔드포인트
   - Task 3: 리프레시 토큰 로직
   - ...

4. [TDD] 각 태스크마다 테스트 먼저 작성 → 구현 → 통과 확인

5. [code-review] 전체 구현이 설계 문서와 일치하는지 검증

6. [finishing] 테스트 전부 통과 확인 후 PR 생성
```

Superpowers 없이 같은 요청을 하면? 대부분 바로 코드부터 작성하고, 테스트는 나중에(혹은 빠뜨리고), 리뷰 없이 끝납니다.

---

## 주요 스킬 소개

### brainstorming: 코드 쓰기 전 생각부터

모든 작업의 시작점이에요. 소크라테스식 질문으로 요구사항을 정제합니다.

- 한 번에 하나씩 질문 (정보 과부하 방지)
- 2-3가지 접근법을 트레이드오프와 함께 제안
- 사용자가 승인해야 다음 단계로 진행

```text
> 검색 기능 추가해줘

[brainstorming 동작]
"검색 대상이 뭔가요? (a) 제목만 (b) 전체 텍스트 (c) 태그 기반"
→ 대화로 범위 확정 후 설계 문서 작성
```

### writing-plans / executing-plans: 계획을 세우고 실행

큰 작업을 **2-5분 단위 태스크**로 분해합니다. 각 태스크에 정확한 파일 경로와 변경 내용이 명시돼요.

- 태스크 간 의존성 표시
- 독립적인 태스크는 병렬 실행 가능
- 실행 중 체크포인트에서 진행 상황 보고

### test-driven-development: 테스트 없이 못 넘어감

RED-GREEN-REFACTOR 사이클을 강제합니다.

```text
[RED]    실패하는 테스트 작성
    ↓
[GREEN]  테스트를 통과하는 최소 구현
    ↓
[REFACTOR] 코드 정리 (테스트는 계속 통과)
```

"됐습니다"라는 주장 대신 **테스트 결과가 증거**가 됩니다.

### systematic-debugging: 추측 말고 분석

버그를 만나면 이것저것 바꿔보는 대신 4단계 프로세스를 따릅니다.

1. **증상 수집** — 에러 메시지, 재현 조건 파악
2. **가설 수립** — 가능한 원인 목록 작성
3. **가설 검증** — 로그, 테스트로 하나씩 확인
4. **근본 원인 수정** — 확인된 원인만 수정

### dispatching-parallel-agents: 독립 작업은 동시에

2개 이상의 독립적인 태스크가 있으면 서브에이전트에게 분배합니다.

- 각 서브에이전트는 자기 태스크만 담당
- 완료 후 2단계 검토 (사양 준수 → 코드 품질)
- git worktree로 작업 공간도 격리

### using-git-worktrees: 안전한 병렬 작업

기능 개발을 메인 작업 공간과 분리된 worktree에서 진행합니다.

- 현재 작업을 방해하지 않고 새 기능 개발
- 실패해도 worktree만 삭제하면 됨
- 여러 기능을 동시에 개발할 때 유용

### verification-before-completion: 끝났다고 하기 전에

"완료"를 선언하기 전 실제로 검증합니다.

- 테스트 실행 결과 확인
- 빌드 성공 여부 확인
- 타입 체크 통과 확인
- **증거 없는 "됐습니다"를 방지**

---

## OMCC vs Superpowers: 어떤 도구를 선택할까

두 도구 모두 Claude Code를 강화하지만, **해결하는 문제가 다릅니다.**

| | oh-my-claudecode | Superpowers |
| --- | --- | --- |
| **한 줄 요약** | 여러 에이전트를 동시에 굴리는 매니저 | 에이전트가 올바른 프로세스로 일하게 하는 코치 |
| **핵심 가치** | 병렬 실행, 모델 라우팅, autopilot | TDD 강제, 체계적 워크플로우, 품질 규율 |
| **강점** | 속도와 자동화 | 안정성과 방법론 |
| **적합한 상황** | 빠르게 여러 작업을 처리할 때 | 품질이 중요한 기능 개발·버그 수정 |
| **GitHub Stars** | 4.9k+ | 110k+ |

### 이럴 때 OMCC

- autopilot으로 아이디어 → 완성까지 한 번에 돌리고 싶을 때
- 여러 독립 작업을 최대 병렬로 처리할 때
- 토큰 비용을 줄이면서 모델을 자동 라우팅하고 싶을 때

### 이럴 때 Superpowers

- 에이전트가 테스트를 빠뜨리거나 대충 넘기는 게 싫을 때
- brainstorming → plan → TDD 같은 체계적 워크플로우가 필요할 때
- 코드 리뷰, 디버깅까지 프로세스로 관리하고 싶을 때

### 동시 사용은 비추천

두 플러그인 모두 Claude Code의 **같은 확장 포인트**(skills, hooks)를 사용합니다. 동시에 설치하면:

- 비슷한 기능의 스킬이 동시에 자동 트리거돼서 이중 실행될 수 있음
- hooks 설정이 서로 덮어쓸 수 있음
- Superpowers의 "모든 작업 전 스킬 확인" 규율이 OMCC의 autopilot 흐름과 충돌

> 하나를 골라서 익숙해진 뒤, 필요하면 다른 쪽을 시도해보세요.

---

## 다음 단계

Superpowers의 워크플로우를 이해했다면 [09. 자동화의 힘](./09-automation.md)에서 반복 작업을 자동화하는 방법을 알아보세요.

스킬 시스템 자체에 대해 더 알고 싶다면 [11. 스킬과 플러그인 생태계](./11-skills-plugins.md)를 참고하세요.

---

**참고 자료**:

- [Superpowers GitHub](https://github.com/obra/superpowers) — 소스 코드와 전체 문서
- [Superpowers 블로그](https://blog.fsck.com/2025/10/09/superpowers/) — 제작자의 설계 철학과 배경
- [Superpowers Discord](https://discord.gg/Jd8Vphy9jq) — 커뮤니티 지원
- [oh-my-claudecode 가이드](./08-omc-guide.md) — OMCC가 궁금하다면
