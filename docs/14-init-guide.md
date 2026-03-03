# 14. /init으로 프로젝트 초기화하기

새 프로젝트에 Claude Code를 처음 붙일 때 가장 먼저 실행해야 할 명령어예요.

---

## /init이란?

`/init`은 Claude Code에 내장된 프로젝트 스캔 명령어예요. 실행하면 Claude가 **프로젝트를 훑어보고 CLAUDE.md를 알아서 만들어줍니다.**

### 스캔하는 항목들

| 항목 | 예시 |
| --- | --- |
| **파일 구조** | 디렉토리 레이아웃, 모듈 경계 |
| **패키지 설정** | `package.json`, `pyproject.toml`, `Cargo.toml` |
| **빌드/설정 파일** | `tsconfig.json`, `.eslintrc`, `Dockerfile` |
| **코드 패턴** | 네이밍 컨벤션, 자주 쓰는 라이브러리 |
| **기존 문서** | `README.md`, 기존 `CLAUDE.md` |

### 결과물: CLAUDE.md

스캔이 끝나면 프로젝트 루트에 `CLAUDE.md`가 생겨요. 이 파일이 있으면 Claude가 세션을 시작할 때마다 읽어서 **프로젝트 맥락을 미리 파악**합니다.

```markdown
# Project Context

## Build Commands
- `pnpm dev` - 개발 서버 실행
- `pnpm build` - 프로덕션 빌드
- `pnpm test` - 테스트 실행

## Directory Structure
- `src/` - 소스 코드
- `src/components/` - React 컴포넌트
- `src/lib/` - 공통 유틸리티

## Tech Stack
- React 18, TypeScript, Vite
- TanStack Query, Zustand
- Vitest, Playwright

## Conventions
- 컴포넌트명: PascalCase
- 훅: use 접두사
- 테스트: 파일명.test.ts

## Do Not Modify
- `src/generated/` - 자동 생성 파일
- `.env.production` - 프로덕션 환경변수
```

---

## /init vs deep-init 비교

두 가지 초기화 방법이 있어요. 상황에 맞게 골라 쓰세요.

| 항목 | `/init` | `/oh-my-claudecode:deep-init` |
| --- | --- | --- |
| **제공** | Claude Code 내장 | OMC 플러그인 |
| **결과물** | 루트 `CLAUDE.md` 1개 | `CLAUDE.md` + 디렉토리별 `AGENTS.md` |
| **깊이** | 프로젝트 전체 요약 | 계층적 상세 문서화 |
| **소요시간** | ~30초 | 2~5분 |
| **언제 쓸까** | 빠르게 시작할 때 | 대규모 프로젝트, 팀 온보딩 |

### 어떤 걸 써야 할까?

> 작은 프로젝트나 혼자 쓸 때는 `/init`으로 충분합니다.
> 팀 프로젝트이거나 디렉토리 구조가 복잡하면 `deep-init`을 추천합니다.

`deep-init`은 `src/auth/`, `src/payment/` 같은 서브디렉토리마다 `AGENTS.md`를 따로 만들어서 **에이전트가 그 영역의 규칙을 정확히 파악**하게 해줍니다.

!!! note "핵심 차이"
    `/init`은 **프로젝트 전체를 한 장으로 요약**하고, `deep-init`은 **디렉토리마다 맥락을 따로 기록**합니다. 모노레포나 서비스가 여러 개인 프로젝트라면 deep-init이 훨씬 효과적이에요.

---

## /init 실행하기

```
/init
```

프로젝트 루트에서 Claude Code 세션을 열고 위 명령어를 입력하면 돼요.

### Before / After

**Before: CLAUDE.md 없을 때**

```
Claude: 이 코드베이스에서 인증 로직 어디 있어?
→ 파일 전체를 직접 탐색 (느리고 비쌈)
```

**After: CLAUDE.md 있을 때**

```
Claude: 이 코드베이스에서 인증 로직 어디 있어?
→ CLAUDE.md 읽고 src/auth/ 바로 확인 (빠르고 정확)
```

### 실행 중에 하는 일

1. 파일 트리 스캔
2. 주요 설정 파일 파싱
3. 코드 샘플 분석
4. CLAUDE.md 초안 작성
5. 검토 요청 (수정 원하면 바로 말하면 됨)

---

## /init 후 해야 할 것들

