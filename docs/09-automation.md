# 09. 자동화의 힘

Claude Code로 자동화할 수 있는 것들과 그 방법이에요.

---

## 무엇을 자동화할 수 있는가?

Claude Code는 단순 코드 생성 도구가 아닙니다.
**개발하면서 하는 일 전체**를 자동화할 수 있어요.

```
코드 작성 → 테스트 → 리뷰 → 커밋 → PR → 배포
    ↑         ↑       ↑       ↑      ↑
    모든 단계를 Claude Code가 도와줍니다
```

---

## 1. Git Commit 자동화

### 기본 사용

```
/commit
```

이 한 줄이면:
1. 변경된 파일 분석
2. 변경 내용에 맞는 커밋 메시지 자동 생성 (Conventional Commits)
3. 사용자 확인 후 커밋

### 실제 동작 예시

```
> /commit

Claude: "변경 사항을 분석했습니다:
- src/auth/login.ts: JWT 검증 로직 추가
- src/auth/login.test.ts: 테스트 케이스 3개 추가

제안 커밋 메시지:
feat(auth): JWT 토큰 검증 로직 및 테스트 추가

이 메시지로 커밋할까요?"
```

### 커밋 + 푸시 + PR 한 번에

```
/commit-push-pr
```

커밋부터 PR 생성까지 **한 번에** 처리합니다.

---

## 2. PR 자동화

### PR 생성

```
> 지금까지 작업한 내용으로 PR 만들어줘

Claude:
- 브랜치의 모든 커밋 분석
- PR 제목 자동 생성
- 변경 사항 요약 작성
- 테스트 계획 포함
- gh pr create 실행
```

### PR 리뷰

```
> 이 PR 리뷰해줘: #123

# 또는 OMC 사용
/oh-my-claudecode:code-review
```

자동 리뷰 항목:
- 코드 품질 (안티패턴, 중복)
- 보안 취약점
- 성능 이슈
- 테스트 커버리지
- 스타일 가이드 준수

---

## 3. 테스트 자동화

### TDD 모드

```
/oh-my-claudecode:tdd

> 사용자 인증 기능 만들어줘
```

TDD 모드에서는:
1. **테스트를 먼저** 작성 (Red)
2. 테스트 통과하는 코드 작성 (Green)
3. 리팩토링 (Refactor)

### QA 사이클 (ultraqa)

```
# 테스트 전부 통과할 때까지 반복
/oh-my-claudecode:ultraqa
```

동작 방식:
```
테스트 실행 → 실패 발견 → 수정 → 다시 테스트 → ...
(최대 5 사이클, 같은 에러 3회 반복 시 중단)
```

### 테스트 커버리지 확인

```
> 현재 테스트 커버리지 확인하고, 부족한 부분 테스트 추가해줘
```

---

## 4. 코드 리뷰 자동화

### 전체 리뷰

```
/oh-my-claudecode:code-review
```

OMC의 코드 리뷰는 **5가지 관점**에서 동시에 진행됩니다:

| 관점 | 에이전트 | 확인 항목 |
| --- | --- | --- |
| 스타일 | style-reviewer | 네이밍, 포맷팅, 컨벤션 |
| 품질 | quality-reviewer | 로직 결함, 안티패턴, SOLID |
| API | api-reviewer | 계약, 호환성, 버전 |
| 보안 | security-reviewer | OWASP Top 10, 인증/인가 |
| 성능 | performance-reviewer | 복잡도, 메모리, 지연 |

### 보안 리뷰만 실행

```
/oh-my-claudecode:security-review
```

---

## 5. 빌드 에러 자동 수정

```
/oh-my-claudecode:build-fix
```

타입 에러, 빌드 실패를 **최소한의 변경**으로 고쳐줍니다.
아키텍처는 안 건드리고 에러만 잡아요.

---

## 6. 병렬 CLI 활용

### 독립 작업 병렬 처리

터미널을 여러 개 열어서 **동시에 다른 일**을 시킵니다.

```
# 터미널 1
claude
> 프론트엔드 로그인 페이지 만들어줘

# 터미널 2
claude
> 백엔드 인증 API 만들어줘

# 터미널 3
claude
> E2E 테스트 시나리오 작성해줘
```

### 비대화형 모드로 스크립트 자동화

```bash
# CI/CD 파이프라인에서 활용
claude -p "이 PR의 변경사항 리뷰해줘" --output-format json > review.json

# 일괄 처리
for file in src/**/*.ts; do
  claude -p "이 파일의 타입 안전성 검토해줘: $file"
done
```

### OMC의 ultrawork 모드

CLI를 직접 여러 개 열 필요 없이, OMC가 알아서 병렬로 돌려줍니다.

```
ulw 다음 5개 작업을 동시에 처리해줘:
1. UserService 리팩토링
2. OrderService 테스트 작성
3. PaymentService 타입 수정
4. API 문서 업데이트
5. E2E 테스트 추가
```

---

## 자동화 조합 예시

### 일일 개발 루틴

```
[아침]
> 어제 올라온 PR들 리뷰해줘
  → /oh-my-claudecode:code-review

[작업 중]
> 이 기능 TDD로 만들자
  → /oh-my-claudecode:tdd

[작업 완료]
> 커밋하고 PR 올려줘
  → /commit-push-pr

[퇴근 전]
> 오늘 변경사항 정리해줘
  → 자동 요약 생성
```

### 레거시 코드 개선

```
# 1단계: 현재 상태 분석
> 이 모듈 분석해줘. 의존성, 테스트 커버리지, 코드 품질.

# 2단계: 테스트 먼저 추가
> 기존 동작 보존하는 테스트부터 작성해줘

# 3단계: 리팩토링
> 테스트 통과 유지하면서 리팩토링해줘

# 4단계: 리뷰
> /oh-my-claudecode:code-review
```

---

## 실천 체크리스트

- [ ] `/commit` 으로 커밋 메시지 자동 생성 시도
- [ ] `/oh-my-claudecode:code-review` 로 코드 리뷰 실행
- [ ] 터미널 2개 열고 병렬 작업 시도
- [ ] `/oh-my-claudecode:build-fix` 로 빌드 에러 자동 수정 체험
- [ ] 비대화형 모드 (`claude -p`) 한 번 실행해보기

---

## 다음 단계

자동화 방법을 익혔다면 [10. MCP 생태계](./10-mcp-ecosystem.md)에서 외부 도구 연동으로 가능성을 확장하세요.

---

**참고 자료**:

- [Claude Code CLI 레퍼런스](https://code.claude.com/docs/ko/cli-reference)
- [oh-my-claudecode 실행 모드](https://github.com/Yeachan-Heo/oh-my-claudecode#execution-modes)
