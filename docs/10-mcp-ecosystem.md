# 10. MCP 생태계

Claude Code에 외부 도구를 붙여 쓰는 MCP(Model Context Protocol) 사용법입니다.

---

## MCP란?

MCP는 AI에 **외부 도구를 붙이는 표준 프로토콜**입니다.

```
[Claude Code] ←→ [MCP 서버] ←→ [외부 서비스]
                                  ├─ Figma
                                  ├─ Jira/Confluence
                                  ├─ Context7 (문서)
                                  ├─ 파일시스템
                                  └─ 웹 검색
```

MCP를 쓰면 Claude가 **직접** 외부 서비스 데이터를 읽고 조작할 수 있어요.

### 설치 (OMC 사용 시)

```
/oh-my-claudecode:mcp-setup
```

추천 MCP 서버를 자동으로 구성해줍니다.

---

## Context7: 최신 문서 접근

### 왜 필요한가?

AI 모델은 학습 시점 이후 정보를 모릅니다.
Context7이 **라이브러리 최신 버전 문서와 코드 예제**를 실시간으로 가져다줘요.

```
[문제] Claude가 React 19 문법을 모르고 React 18 방식으로 코드 작성
         ↓
[해결] Context7이 React 19 최신 문서를 실시간 주입
         ↓
[결과] 정확한 최신 문법으로 코드 생성
```

### 설치 방법

```bash
# Claude Code에서 직접 추가 (권장)
claude mcp add context7 -- npx -y @upstash/context7-mcp

# 또는 프로젝트 루트의 .mcp.json에 수동 추가
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    }
  }
}
```

### 사용 예시

```
# 최신 문서 기반으로 코드 작성
React 19의 use() 훅 사용법 알려줘. use context7

# 특정 라이브러리 문서 조회
Next.js 15 앱 라우터 설정 방법. use context7

# 마이그레이션 가이드
Tailwind CSS v4 마이그레이션 방법 알려줘. use context7
```

### Context7에서 미리 만들어진 문서 활용

