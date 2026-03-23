# 30. 학습 기능 활용하기 — Learner, Insights, Memory

Claude Code는 쓸수록 나에게 맞춰지는 도구예요. 세 가지 학습 기능을 활용하면 세션이 끝나도 지식이 쌓입니다.

> "좋은 도구는 쓸수록 손에 익는다. 좋은 AI 도구는 쓸수록 나를 안다."

---

## 전체 그림

| 기능 | 역할 | 비유 |
|------|------|------|
| **Learner** | 대화에서 패턴을 추출 → 재사용 스킬 | 반복 작업을 기억하는 비서 |
| **Insights** | 코드 작성 시 교육적 설명 제공 | 옆에서 가르쳐주는 시니어 |
| **Memory** | 프로젝트 지식을 세션 간 유지 | 팀의 공유 위키 |

```text
대화에서 반복 패턴 발견
    ↓ Learner
재사용 가능한 스킬로 저장
    ↓ Memory
다음 세션에서도 활용
    ↓ Insights
코드 이해도 함께 성장
```

---

## Learner 스킬 심화

Learner는 대화 중 반복되는 패턴을 감지하고, 재사용 가능한 스킬로 추출해요.

### 자동 vs 수동

| 모드 | 동작 | 사용 시점 |
|------|------|-----------|
| **수동** (기본) | `/oh-my-claudecode:learner` 명령으로 직접 실행 | 의미 있는 패턴을 발견했을 때 |
| **자동** | oh-my-claudecode 설정에 따라 대화 종료 시 자동 분석 | 반복 작업이 많은 프로젝트 |

> 처음에는 **수동 모드**로 시작하세요. 어떤 패턴이 스킬로 만들어지는지 감을 잡은 뒤 자동 모드를 활성화하면 노이즈가 줄어들어요.

### 어떻게 작동하나?

1. `/oh-my-claudecode:learner`를 실행하거나, 자동 모드에서 대화가 끝나면
2. Learner가 반복 패턴을 분석하고
3. `.claude/skills/` 폴더에 스킬 파일로 저장

### 사용법

```text
/oh-my-claudecode:learner
```

현재 대화에서 추출 가능한 패턴을 분석하고, 스킬로 만들어 줍니다.

### 실전 예제

매번 이렇게 요청한다면:

```text
> "커밋 메시지를 conventional commits 형식으로 작성해줘.
> 한국어로, scope는 변경된 모듈명으로."
```

Learner가 이걸 스킬로 자동 추출:

```markdown
# .claude/skills/commit-message.md
---
name: Korean Conventional Commit
description: 한국어 conventional commits 형식 커밋 메시지 생성
triggers:
  - "커밋"
  - "commit"
---

커밋 메시지를 다음 규칙으로 작성합니다:
- Conventional Commits 형식 (feat, fix, refactor, ...)
- 한국어 설명
- scope는 변경된 주요 모듈명
```

### 커스터마이징

자동 추출된 스킬이 마음에 안 들면 직접 편집하세요:

```bash
# 스킬 목록 확인
ls .claude/skills/

# 직접 편집
claude "스킬 파일 .claude/skills/commit-message.md를 수정해줘"
```

---

## Insights 모드

Insights는 코드를 작성하면서 **교육적인 설명을 함께 제공**하는 모드예요. 새로운 코드베이스를 탐색하거나 복잡한 로직을 이해할 때 유용합니다.

### 무엇인가?

일반 모드에서는 코드만 작성하지만, Insights 모드에서는 **왜 이렇게 작성했는지** 설명이 함께 나와요.

### Before/After 비교

**일반 모드**:
```text
> "JWT 검증 미들웨어 만들어줘"

(코드만 생성)
```

**Insights 모드**:
```text
> "JWT 검증 미들웨어 만들어줘"

★ Insight ─────────────────────────────────────
- RS256 대신 HS256을 선택한 이유: 단일 서버 환경에서는 대칭키가 더 빠르고 간단
- 토큰 만료 검증을 미들웨어 레벨에서 하는 이유: 각 라우트에서 중복 검증 방지
─────────────────────────────────────────────────

(코드 생성)
```

### 활성화 방법

Insights는 Claude Code의 출력 스타일 설정을 통해 활성화해요:

```text
# Claude Code 설정에서 explanatory 모드 활성화
# .claude/settings.json 또는 hooks를 통해 설정

# 또는 대화에서 직접 요청
> "앞으로 코드를 작성할 때 왜 그렇게 했는지 인사이트도 함께 알려줘"
```

### 언제 유용한가?

| 상황 | Insights ON | Insights OFF |
|------|-------------|-------------|
| 새 코드베이스 탐색 | ✅ 구조 이해에 도움 | |
| 복잡한 알고리즘 구현 | ✅ 설계 결정 이해 | |
| 빠른 반복 작업 | | ✅ 속도 우선 |
| 익숙한 패턴 반복 | | ✅ 불필요한 설명 |

