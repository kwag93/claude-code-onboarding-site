# 15. CLAUDE.md 마스터하기

Claude Code를 처음 쓰다 보면 같은 말을 반복하게 됩니다. "한국어로 답해줘", "pnpm 써줘", "커밋 전에 타입 체크 먼저 해줘". CLAUDE.md는 그 반복을 끝내주는 파일이에요.

> **CLAUDE.md는 AI에게 주는 사수 노트입니다.**
> 신입이 처음 출근했을 때 옆 사수가 "우리 팀은 이렇게 해"라고 알려주는 것처럼,
> CLAUDE.md는 매 세션마다 Claude에게 같은 컨텍스트를 알아서 전달해요.

---

## 왜 중요한가?

Claude Code는 **세션 시작 시 CLAUDE.md를 알아서 로드**해요. 따로 첨부하거나 언급할 필요 없어요.

이게 가져오는 변화:

| 이전 | CLAUDE.md 사용 후 |
|------|-------------------|
| 매번 "pnpm 써줘" 입력 | 자동으로 pnpm 사용 |
| 매번 언어 설정 반복 | 항상 한국어로 응답 |
| 신규 팀원에게 구두로 설명 | 파일 하나로 자동 전달 |
| 개인마다 다른 컨벤션 적용 | 팀 전체 동일 기준 |

팀이 git으로 CLAUDE.md를 공유하면 신입 온보딩 시 팀 컨텍스트가 바로 주입돼요.

---

## 60줄 원칙

HumanLayer 연구에 따르면 **LLM은 약 150-200개 지시를 합리적으로 따를 수 있습니다.**
문제는 Claude Code의 시스템 프롬프트가 이미 ~50개를 소비한다는 것이에요.

실질적으로 CLAUDE.md에 쓸 수 있는 "여유분"이 많지 않습니다.

| 범위 | 평가 |
|------|------|
| 60줄 이하 | 우수 — 핵심만 담긴 상태 |
| 60-300줄 | 허용 — 하지만 정기 정리 필요 |
| 300줄 초과 | 위험 — 지시 충돌, 성능 저하 |

**린터, 포매터 규칙은 CLAUDE.md에 쓰지 마세요.** Hooks에 위임하면 돼요. CLAUDE.md는 Claude가 코드를 읽어서 스스로 알 수 없는 것만 담아야 해요.

!!! tip "ykdojo의 조언"
    "처음에는 CLAUDE.md 없이 시작하세요. 같은 말을 반복하게 되면 그때 추가하세요. 과도한 정보는 컨텍스트를 낭비합니다."

---

## 3가지 계층 구조

CLAUDE.md는 하나가 아니에요. **3가지 계층**이 중첩되어 적용돼요.

| 계층 | 경로 | 용도 | 공유 범위 |
|------|------|------|-----------|
| 글로벌 | `~/.claude/CLAUDE.md` | 개인 습관, 언어 설정, 선호 도구 | 나만 (git 제외) |
| 프로젝트 | `./CLAUDE.md` | 팀 컨벤션, 빌드 명령, 아키텍처 결정 | 팀 전체 (git 포함) |
| 디렉토리 | `./src/CLAUDE.md` | 모듈별 특수 규칙, 도메인 지식 | 해당 디렉토리 |

### 글로벌 계층 예시 (`~/.claude/CLAUDE.md`)

개인적인 선호를 담습니다. 팀원에게 강요하지 않을 것들이에요.

```markdown
# 개인 설정

## 언어
한국어로 응답해줘. 코드 주석은 영어로.

## 패키지 매니저
npm 대신 pnpm을 항상 사용해줘.

## 응답 스타일
불필요한 설명은 줄이고, 핵심만 간결하게.
```

### 프로젝트 계층 예시 (`./CLAUDE.md`)

팀 전체가 공유하는 컨벤션을 담습니다. git에 커밋하세요.

```markdown
# MyApp 프로젝트

## 빌드 명령어
- 개발: `pnpm dev`
- 타입 체크: `pnpm check-types`
- 테스트: `pnpm test:run`
- 린트: `pnpm lint`

## 코딩 규칙
- 컴포넌트: `src/components/`
- 훅: `src/hooks/` (use 접두사 필수)
- 타입: `src/types/`

## 금지 사항
- `any` 타입 사용 금지
- `console.log` 커밋 금지
- `main` 브랜치 직접 push 금지

## 브랜치 네이밍
- `feature/PROJ-123-description`
- `fix/PROJ-123-description`
```

### 디렉토리 계층 예시 (`./src/payments/CLAUDE.md`)

특정 모듈에만 적용되는 규칙이에요.

```markdown
# Payments 모듈

이 디렉토리는 결제 관련 코드만 포함합니다.

## 중요 규칙
- Stripe SDK 직접 호출은 `stripe-client.ts`에서만
- 금액은 항상 센트(정수) 단위로 처리
- 결제 이벤트는 반드시 로깅

## 테스트
실제 Stripe API 호출 금지. `stripeMock`을 사용할 것.
```

---

## 포함할 것 vs 제외할 것

CLAUDE.md가 길어지는 주된 이유는 **안 써도 되는 것을 쓰기 때문**이에요.

| 포함할 것 | 제외할 것 |
|----------|----------|
| Claude가 추론할 수 없는 bash 명령어 | Claude가 코드를 읽으면 알 수 있는 것 |
| 기본값과 다른 코드 스타일 규칙 | Claude가 이미 아는 언어 표준 규칙 |
| 테스트 실행 방법 | 상세 API 문서 (링크만 제공) |
| 브랜치 네이밍, PR 관례 | 자주 바뀌는 정보 |
| 프로젝트 특수 아키텍처 결정 | "깔끔하게 코드 작성" 같이 자명한 것 |
| 절대 건드리면 안 되는 파일/시스템 | ESLint 규칙 전체 복붙 |

