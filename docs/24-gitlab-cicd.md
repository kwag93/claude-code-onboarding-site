# 24. GitLab CI/CD에서 Claude Code 활용하기

GitLab CI/CD에 Claude Code를 연결하면 이슈를 코드로 바꾸고, MR 검토를 자동화할 수 있어요.

---

## 왜 GitLab CI/CD + Claude Code?

### 핵심 기능

| 기능 | 설명 |
|------|------|
| **이슈 → MR** | 이슈 설명을 읽고 자동으로 구현 제안 생성 |
| **@claude 멘션** | MR/댓글에서 Claude가 직접 코드 작성 |
| **CLAUDE.md 준수** | 프로젝트 지침 자동 적용 |
| **안전한 격리** | 컨테이너에서 독립적으로 실행 |
| **AI 모델 선택** | Anthropic / AWS Bedrock / Google Vertex AI |

### 작동 방식

```
[Git 이벤트]
    ↓
[GitLab Runner 감지]
    ↓
[Claude Code 실행]
    ↓
[MR 생성 또는 업데이트]
```

---

## 빠른 설정 (3단계)

### Step 1: API 키 등록

**Anthropic Claude 사용 시:**

1. GitLab 프로젝트 → **Settings** → **CI/CD** → **Variables**
2. **+ Add variable** 클릭
3. 키: `ANTHROPIC_API_KEY`
4. 값: Claude API 키 ([여기서 발급](https://console.anthropic.com/account/keys))
5. **Protect variable** 체크
6. **Mask variable** 체크 ✅

```
마스킹하면 로그에 키가 찍히지 않아요.
```

### Step 2: `.gitlab-ci.yml` 작성

프로젝트 루트에 `.gitlab-ci.yml`을 추가하세요:

```yaml
stages:
  - ai

claude:
  stage: ai
  image: node:24-alpine3.21
  rules:
    - if: '$CI_PIPELINE_SOURCE == "web"'
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
  variables:
    GIT_STRATEGY: fetch
  before_script:
    - apk update
    - apk add --no-cache git curl bash
    - curl -fsSL https://claude.ai/install.sh | bash
  script:
    - /bin/gitlab-mcp-server || true
    - >
      claude
      -p "${AI_FLOW_INPUT:-'Review this MR and implement the requested changes'}"
      --permission-mode acceptEdits
      --allowedTools "Bash Read Edit Write mcp__gitlab"
      --debug
```

### Step 3: 파이프라인 실행

MR 생성 또는 이슈에서 **@claude** 멘션:

```
@claude implement the user authentication feature described in this issue
```

MR/댓글에 멘션하는 순간 파이프라인이 자동으로 떠요.

---

## 사용 사례

### 이슈 자동 구현

```
이슈: "사용자 프로필 페이지 구현"
↓
@claude implement this feature based on the issue description
↓
Claude가 컴포넌트 작성 → MR 생성 → 제안
```

### MR 검토 및 개선

```
@claude suggest a concrete approach to optimize the database query
↓
Claude가 성능 분석 및 개선안 제시
```

### 버그 빠른 수정

```
@claude fix the TypeError in the checkout component
↓
Claude가 근본 원인 파악 및 수정
```

---

## AWS Bedrock 설정 (옵션)

API 키 없이 OIDC 기반 인증으로 연결하는 방식이에요.

### Step 1: AWS IAM 설정

1. AWS 콘솔 → **IAM** → **Identity providers**
2. **OpenID Connect** 추가:
   - Provider URL: `https://gitlab.com`
   - Audience: `https://gitlab.com`

3. IAM 역할 생성 (`GitLabCIRole`):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::YOUR_ACCOUNT:oidc-provider/gitlab.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "gitlab.com:aud": "https://gitlab.com"
        }
      }
    }
  ]
}
```

4. 역할에 정책 추가:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "bedrock:InvokeModel",
        "bedrock:InvokeModelWithResponseStream"
      ],
      "Resource": "arn:aws:bedrock:*:*:foundation-model/*"
    }
  ]
}
```

### Step 2: GitLab CI 설정

`.gitlab-ci.yml`:

