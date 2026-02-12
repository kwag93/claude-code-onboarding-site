# 11. 스킬과 플러그인 생태계

커뮤니티 스킬 쓰는 법부터 직접 만들기까지, Claude Code 확장에 대한 모든 것입니다.

---

## 스킬이란?

스킬은 Claude Code에서 **재사용할 수 있는 전문 기능 묶음**입니다.

```
[스킬 없이]
매번: "한국어 문법 검사해줘. 띄어쓰기, 맞춤법, 조사 사용..."
     → 긴 설명 반복

[스킬 사용]
/grammar-checker
     → 전문화된 규칙이 자동 적용
```

---

## 커뮤니티 스킬 활용

### humanizer: AI 글을 자연스럽게

한국어로 글 쓸 때 AI 특유의 딱딱한 느낌이 남을 때가 있죠.
humanizer 스킬은 **언어학 연구 기반**(KatFishNet 논문, 94.88% AUC)으로 AI 냄새를 없애줍니다.

```
/humanizer
```

감지하는 24가지 패턴:
- 쉼표 과다 사용
- 띄어쓰기 경직성
- AI 특유 어휘 과용 ("활용", "다양한", "효과적인")
- 구조적 단조로움 (모든 문단이 비슷한 길이)
- 대명사/복수형 과다

**사용 시점**: 문서 작성, README, 블로그 글, 사내 공지 등 **사람이 읽을 글**을 쓸 때

### grammar-checker: 한국어 문법 검사

```
/grammar-checker
```

표준 한국어 규칙에 기반한 검사:
- 맞춤법 오류 (되/돼, -ㄴ지/-는지)
- 띄어쓰기 오류 (의존명사, 보조용언)
- 문법 오류 (조사 사용, 시제)
- 구두점 문제

### style-guide: 스타일 일관성

```
/style-guide
```

프로젝트 전체에서 일관된 작성 스타일 유지:
- 어조 (경어체/반말) 불일치 감지
- 용어 통일 (사용자/유저, 삭제/제거)
- 숫자/날짜 형식 일관성

---

## 스킬 찾기와 설치

### 1. Anthropic 공식 스킬 (추천)

Anthropic이 직접 만든 공식 스킬 저장소예요. **가장 안전하고 검증된** 스킬들입니다.