**테스트:**
> "Claude가 코드베이스를 읽으면 알 수 있는 내용인가?"

그렇다면 CLAUDE.md에 쓰지 마세요. Claude는 이미 코드를 읽을 수 있습니다.

---

## @import로 점진적 공개

CLAUDE.md가 길어지면 분리할 수 있습니다. `@파일경로` 문법으로 다른 파일을 가져올 수 있어요.

```markdown
# CLAUDE.md (목차 역할만)

@docs/git-instructions.md
@docs/api-conventions.md
@docs/testing-guide.md
```

각 파일은 작업 종류에 따라 관련 것만 참조합니다. CLAUDE.md 자체는 짧게 유지하면서, 필요한 지침은 별도 파일에서 관리하는 방식이에요.

```
~/.claude/
├── CLAUDE.md          # 진입점 (목차)
└── rules/
    ├── git-workflow.md    # Git 관련
    ├── korean-response.md # 응답 언어
    └── testing.md         # 테스트 관련
```

!!! tip "폴더 구조 팁"
    규칙이 많아지면 `rules/` 폴더를 만들고 주제별로 파일을 나눠보세요. CLAUDE.md는 `@rules/git-workflow.md` 형태로 가져오면 돼요.

---

## 좋은 CLAUDE.md vs 나쁜 CLAUDE.md

### 나쁜 예시

```markdown
# 프로젝트 가이드

이 프로젝트는 React와 TypeScript를 사용합니다.
TypeScript는 정적 타입 언어입니다. any 타입은 타입 안전성을
해칩니다. React는 컴포넌트 기반 UI 라이브러리입니다.
함수형 컴포넌트를 사용하세요. 클래스 컴포넌트는 레거시입니다.
코드는 항상 깔끔하게 작성하세요. 변수명은 의미있게 지으세요.
주석은 필요한 곳에만 달고, 너무 많이 달지 마세요.
ESLint 규칙을 따르세요: no-console, no-unused-vars, ...
Prettier 설정: printWidth 80, tabWidth 2, singleQuote true...
```

**문제점:**
- Claude가 이미 아는 내용 (`TypeScript는 정적 타입 언어입니다`)
- 자명한 규칙 (`코드는 항상 깔끔하게`)
- 툴 설정 복붙 (ESLint/Prettier는 설정 파일로 관리)
- 불필요하게 장황한 설명

### 좋은 예시

```markdown
# MyApp

## 빌드
- `pnpm dev` — 개발 서버
- `pnpm check-types && pnpm test:run` — 커밋 전 필수

## 구조
- `src/features/` — 기능별 폴더 (도메인 주도)
- `src/shared/` — 공통 컴포넌트, 훅

## 금지
- `any` 타입
- `useEffect`로 서버 데이터 페칭 (TanStack Query 사용)
- `main` 직접 push

## PR
- 제목: `feat(scope): 한글 설명`
- 템플릿: `.github/pull_request_template.md` 참고
```

**차이점:**
- Claude가 모르는 것만 (`pnpm check-types && pnpm test:run`)
- 팀 결정사항만 (`useEffect 대신 TanStack Query`)
- 짧고 스캔 가능한 구조
- 전체 20줄 이하

---

## CLAUDE.md 관리 팁

### 시작하기: `/init` 사용

```
/init
```

Claude Code가 코드베이스를 분석해서 CLAUDE.md 초안을 만들어줘요. 기술 스택, 빌드 명령어, 코딩 패턴을 읽고 반영합니다. 이후 불필요한 내용을 제거하고 팀 결정사항을 추가하면 돼요.

### 버전 관리

```bash
# 프로젝트 CLAUDE.md는 반드시 git에
git add CLAUDE.md
git commit -m "docs: 팀 컨벤션 CLAUDE.md 추가"
```

팀원이 `git pull` 하는 순간 컨텍스트가 동기화돼요.

### 정기 정리 (월 1회)

CLAUDE.md도 코드처럼 낡습니다. 월 1회 체크하세요:

- 더 이상 유효하지 않은 규칙은 삭제
- 실제로 도움이 됐던 규칙은 유지
- 팀이 반복적으로 Claude에게 말하는 것이 있다면 추가

### 팀 온보딩 자동화

신규 팀원이 첫날 `git clone`만 하면 CLAUDE.md가 함께 와요. 구두로 설명하던 "우리 팀 규칙"이 파일로 남게 되죠.

---

## 빠른 시작 체크리스트

- [ ] `/init` 실행해서 초안 생성
- [ ] 자명한 내용 삭제 (Claude가 이미 아는 것)
- [ ] 팀 결정사항 추가 (프레임워크 선택, 금지 사항)
- [ ] 빌드/테스트 명령어 확인
- [ ] `git add CLAUDE.md` 로 팀 공유
- [ ] 60줄 이하 유지 여부 확인

---

## 다음 단계

CLAUDE.md 작성법을 익혔다면 [16. Hooks 실전 가이드](./16-hooks-practical.md)에서 자동화의 다음 단계를 알아보세요. Hooks는 CLAUDE.md가 "말로 부탁"하는 방식을 넘어, Claude의 행동을 코드 수준에서 강제하는 방법이에요.

---

**참고 자료**:

- [CLAUDE.md 공식 문서](https://code.claude.com/docs/en/memory)
- [13. 파워 유저 가이드](./13-power-user-tips.md) — CLAUDE.md와 Skills/Commands 비교