```yaml
stages:
  - ai

claude-bedrock:
  stage: ai
  image: node:24-alpine3.21
  id_tokens:
    AWS_OIDC_TOKEN:
      aud: https://gitlab.com
  variables:
    GIT_STRATEGY: fetch
    AWS_ROLE_TO_ASSUME: "arn:aws:iam::YOUR_ACCOUNT:role/GitLabCIRole"
    AWS_REGION: "us-west-2"
  before_script:
    - apk add --no-cache git curl bash
    - curl -fsSL https://claude.ai/install.sh | bash
    - |
      # AWS STS 토큰 획득
      AWS_CREDS=$(aws sts assume-role-with-web-identity \
        --role-arn $AWS_ROLE_TO_ASSUME \
        --role-session-name gitlab-ci \
        --web-identity-token $AWS_OIDC_TOKEN \
        --query 'Credentials.[AccessKeyId,SecretAccessKey,SessionToken]' \
        --output text)
      export AWS_ACCESS_KEY_ID=$(echo $AWS_CREDS | awk '{print $1}')
      export AWS_SECRET_ACCESS_KEY=$(echo $AWS_CREDS | awk '{print $2}')
      export AWS_SESSION_TOKEN=$(echo $AWS_CREDS | awk '{print $3}')
  script:
    - /bin/gitlab-mcp-server || true
    - >
      claude
      -p "${AI_FLOW_INPUT:-'Review this MR'}"
      --model bedrock.anthropic.claude-3-5-sonnet
      --permission-mode acceptEdits
      --allowedTools "Bash Read Edit Write mcp__gitlab"
```

---

## Google Vertex AI 설정 (옵션)

Google Cloud Workload Identity Federation 방식이에요.

### Step 1: Google Cloud 설정

1. Google Cloud 콘솔 → **IAM & Admin** → **Workload Identity Federation**
2. **Create pool** 및 **provider** 생성:
   - Provider: `GitLab`
   - OIDC issuer: `https://gitlab.com`
   - Audience: `https://iam.googleapis.com/projects/YOUR_PROJECT_NUMBER/locations/global/workloadIdentityPools/gitlab-pool/providers/gitlab`

3. Service Account 생성:
   - 역할: `Vertex AI User`
   - IAM 바인딩: `Service Account User`

### Step 2: GitLab CI 설정

`.gitlab-ci.yml`:

```yaml
stages:
  - ai

claude-vertex:
  stage: ai
  image: node:24-alpine3.21
  id_tokens:
    GCP_OIDC_TOKEN:
      aud: https://iam.googleapis.com/projects/YOUR_PROJECT_NUMBER/locations/global/workloadIdentityPools/gitlab-pool/providers/gitlab
  variables:
    GIT_STRATEGY: fetch
    GCP_WORKLOAD_IDENTITY_PROVIDER: "projects/YOUR_PROJECT_NUMBER/locations/global/workloadIdentityPools/gitlab-pool/providers/gitlab"
    GCP_SERVICE_ACCOUNT: "gitlab-ci@YOUR_PROJECT.iam.gserviceaccount.com"
    CLOUD_ML_REGION: "us-central1"
  before_script:
    - apk add --no-cache git curl bash jq
    - curl -fsSL https://claude.ai/install.sh | bash
    - |
      # Google Cloud 토큰 교환
      GCP_ACCESS_TOKEN=$(curl -X POST \
        https://iam.googleapis.com/v1/projects/-/locations/global/workloadIdentityPools/gitlab-pool/providers/gitlab:generateAccessToken \
        -H "Content-Type: application/json" \
        -d "{
          \"grant_type\": \"urn:ietf:params:oauth:grant-type:token-exchange\",
          \"subject_token\": \"$GCP_OIDC_TOKEN\",
          \"subject_token_type\": \"urn:ietf:params:oauth:token-type:id_token\",
          \"requested_token_use\": \"access\"
        }" | jq -r '.access_token')
      export GOOGLE_APPLICATION_CREDENTIALS=$(mktemp)
      echo "{\"type\": \"external_account\", \"audience\": \"$GCP_WORKLOAD_IDENTITY_PROVIDER\", \"service_account_impersonation_url\": \"https://iamcredentials.googleapis.com/v1/projects/-/serviceAccounts/$GCP_SERVICE_ACCOUNT:generateAccessToken\"}" > $GOOGLE_APPLICATION_CREDENTIALS
  script:
    - /bin/gitlab-mcp-server || true
    - >
      claude
      -p "${AI_FLOW_INPUT:-'Review this MR'}"
      --model vertex-claude-3-5-sonnet
      --permission-mode acceptEdits
      --allowedTools "Bash Read Edit Write mcp__gitlab"
```

