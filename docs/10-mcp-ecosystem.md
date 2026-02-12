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
# Claude Code에서 직접 추가
claude mcp add context7 -- npx -y @upstash/context7-mcp

# 또는 ~/.claude.json에 수동 추가
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

| 유형 | 주소 | 요구사항 |
| --- | --- | --- |
| **데스크톱 MCP** | `http://127.0.0.1:3845/mcp` | Figma 데스크톱 앱 + Dev/Full 시트 |
| **원격 MCP** | `https://mcp.figma.com/mcp` | 모든 요금제 (월 6회 제한 있음) |

### 설치 방법

1. Figma 데스크톱 앱 최신 버전 설치
2. Dev Mode 전환
3. "데스크톱 MCP 서버 활성화" 클릭

### Claude Code에 연동

```json
{
  "mcpServers": {
    "figma": {
      "url": "http://127.0.0.1:3845/mcp"
    }
  }
}
```

### 사용 예시

```
# Figma에서 프레임 선택 후
이 디자인대로 React 컴포넌트 만들어줘

# 링크로 직접 전달
이 Figma 프레임을 Tailwind CSS로 구현해줘:
https://figma.com/design/xxx/yyy?node-id=1-2

# 디자인 시스템 변수 활용
이 컴포넌트의 디자인 변수 확인하고, CSS 변수로 매핑해줘
```

### 활용 팁

- **선택 기반** (데스크톱): Figma에서 프레임 선택 → Claude에 요청
- **링크 기반**: node-id 포함된 URL을 Claude에 전달
- 디자인 시스템의 변수(색상, 간격 등)도 자동으로 추출됩니다

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

### 전체 설정 예시 (~/.claude.json)

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
- [ ] Jira MCP 연동 (이슈 트래커 사용하는 경우)
- [ ] `/mcp` 명령으로 연결 상태 확인

---

## 다음 단계

MCP 생태계를 이해했다면 [11. 스킬과 플러그인 생태계](./11-skills-plugins.md)에서 Claude Code를 직접 확장하는 방법을 알아보세요.

---

**참고 자료**:

- [Context7 공식 사이트](https://context7.com)
- [Figma MCP 서버 가이드](https://help.figma.com/hc/ko/articles/32132100833559)
- [MCP Atlassian GitHub](https://github.com/sooperset/mcp-atlassian)
- [MCP 프로토콜 명세](https://modelcontextprotocol.io)