- [github.com/anthropics/skills](https://github.com/anthropics/skills)

| 카테고리 | 스킬 예시 | 설명 |
| --- | --- | --- |
| **Document** | DOCX, PDF, PPTX, XLSX | 문서 생성/편집 |
| **Creative** | 아트, 디자인 | 창작 작업 |
| **Development** | 웹 테스팅, MCP 서버 생성 | 기술 자동화 |
| **Enterprise** | 브랜드 가이드, 워크플로우 | 조직 업무 |

```bash
# Claude Code에서 공식 스킬 설치
/plugin marketplace add anthropics/skills
/plugin install document-skills@anthropic-agent-skills
/plugin install example-skills@anthropic-agent-skills
```

### 2. Context7 스킬 디렉토리

[context7.com/skills](https://context7.com/skills)에서 커뮤니티 스킬을 검색할 수 있습니다.

```bash
# Context7 CLI로 스킬 설치
npx ctx7 skills install <owner/repo>
```

**주요 메트릭**:

| 지표 | 의미 | 확인 포인트 |
| --- | --- | --- |
| **Trust Score** | 스킬의 신뢰도 점수 | 높을수록 안전 |
| **Weekly Installs** | 주간 설치 수 | 많을수록 검증됨 |
| **Source** | 스킬 출처 | 신뢰할 수 있는 출처인지 확인 |

### 커뮤니티 스킬 보안 주의사항

> **커뮤니티 스킬은 누구나 올릴 수 있습니다. 반드시 주의하세요.**

```
⚠️ 설치 전 체크리스트:

1. Trust Score 확인 → 낮은 점수는 경계
2. Weekly Installs 확인 → 설치 수 0~극소수는 검증 안 됨
3. Source 코드 직접 확인 → SKILL.md 내용 읽어보기
4. 의심스러운 코드 실행 여부 → 시스템 명령어, 네트워크 요청 주의
5. 알려진 제작자인지 확인 → GitHub 프로필, 활동 이력
```

**안전한 순서**: Anthropic 공식 → Trust Score 높은 커뮤니티 → 직접 만들기

### 3. OMC 스킬 관리

```
# 설치된 스킬 목록
/oh-my-claudecode:skill list

# 새 스킬 추가
/oh-my-claudecode:skill add [스킬 경로]

# 스킬 검색
/oh-my-claudecode:skill search [키워드]
```

---

## 직접 스킬 만들기

반복되는 작업이 있으면 **스킬로 만들어서** 다음부터 다시 쓰세요.

### 스킬 구조

```
.agents/skills/
└── my-skill/
    └── SKILL.md    ← 스킬 정의 파일
```

### SKILL.md 작성법

```markdown
---
name: api-response-formatter
description: API 응답을 팀 표준 형식으로 변환
---

# API Response Formatter

## 목적
모든 API 응답을 다음 형식으로 통일합니다.

## 표준 응답 형식
\```json
{
  "success": true,
  "data": {},
  "error": null,
  "meta": {
    "timestamp": "ISO-8601",
    "requestId": "uuid"
  }
}
\```

## 규칙
1. 성공 응답: success=true, data에 결과, error=null
2. 실패 응답: success=false, data=null, error에 상세
3. 모든 응답에 meta.timestamp 필수
4. 에러 코드는 HTTP 상태 코드와 별도로 내부 코드 사용
```

### learner로 자동 생성

직접 안 써도 됩니다. **learner 스킬**이 대화 패턴에서 알아서 뽑아줘요.

```
# 대화 중 반복 패턴이 보이면
/oh-my-claudecode:learner

# Claude가 분석 후 스킬 제안
Claude: "다음 패턴을 스킬로 등록할 수 있습니다:
- API 응답 형식 통일
- 에러 핸들링 패턴
등록할까요?"

> 응

# .agents/skills/ 에 자동 저장
```

---

## 직접 플러그인 만들기

스킬보다 더 복잡한 게 필요하면 **플러그인**을 만들 수도 있어요.

### 플러그인 vs 스킬

| | 스킬 | 플러그인 |
| --- | --- | --- |
| **복잡도** | 단일 SKILL.md | 여러 파일 + 설정 |
| **범위** | 특정 작업 | 시스템 확장 |
| **배포** | 로컬 또는 공유 | 마켓플레이스 |
| **만드는 시간** | 5분 | 수 시간~ |

### 시작하기

```
# Claude에게 직접 요청
> 우리 팀에서 쓸 플러그인 만들어줘.
> 기능: PR 생성 시 자동으로 Jira 이슈 상태 변경
> 트리거: /commit-push-pr 실행 후
```

---

## 서브에이전트 활용

### .agents/ 디렉토리

프로젝트 전용 에이전트를 만들 수 있습니다.

```
.agents/
├── code-reviewer.md      ← 코드 리뷰 전문가
├── planner.md             ← 계획 수립 전문가
├── security-checker.md    ← 보안 검사 전문가
└── skills/
    └── my-skill/
        └── SKILL.md
```

### 에이전트 정의 예시

```markdown
# .agents/api-designer.md

## 역할
REST API 설계 전문가

## 규칙
1. RESTful 원칙 준수
2. OpenAPI 3.0 스펙 기반
3. 버전 관리: /api/v1/ 형식
4. 에러 응답: RFC 7807 Problem Details

## 참조
- 팀 API 가이드: /docs/api-guide.md
- 기존 API 목록: /src/routes/
```

### Task 도구로 서브에이전트 호출

Claude Code 내에서 서브에이전트를 직접 호출할 수 있습니다:

```
> 이 코드를 보안 관점에서 검토해줘.
> .agents/security-checker.md 에이전트 사용해서.
```

---

## 실전: 스킬 개발 워크플로우

### 1단계: 반복 패턴 발견

```
"매번 이거 똑같이 요청하네..."
"이 형식으로 계속 변환하는데..."
```

### 2단계: learner로 추출하거나 직접 작성

```
# 자동 추출
/oh-my-claudecode:learner

# 또는 직접 작성
> .agents/skills/my-formatter/ 에 SKILL.md 만들어줘
```

### 3단계: 테스트

```
> 방금 만든 스킬 테스트해볼게.
> [테스트 입력 제공]
```

### 4단계: 팀 공유

```bash
# git으로 공유
git add .agents/skills/
git commit -m "feat: API 응답 포맷터 스킬 추가"
git push
```

---

## 실천 체크리스트

- [ ] `/humanizer` 로 한국어 글 다듬기 시도
- [ ] `/grammar-checker` 로 문법 검사 실행
- [ ] `/oh-my-claudecode:learner` 로 대화 패턴 스킬화 시도
- [ ] `.agents/skills/` 에 간단한 스킬 하나 직접 만들기
- [ ] `.agents/` 에 프로젝트 전용 에이전트 정의

---

## 다음 단계

스킬과 플러그인 생태계를 이해했다면 [12. AI와 함께 일하는 방법](./12-ai-work-philosophy.md)에서 AI를 활용하는 근본적인 철학을 알아보세요.

---

**참고 자료**:

- [Anthropic 공식 스킬](https://github.com/anthropics/skills) - 검증된 공식 스킬 저장소
- [Context7 스킬 디렉토리](https://context7.com/skills) - 커뮤니티 스킬 검색 (Trust Score 확인 필수)
- [Agent Skills 표준 스펙](https://agentskills.io) - 스킬 작성 표준
- [humanizer 스킬](https://github.com/blader/humanizer) - AI 글 자연화
- [oh-my-claudecode 스킬 가이드](https://github.com/Yeachan-Heo/oh-my-claudecode)