[context7.com](https://context7.com)에서 다양한 라이브러리의 문서를 확인할 수 있습니다.

```
# Claude Code 공식 문서도 Context7에 있습니다
https://context7.com/anthropics/claude-code

# 사용 예시
Claude Code에서 에이전트 팀 사용하는 방법. use context7
```

### CLAUDE.md에 자동 호출 설정

프로젝트의 `CLAUDE.md`에 다음을 추가하면 자동으로 Context7을 사용합니다:

```markdown
## 문서 참조
라이브러리/API 문서, 코드 생성, 설정이 필요할 때 Context7 MCP를 사용하세요.
```

---

## Figma MCP: 디자인에서 코드로

### 왜 필요한가?

디자이너의 Figma 파일을 보고 **바로 코드를 뽑아낼** 수 있습니다.
스크린샷 찍어서 붙여넣는 것보다 훨씬 정확해요.

```
[Figma 디자인] → [Figma MCP] → [Claude Code] → [정확한 코드]
                                      ↑
                              변수, 컴포넌트, 레이아웃
                              데이터를 구조화해서 전달
```

### 두 가지 서버 유형

| 구분 | 데스크톱 MCP | 원격 MCP |
| --- | --- | --- |
| **주소** | `http://127.0.0.1:3845/mcp` | `https://mcp.figma.com/mcp` |
| **요금제** | Dev/Full 시트 (유료) | 모든 요금제 |
| **호출 제한** | 분당 제한 (REST API Tier 1) | 스타터: 월 6회 |
| **선택 기반** | O (프레임 직접 선택) | X |
| **링크 기반** | O | O |
| **인증** | 로컬 자동 | OAuth / Personal Access Token |

### 설치 방법 (데스크톱)

1. Figma 데스크톱 앱 최신 버전 설치
2. Design 파일 열기
3. Dev Mode 전환 (`Shift+D`)
4. 검사 패널 → MCP 서버 섹션 → **"데스크톱 MCP 서버 활성화"** 클릭

### Claude Code에 연동

```json
// 데스크톱 MCP (추천)
{
  "mcpServers": {
    "figma": {
      "url": "http://127.0.0.1:3845/mcp"
    }
  }
}
```

```json
// 원격 MCP (데스크톱 앱 없이 사용 시)
{
  "mcpServers": {
    "figma": {
      "url": "https://mcp.figma.com/mcp"
    }
  }
}
```

### 컨텍스트 전달 방식

**선택 기반** (데스크톱 전용)

```
# Figma에서 프레임 선택 후
이 디자인대로 React 컴포넌트 만들어줘
```

**링크 기반** (양쪽 모두)

```
# node-id 포함된 URL을 전달
이 Figma 프레임을 Tailwind CSS로 구현해줘:
https://figma.com/design/xxx/yyy?node-id=1-2

# 디자인 시스템 변수 활용
이 컴포넌트의 디자인 변수 확인하고, CSS 변수로 매핑해줘
```

### Code Connect

Figma 컴포넌트와 **실제 코드베이스 컴포넌트를 매핑**하는 기능입니다. Code Connect를 설정하면 MCP가 생성하는 코드에서 디자인 시스템 컴포넌트를 자동으로 재사용해요.

```
[Figma 컴포넌트] ←→ [Code Connect 매핑] ←→ [코드베이스 컴포넌트]
                                                ↓
                                        실제 import로 코드 생성
```

### 활용 팁

- **선택 기반** (데스크톱): Figma에서 프레임 선택 → Claude에 요청이 가장 빠릅니다
- **링크 기반**: node-id 포함된 URL을 Claude에 전달
- 디자인 시스템의 변수(색상, 간격 등)도 자동으로 추출됩니다
- Code Connect 설정 시 디자인 시스템 일관성이 크게 향상됩니다

---

## Google Stitch: 텍스트로 UI 만들기

### 왜 필요한가?

Google의 Stitch는 텍스트 설명만으로 **전문가급 UI 화면을 생성**하는 AI 디자인 도구입니다. 디자이너 없이도 프로토타입을 빠르게 뽑아볼 수 있어요.

```
[텍스트 설명] → [Stitch MCP] → [UI 화면 생성]
                                      ↑
                              프로젝트 관리, 화면 편집,
                              코드 추출까지 지원
```

### 설치 방법

Stitch MCP는 Google Cloud 기반으로 동작합니다.

**1단계: API 키 발급**

[stitch.withgoogle.com](https://stitch.withgoogle.com)에서 MCP 연결 시 API 키를 발급받을 수 있습니다.

**2단계: Claude Code 설정**

```json
{
  "mcpServers": {
    "stitch": {
      "url": "https://stitch.withgoogle.com/mcp",
      "env": {
        "STITCH_API_KEY": "your_api_key"
      }
    }
  }
}
```

> API 키 방식이 가장 간단합니다. OAuth 방식도 지원하지만 매시간 토큰 갱신이 필요해요.

### 사용 예시

```
# 대시보드 화면 만들기
사용자 통계, 최근 활동, 알림 패널이 있는 관리자 대시보드 화면 만들어줘

# 로그인 폼 UI 생성
이메일/비밀번호 입력, 소셜 로그인 버튼이 있는 로그인 화면 만들어줘

# 기존 화면 수정
방금 만든 대시보드에 다크 모드 토글 버튼 추가해줘
```

### 주요 도구

| 도구 | 설명 |
| --- | --- |
| `build_site` | 프로젝트의 화면을 라우트로 매핑해 사이트 코드 생성 |
| `get_screen_code` | 특정 화면의 HTML 코드 추출 |
| `get_screen_image` | 화면 스크린샷을 base64 이미지로 추출 |

### Agent Skills (확장 기능)

Stitch는 모듈식 Agent Skills도 지원합니다.

- **Design MD**: 마크다운 형식의 디자인 문서 자동 생성
- **React Components**: Stitch 디자인을 프로덕션 React 코드로 변환 (디자인 토큰 유지)

### 활용 팁

- **프로토타입 우선**: 기획 단계에서 실제 디자인 전에 빠르게 구조를 잡을 때 씁니다
- **코드 추출**: `get_screen_code`로 생성된 UI의 HTML을 바로 가져와 프로젝트에 적용할 수 있어요
- VS Code, Cursor, Claude Code, Gemini CLI 등 다양한 에디터를 지원합니다

---

## Agent Skills Directory: 검증된 스킬 마켓

### 왜 필요한가?

Vercel이 운영하는 [skills.sh](https://skills.sh)는 AI 에이전트를 위한 **재사용 가능한 스킬 마켓플레이스**입니다. 한 줄 명령어로 검증된 모범 사례를 프로젝트에 바로 적용할 수 있어요.

```
[skills.sh] → npx 설치 → [프로젝트 CLAUDE.md에 스킬 추가]
                                ↓
                    Claude가 해당 분야 전문가처럼 동작
```

### 설치 방법

```bash
# 기본 설치 명령
npx skills add <owner/repo>

# 예시: Vercel React 모범 사례
npx skills add vercel-labs/skills
```

### 인기 스킬 TOP 5

| 스킬 | 출처 | 설명 |
| --- | --- | --- |
| `find-skills` | vercel-labs | 필요한 스킬 검색 도구 |
| `vercel-react-best-practices` | vercel-labs | React 모범 사례 |
| `web-design-guidelines` | vercel-labs | 웹 디자인 가이드라인 |
| `frontend-design` | anthropics | 프론트엔드 디자인 패턴 |
| `skill-creator` | anthropics | 커스텀 스킬 만들기 |

### 지원 카테고리

프론트엔드(React, Vue, Svelte), 백엔드(Node.js, Python, Go), 클라우드(AWS, Azure), DevOps, 디자인 시스템, 데이터, 마케팅 등 폭넓은 카테고리를 지원합니다.

### 지원 에이전트

Claude Code, GitHub Copilot, Cursor, Windsurf, Gemini 등 15개 이상의 AI 에이전트를 지원합니다.

### 활용 팁

- **프로젝트 시작 시**: `find-skills`로 프로젝트에 맞는 스킬을 검색하세요
- **팀 표준화**: 팀 전체가 동일한 스킬셋을 사용하면 코드 품질이 일관됩니다
- **커스텀 스킬**: `skill-creator`로 팀만의 스킬을 만들어 공유할 수 있어요

---

## Jira/Confluence MCP: 이슈 관리

### 왜 필요한가?

브라우저 안 열고 **Claude 안에서 바로** Jira 이슈를 관리할 수 있습니다.

```
> SPOTD-123 이슈 내용 보여줘

Claude: "SPOTD-123: 로봇 위치 추적 기능 추가
상태: In Progress
담당자: 김병호
설명: GPS 좌표 실시간 업데이트..."

> 이 이슈 기반으로 코드 작성하고, 완료되면 상태 변경해줘
```

### 설치 방법

```bash
# Python 3.12 이상 필요
pip install mcp-atlassian

# 또는 uvx (권장)
uvx mcp-atlassian
```

### Claude Code 설정

```json
{
  "mcpServers": {
    "mcp-atlassian": {
      "command": "uvx",
      "args": ["mcp-atlassian"],
      "env": {
        "JIRA_URL": "https://your-company.atlassian.net",
        "JIRA_USERNAME": "your.email@company.com",
        "JIRA_API_TOKEN": "your_api_token",
        "CONFLUENCE_URL": "https://your-company.atlassian.net/wiki",
        "CONFLUENCE_USERNAME": "your.email@company.com",
        "CONFLUENCE_API_TOKEN": "your_api_token"
      }
    }
  }
}
```

> **API 토큰 발급**: https://id.atlassian.com/manage-profile/security/api-tokens

### 사용 가능한 기능

**Jira:**

```
# 이슈 검색
> 이번 스프린트의 미완료 이슈 보여줘

# 이슈 생성
> 버그 이슈 만들어줘: 로그인 페이지에서 토큰 만료 시 무한 로딩

# 이슈 상태 변경
> SPOTD-456 상태를 "완료"로 변경해줘

# 이슈 기반 개발
> SPOTD-789 이슈 읽고 구현해줘
```

**Confluence:**

```
# 문서 검색
> 우리 팀의 API 설계 가이드 문서 찾아줘

# 문서 작성
> 이 PR의 변경사항으로 기술 문서 작성해줘
```

---

## MCP 설정 한눈에 보기

### 전체 설정 예시 (.mcp.json)

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    },
    "figma": {
      "url": "http://127.0.0.1:3845/mcp"
    },
    "stitch": {
      "url": "https://stitch.withgoogle.com/mcp",
      "env": {
        "STITCH_API_KEY": "your_api_key"
      }
    },
    "mcp-atlassian": {
      "command": "uvx",
      "args": ["mcp-atlassian"],
      "env": {
        "JIRA_URL": "https://your-company.atlassian.net",
        "JIRA_USERNAME": "your.email@company.com",
        "JIRA_API_TOKEN": "your_api_token"
      }
    }
  }
}
```

### MCP 상태 확인

```
/mcp
```

이 명령으로 현재 연결된 MCP 서버 목록과 상태를 확인할 수 있습니다.

---

## 실천 체크리스트

- [ ] Context7 설치하고 "use context7"으로 최신 문서 조회 시도
- [ ] Figma MCP 연동 (디자이너와 협업하는 경우)
- [ ] Google Stitch로 텍스트 설명만으로 UI 화면 생성 시도
- [ ] [skills.sh](https://skills.sh)에서 프로젝트에 맞는 스킬 검색 및 설치
- [ ] Jira MCP 연동 (이슈 트래커 사용하는 경우)
- [ ] `/mcp` 명령으로 연결 상태 확인

---

## 다음 단계

MCP 생태계를 이해했다면 [11. 스킬과 플러그인 생태계](./11-skills-plugins.md)에서 Claude Code를 직접 확장하는 방법을 알아보세요.

---

**참고 자료**:

- [Context7 공식 사이트](https://context7.com)
- [Figma MCP 서버 가이드](https://help.figma.com/hc/ko/articles/32132100833559)
- [Google Stitch](https://stitch.withgoogle.com)
- [Agent Skills Directory](https://skills.sh)
- [MCP Atlassian GitHub](https://github.com/sooperset/mcp-atlassian)
- [MCP 프로토콜 명세](https://modelcontextprotocol.io)