---

## 메모리 계층 통합 가이드

Claude Code에는 여러 종류의 "기억"이 있어요. 각각의 범위와 용도가 다릅니다.

| 메모리 유형 | 범위 | 지속성 | 용도 | 관리 |
|-------------|------|--------|------|------|
| **CLAUDE.md** | 프로젝트 | 영구 (git 추적) | 프로젝트 규칙, 컨벤션 | 직접 편집 |
| **Project Memory** | 프로젝트 | 로컬 영구 | AI가 기억하는 컨텍스트 | AI가 자동 관리 |
| **OMC Notepad** | 세션 | 7일 | 임시 메모, 작업 기록 | `/note` 명령 |
| **Skills** | 전역/프로젝트 | 영구 (git 추적) | 재사용 가능한 패턴 | Learner 또는 직접 |
| **Hooks** | 프로젝트 | 영구 (settings) | 이벤트 기반 자동화 | settings.json |

### 어떤 메모리를 써야 하나?

```text
"항상 이 규칙을 따라야 해"  → CLAUDE.md
"이 프로젝트의 배경이야"    → Project Memory
"지금 이 작업의 메모"       → OMC Notepad
"이 패턴을 반복 사용해"     → Skill
"이 이벤트에 자동 반응해"   → Hook
```

### CLAUDE.md vs Project Memory

이 두 가지는 혼동하기 쉬워요:

| | CLAUDE.md | Project Memory |
|---|-----------|---------------|
| **관리 주체** | 개발자가 직접 | AI가 자동 |
| **Git** | 추적됨 (팀 공유) | 로컬만 |
| **내용** | 규칙과 지시사항 | 맥락과 기억 |
| **예시** | "커밋은 한국어로" | "이 프로젝트는 3월에 런칭 예정" |

---

## 잘못된 학습 바로잡기

AI가 항상 올바른 패턴만 학습하는 건 아니에요. 잘못된 학습을 발견하면 바로잡아야 합니다.

### Learner가 잘못된 스킬을 만들었을 때

```bash
# 스킬 목록 확인
ls .claude/skills/

# 잘못된 스킬 삭제
rm .claude/skills/wrong-pattern.md

# 또는 수정
claude "이 스킬을 수정해줘: .claude/skills/wrong-pattern.md"
```

### Project Memory가 오염되었을 때

```text
# 메모리 확인
> "프로젝트 메모리에 뭐가 저장되어 있어?"

# 잘못된 기억 삭제
> "프로젝트 메모리에서 'Redis 사용' 관련 내용을 삭제해줘.
> 우리는 Memcached로 변경했어."
```

### CLAUDE.md에 잘못된 규칙이 들어갔을 때

```bash
# git으로 변경 추적
git diff CLAUDE.md

# 잘못된 부분 확인 후 수정
claude "CLAUDE.md에서 '모든 변수는 camelCase' 규칙을 삭제해줘.
우리 프로젝트는 snake_case를 써."
```

> **정기적인 학습 위생**: 주 1회 정도 `.claude/skills/`와 CLAUDE.md를 점검하는 습관을 들이세요. 오래된 규칙이나 잘못된 패턴이 쌓이면 Claude의 성능이 오히려 떨어질 수 있어요.

---

## 실전: 학습 시스템 구축하기

### 주간 리뷰 패턴

매주 한 번, 이렇게 요청하세요:

```text
> "이번 주 대화에서 반복된 패턴이 있으면 CLAUDE.md나 스킬로 정리해줘.
> 새로 알게 된 프로젝트 컨벤션도 업데이트해줘."
```

### Learner → Skill → Memory 파이프라인

```text
1. 반복 패턴 감지 (Learner)
   ↓
2. 스킬로 추출 (.claude/skills/)
   ↓
3. 프로젝트 규칙으로 승격 (CLAUDE.md)
   ↓
4. 팀과 공유 (git push)
```

### 팀 지식 공유

```bash
# CLAUDE.md와 skills는 git으로 추적되므로 팀 전체가 공유
git add .claude/skills/ CLAUDE.md
git commit -m "docs: 프로젝트 컨벤션 및 스킬 업데이트"
git push
```

팀원이 pull하면 동일한 학습 결과를 바로 사용할 수 있어요.

---

## 다음 단계

- [CLAUDE.md 마스터하기](15-claude-md-mastery.md) — CLAUDE.md 작성의 모든 것
- [스킬과 플러그인](11-skills-plugins.md) — 스킬 생태계 상세 가이드
- [직접 만들기](31-build-your-own.md) — 나만의 스킬 만들기 튜토리얼
- [흔한 실수 피하기](17-anti-patterns.md) — 컨텍스트 관리와 HANDOFF.md 활용법