---

## 모범 사례

### 1. CLAUDE.md로 지침 정의

프로젝트 루트에 `.claude/CLAUDE.md`를 두면 돼요:

```markdown
# 프로젝트 코딩 표준

## 언어: TypeScript
## 프레임워크: React + Vite
## 스타일: Tailwind CSS

## 금지사항
- console.log 커밋 금지
- any 타입 사용 금지
- 임시 변수명 사용 금지
```

Claude는 이 파일을 자동으로 읽고 지침을 따라요.

### 2. API 키 보안

```yaml
# ✅ 올바름
variables:
  ANTHROPIC_API_KEY:
    value: $ANTHROPIC_API_KEY
    protected: true
    masked: true

# ❌ 잘못됨 - 절대 하드코딩하지 말 것
script:
  - export ANTHROPIC_API_KEY="sk-ant-..."
```

### 3. 작업 시간 초과 설정

```yaml
claude:
  stage: ai
  timeout: 30 minutes
  variables:
    GIT_STRATEGY: fetch
```

### 4. 비용 관리

| 항목 | 비용 |
|------|------|
| GitLab Runner (시간) | ~$0.50/시간 |
| Claude API (토큰) | ~$3/100만 토큰 |
| 월 예상치 | $100~500 |

**비용 줄이려면:**
- 꼭 필요한 상황에만 @claude 멘션하세요
- rules 조건으로 불필요한 파이프라인 실행을 걸러내세요
- 로그를 주기적으로 봐서 이상 실행이 없는지 확인하세요

---

## 문제 해결

### @claude가 응답하지 않음

| 확인 사항 | 해결 방법 |
|---------|---------|
| 파이프라인 실행 안 됨 | GitLab → **CI/CD** → **Pipelines** 확인 |
| `rules` 미설정 | `.gitlab-ci.yml`의 `if` 조건 확인 |
| 트리거 안 됨 | MR/댓글에서 **@claude** 정확히 입력 |

### MR 생성 실패

| 문제 | 원인 | 해결 |
|------|------|------|
| Permission denied | 토큰 권한 부족 | `CI_JOB_TOKEN` 또는 PAT 권한 확인 |
| 커밋할 파일 없음 | 변경사항 미생성 | Claude 프롬프트 구체화 |
| 403 Forbidden | API 키 만료 | 새 키로 변수 업데이트 |

### 인증 오류

```
Error: ANTHROPIC_API_KEY not found
```

**해결:**
1. 변수가 **마스킹** 되어 있는지 확인
2. 프로젝트 설정에서 CI/CD 변수 재확인
3. Runner가 해당 변수를 볼 수 있는지 확인 (Protected branch 범위 주의)

---

## 다음 단계

1. ✅ `.gitlab-ci.yml` 작성 및 커밋
2. ✅ API 키를 변수로 등록
3. ✅ MR/이슈에서 **@claude** 멘션 테스트
4. ✅ 첫 번째 자동 생성 MR 검토

---

## 참고 자료

- [GitLab CI/CD 공식 문서](https://docs.gitlab.com/ee/ci/)
- [Claude API 가이드](https://docs.anthropic.com/)
- [AWS Bedrock + GitLab 통합](https://docs.aws.amazon.com/bedrock/latest/userguide/)
- [Google Vertex AI + GitLab](https://cloud.google.com/docs/authentication/workload-identity-federation)

---

> **⚠️ 베타 기능**
>
> GitLab CI/CD 통합은 아직 베타예요. 프로덕션에 붙이기 전에 충분히 테스트해보세요.
