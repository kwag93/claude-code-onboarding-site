# 08. oh-my-claudecode 활용 가이드

oh-my-claudecode(OMC)의 주요 기능과 실전에서 쓰는 법입니다.

---

## OMC 업데이트

OMC는 활발하게 개발되고 있어 정기적인 업데이트가 중요합니다.

### 업데이트 방법

```bash
# 최신 버전 설치 (업데이트도 동일 명령)
/plugin install oh-my-claudecode

# 설정 새로고침
/oh-my-claudecode:omc-setup

# 캐시 문제 해결
/oh-my-claudecode:doctor
```

### 업데이트 확인

```bash
# 현재 설치된 상태 확인
/oh-my-claudecode:help
```

> **Tip**: 주 1회 정도 업데이트하면 새 에이전트, 스킬, 버그 수정이 자동 반영됩니다.

---

## autopilot 모드: 아이디어에서 완성까지

autopilot은 OMC에서 제일 쓸만한 기능이에요. **2~3줄만 적어주면** 요구사항 분석부터 검증까지 알아서 돌아갑니다.

### 실행 흐름

```
[아이디어 입력]
    ↓
Phase 0: 요구사항 확장 (Analyst + Architect)
    ↓
Phase 1: 구현 계획 (Architect + Critic)
    ↓
Phase 2: 병렬 구현 (Executor 팀)
    ↓
Phase 3: QA 사이클 (빌드/테스트/수정 반복)
    ↓
Phase 4: 다각도 검증 (아키텍처/보안/코드 품질)
    ↓
[완성된 코드]
```

### 사용 예시

```
# 간단한 시작
autopilot 사용자 인증 API를 TypeScript로 만들어줘. JWT 기반, refresh token 포함.

# 더 구체적인 요청
autopilot REST API로 도서 관리 시스템 만들어줘.
- CRUD 기능
- PostgreSQL 사용
- 페이지네이션과 검색
- 테스트 코드 포함
```

### 동작 중 상태 확인

autopilot이 실행되면 각 Phase의 진행 상황이 표시됩니다.
중간에 멈추고 싶으면:

```
/oh-my-claudecode:cancel
```

---

## learner 스킬: 대화에서 배우기

learner는 **지금 대화에서 반복되는 패턴을 잡아내서** 자동으로 스킬로 만들어줍니다.

### 사용 시나리오

```
[대화 중 반복 패턴 발견]

> 아까 그 API 응답 형식 맞춰주는 거, 매번 같은 요청이야.
> 이걸 스킬로 만들어줘.

/oh-my-claudecode:learner
```

### learner가 하는 일

1. 현재 대화를 분석하여 반복 패턴 추출
2. 패턴을 재사용 가능한 스킬로 변환
3. `.claude/skills/` 또는 `.agents/skills/`에 자동 저장
4. 다음 세션부터 해당 스킬 자동 활용

### 실전 예시

```
# 대화 중 매번 같은 패턴으로 API를 만들고 있다면
/oh-my-claudecode:learner

Claude: "이 대화에서 다음 패턴을 발견했습니다:
- REST API 응답 형식: { success, data, error }
- 에러 핸들링: try-catch + 로깅
- 입력 검증: zod 스키마 사용

'api-pattern' 스킬로 등록할까요?"

> 응, 등록해줘

# 다음 세션부터 자동 적용
> API 만들어줘
Claude: [api-pattern 스킬 자동 적용하여 일관된 패턴으로 생성]
```

---

## HUD: 상태를 한눈에

HUD(Heads-Up Display)는 지금 세션 **상태를 한눈에 보여줍니다.**

### 활성화

```
/oh-my-claudecode:hud
```

### HUD가 보여주는 정보

- 현재 활성 모드 (autopilot, ralph, ultrawork 등)
- 토큰 사용량
- 진행 중인 작업 상태
- 에이전트 활동 현황

### 프리셋 설정

```
# 미니멀 모드
/oh-my-claudecode:hud minimal

# 전체 정보 모드
/oh-my-claudecode:hud full

# 특정 요소만 표시
/oh-my-claudecode:hud --show tokens,mode,tasks
```

---

## 유용한 실행 모드

### ecomode: 토큰 절약

```
# 토큰 30-50% 절약하면서 작업
eco 이 함수 리팩토링해줘
```

간단한 작업에는 Haiku, 복잡한 작업에는 Sonnet을 자동 라우팅합니다.

### ralph: 끝날 때까지

```
# 검증 통과할 때까지 반복
ralph 이 기능 완성해줘. 테스트 전부 통과해야 해.
```

구현 → 검증 → 수정 루프를 **자동으로 반복**합니다.

### ultrawork: 최대 병렬

```
# 최대 병렬 처리
ulw 이 5개 컴포넌트 동시에 만들어줘
```

독립적인 작업을 **병렬로 동시 실행**합니다.

### team: 팀 조율

```
# 에이전트 팀 구성
team 결제 모듈 리팩토링해줘.
분석, 구현, 테스트를 각각 맡겨.
```

여러 에이전트가 **역할을 나눠** 협업합니다.

---

## 자주 쓰는 OMC 명령어

| 명령어 | 설명 |
| --- | --- |
| `/oh-my-claudecode:help` | 전체 도움말 |
| `/oh-my-claudecode:doctor` | 설치 상태 진단 및 수정 |
| `/oh-my-claudecode:trace` | 에이전트 실행 타임라인 확인 |
| `/oh-my-claudecode:cancel` | 실행 중인 모드 중단 |
| `/oh-my-claudecode:note` | 세션 메모 저장 (compaction 대비) |
| `/oh-my-claudecode:deepsearch` | 코드베이스 깊이 검색 |
| `/oh-my-claudecode:deep-init` | 프로젝트 초기 인덱싱 |
| `/oh-my-claudecode:mcp-setup` | MCP 서버 자동 구성 |
| `/oh-my-claudecode:build-fix` | 빌드/타입 에러 자동 수정 |
| `/oh-my-claudecode:code-review` | 코드 리뷰 실행 |
| `/oh-my-claudecode:security-review` | 보안 리뷰 실행 |
| `/oh-my-claudecode:tdd` | TDD 모드 (테스트 먼저) |

---

## 에이전트 라우팅 이해하기

OMC는 작업 난이도에 따라 **알아서 모델을 골라줍니다.**

| 모델 | 용도 | 예시 |
| --- | --- | --- |
| **Haiku** | 간단한 조회, 검색 | 파일 찾기, 심볼 검색 |
| **Sonnet** | 표준 구현, 디버깅 | 코드 작성, 버그 수정 |
| **Opus** | 아키텍처, 심층 분석 | 설계 리뷰, 복잡한 리팩토링 |

덕분에 **쓸데없이 비싼 모델을 안 써도 돼서** 토큰이 절약됩니다.

---

## 다음 단계

OMC 활용법을 익혔다면 [09. 자동화의 힘](./09-automation.md)에서 반복 작업을 자동화하는 방법을 알아보세요.

---

**참고 자료**:

- [oh-my-claudecode GitHub](https://github.com/Yeachan-Heo/oh-my-claudecode)
- [OMC 공식 문서](https://github.com/Yeachan-Heo/oh-my-claudecode#readme)
