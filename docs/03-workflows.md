# 03. 실무 워크플로우

Claude Code의 에이전트, 스킬, 훅을 활용한 실무 예제입니다.

---

## 에이전트, 스킬, 훅이란?

Claude Code는 단순한 대화 도구가 아닙니다.
**확장 가능한 시스템**으로, 세 가지 핵심 개념을 이해해야 합니다.

| 개념         | 설명                    | 예시                                      |
| ------------ | ----------------------- | ----------------------------------------- |
| **에이전트** | 특정 도메인의 전문가    | 코드 분석가, 테스트 작성자, 문서화 전문가 |
| **스킬**     | 재사용 가능한 작업 단위 | API 문서 조회, 코드 검색, 린트 실행       |
| **훅**       | 이벤트 기반 자동 실행   | 커밋 전 검사, 파일 저장 시 포맷팅         |

### 왜 중요한가?

- 반복 작업을 자동화
- 도메인 지식을 영구 보존
- 팀 전체가 동일한 워크플로우 공유

---

## 예제: SPOT SDK 서브에이전트 만들기

Boston Dynamics의 SPOT 로봇 SDK를 다루는 전문 에이전트를 만들어봅시다.
이 예제를 통해 **지식 베이스 구축 → 스킬/에이전트 생성**의 전체 과정을 익힐 수 있습니다.

### Step 1: 지식 소스 수집

먼저 AI에게 필요한 자료들을 제공합니다.

```
SPOT SDK 개발을 도와줄 전문 에이전트를 만들려고 해.
다음 자료들을 참고해서 지식 베이스를 구축해줘:

1. 공식 개발자 포털
   https://dev.bostondynamics.com/

2. 릴리즈 노트 (버전별 변경사항)
   https://dev.bostondynamics.com/docs/release_notes

3. 공식 스타일 가이드
   https://dev.bostondynamics.com/docs/protos/style_guide

4. Proto Reference (API 상세)
   https://dev.bostondynamics.com/protos/bosdyn/api/proto_reference
```

### Step 2: Wishful Thinking으로 요구사항 정의

> **Wishful Thinking**: 이상적으로 어떤 동작을 원하는지 먼저 상상하고,
> 그것을 AI에게 설명하는 방식입니다.

```
이 에이전트가 했으면 하는 일들:

1. SPOT API를 사용할 때 공식 스타일 가이드에 맞는지 검증
2. 내 코드가 특정 SDK 버전과 호환되는지 확인
3. 버전 업그레이드 시 어떤 변경이 필요한지 릴리즈 노트에서 찾아줌
4. Proto Reference에서 필요한 API를 빠르게 검색

이런 동작을 하려면 어떤 스킬과 훅이 필요할까?
```

### Step 3: 필요한 지식 추가 요청

AI가 분석한 후 추가 정보가 필요하면 알려줍니다.

```
Claude: "현재 사용 중인 SDK 버전과 타겟 버전이 필요합니다.
또한 예제 코드가 있으면 패턴 분석에 도움이 됩니다."

> 현재 버전은 3.3.0이고 4.0.0으로 업그레이드 예정이야.
> 우리 코드는 여기 있어: [GitHub 링크 또는 로컬 경로]
```

### Step 4: 스킬/에이전트 생성

충분한 정보가 모이면 실제 구현을 요청합니다.

```
좋아, 이제 다음을 만들어줘:

1. spot-style-checker 스킬
   - Proto 스타일 가이드 준수 여부 검사

2. spot-version-advisor 스킬
   - 릴리즈 노트 기반 마이그레이션 가이드 제공

3. pre-commit 훅
   - 커밋 전 스타일 검사 자동 실행
```

### 완성된 워크플로우 예시

```
> SPOT 코드 커밋할게

[pre-commit 훅 자동 실행]
Claude: "스타일 가이드 위반 2건 발견:
- line 45: deprecated API 사용 (v4.0에서 제거됨)
- line 78: proto naming convention 위반

수정할까요?"

> 응, 수정해줘

[자동 수정 후]
Claude: "수정 완료. 릴리즈 노트에 따르면 line 45의 API는
RobotCommand 대신 RobotCommandBuilder를 사용해야 합니다.
변경사항을 적용했습니다."
```

---

## 핵심 워크플로우 패턴

### 패턴 1: 전문가 에이전트 + 지식 베이스

```
[공식 문서] + [릴리즈 노트] + [예제 코드]
           ↓
    [지식 베이스 구축]
           ↓
    [전문 에이전트 생성]
           ↓
    [스킬로 기능 분리]
```

### 패턴 2: 훅을 통한 자동화

| 시점      | 훅         | 동작                     |
| --------- | ---------- | ------------------------ |
| 커밋 전   | pre-commit | 스타일 검사, 테스트 실행 |
| PR 생성   | post-pr    | 자동 리뷰 요청           |
| 파일 저장 | on-save    | 포맷팅, 린트             |

### 패턴 3: 버전 관리와 릴리즈 노트 활용

```
> 이 코드가 v4.0에서 동작할까?

Claude: "릴리즈 노트 확인 결과:
- RobotState.kinematic_state → 제거됨
- 대체: RobotState.mobility_state 사용

마이그레이션 코드를 생성할까요?"
```

---

## 실습: 나만의 서브에이전트 만들기

### 체크리스트

- [ ] **1. 공식 URL 수집** - 개발자 포털, API 문서
- [ ] **2. 릴리즈 노트 확인** - 버전별 변경사항
- [ ] **3. 스타일 가이드 제공** - 코딩 컨벤션
- [ ] **4. 예제 코드 제공** - 공식 또는 내부 코드
- [ ] **5. Wishful Thinking** - 원하는 동작 명확히 정의
- [ ] **6. 필요 추가 지식 확인** - AI에게 부족한 정보 물어보기
- [ ] **7. 스킬/훅 결정** - 어떤 형태가 적합한지 논의
- [ ] **8. 구현 및 테스트**

---

## 다음 단계

워크플로우 구축 방법을 익혔다면 [04. 나아가는 방법](./04-evolving.md)에서 지속적으로 시스템을 개선하는 방법을 알아보세요.

---

**Sources**:

- [Boston Dynamics Developer Portal](https://dev.bostondynamics.com/)
- [SPOT SDK Release Notes](https://dev.bostondynamics.com/docs/release_notes)
- [Proto Style Guide](https://dev.bostondynamics.com/docs/protos/style_guide)