생성된 내용이 완벽하지는 않아요. **반드시 검토하고 팀 관례를 직접 추가하세요.**

### 1. 생성된 CLAUDE.md 검토

- 빌드 명령어가 맞는지 확인
- 잘못 감지된 기술 스택 수정
- 민감한 정보가 포함됐는지 체크

### 2. 팀 관례 수동 추가

Claude가 코드만 봐서는 알 수 없는 것들이 있어요.

```markdown
## Team Conventions

### PR 규칙
- 제목 형식: `feat(scope): 설명`
- PR 크기: 400줄 이하 권장
- 리뷰어 최소 1명 승인 필수

### 코딩 컨벤션
- 함수형 컴포넌트만 사용 (클래스 컴포넌트 금지)
- 인라인 스타일 금지, Tailwind 클래스 사용
- API 응답은 zod로 검증

### 테스트 기준
- 커버리지 80% 이상 유지
- E2E는 크리티컬 플로우만
```

### 3. git 커밋하여 팀 공유

```bash
git add CLAUDE.md
git commit -m "chore: add CLAUDE.md for Claude Code context"
git push
```

팀원 모두 같은 맥락으로 Claude를 쓸 수 있게 돼요.

### 4. @import로 세부 문서 분리

CLAUDE.md가 너무 길어지면 파일을 나눠서 관리하세요.

```markdown
# CLAUDE.md (루트)

@import ./docs/conventions.md
@import ./docs/architecture.md
@import ./.claude/commands.md
```

각 파일을 작게 유지하면 Claude가 더 정확하게 읽어요.

---

## 팀 온보딩 워크플로우

`/init` 한 번이면 팀 온보딩이 훨씬 쉬워져요.

```
1. 새 팀원이 프로젝트 clone
        ↓
2. claude 명령으로 Claude Code 실행
        ↓
3. /init 또는 deep-init 실행
        ↓
4. CLAUDE.md가 알아서 컨텍스트 제공
        ↓
5. 팀 스킬/훅이 바로 동작
        ↓
6. 첫날부터 팀 컨벤션에 맞는 코드 생성
```

> 기존에는 "우리 팀은 이렇게 해요"를 말로 설명해야 했다면, 이제는 CLAUDE.md가 그 역할을 합니다.

---

## /init 활용 팁

### 주기적으로 다시 실행하기

프로젝트가 크게 바뀌면 다시 스캔하세요.

```
# 기술 스택이 바뀌었을 때
# 새 디렉토리 구조가 생겼을 때
# 팀 컨벤션이 업데이트됐을 때
/init
```

기존 CLAUDE.md를 덮어쓸지 물어봐요. 기존 내용은 보존하면서 새 정보만 합칠 수 있어요.

### .claude/ 디렉토리 구조 이해하기

`/init`이 만들어주는 것 외에도 `.claude/` 아래에 여러 설정을 둘 수 있어요.

```
.claude/
├── settings.json      # Claude Code 전역 설정
├── commands/          # 커스텀 슬래시 커맨드
│   └── deploy.md
└── skills/            # 팀 전용 스킬
    └── api-pattern.md
```

### CLAUDE.md의 3가지 계층

Claude는 세션 시작 시 세 위치의 CLAUDE.md를 **모두 읽고 합쳐서** 적용해요.

| 계층 | 경로 | 용도 |
| --- | --- | --- |
| **글로벌** | `~/.claude/CLAUDE.md` | 내 모든 프로젝트에 공통 적용 |
| **프로젝트** | `./CLAUDE.md` | 현재 프로젝트 전체에 적용 |
| **디렉토리별** | `./src/CLAUDE.md` | 특정 영역에만 적용 |

예를 들어 글로벌에는 "항상 한국어로 대답해줘"를 넣고, 프로젝트에는 기술 스택을, 디렉토리별로는 각 모듈의 규칙을 넣으면 됩니다.

---

## 다음 단계

프로젝트 초기화를 마쳤으면 [02. AI 마인드셋](./02-ai-mindset.md)에서 AI와 효과적으로 협업하는 관점을 잡아보세요.

---

**참고 자료**:

- [Claude Code 공식 문서 - CLAUDE.md](https://code.claude.com/docs/en/overview)
- [oh-my-claudecode deep-init](https://github.com/Yeachan-Heo/oh-my-claudecode)
