# 01. 빠른 시작 가이드

Claude Code 설치하고 환경 세팅하는 법이에요.

## 1단계: Claude Code 설치

### 권장: 공식 설치 스크립트 (자동 업데이트)

**macOS, Linux, WSL:**

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows PowerShell** (관리자 권한):

```powershell
irm https://claude.ai/install.ps1 | iex
```

**Windows CMD** (관리자 권한):

```cmd
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

> **Windows 필수**: [Git for Windows](https://git-scm.com/downloads/win) 설치 후 진행하세요.

!!! tip
    공식 설치 스크립트 쓰면 업데이트도 자동이에요.

### 대안: 패키지 매니저

**Homebrew (macOS):**

```bash
brew install --cask claude-code
```

**WinGet (Windows):**

```cmd
winget install Anthropic.ClaudeCode
```

> **주의**: 패키지 매니저로 설치하면 수동 업데이트가 필요합니다.
> - Homebrew: `brew upgrade claude-code`
> - WinGet: `winget upgrade Anthropic.ClaudeCode`

### 설치 확인

```bash
claude --version
```

---

### 지원 환경

- **VS Code** (권장) - 기본 IDE
- **Desktop App** - 독립형 애플리케이션
- **Web** - 브라우저에서 직접 사용
- **JetBrains IDE** - IntelliJ, PyCharm 등
- **Cursor, Windsurf** 등 다른 에디터도 지원

### 계정 설정

Claude Code를 사용하려면 다음 중 하나가 필요해요:

- **Claude Pro/Max/Teams/Enterprise** - claude.com 계정
- **Claude Console** - API 키 기반 (pay-as-you-go)
- **Anthropic Bedrock** - AWS 환경
- **Google Vertex AI** - Google Cloud 환경
- **Third-party** - Foundry 등 다른 제공자

## 2단계: 공식 문서 참조

> **Tip**: 직접 읽을 필요 없어요.
> Claude한테 "이 문서 읽고 요약해줘" 던지면 됩니다.

**공식 문서**: https://code.claude.com/docs/en/overview

```
이 문서 읽고 핵심 기능 5가지로 요약해줘
https://code.claude.com/docs/en/overview
```

---

## 3단계: oh-my-claudecode 플러그인 설치 (강력 추천)

[oh-my-claudecode](https://github.com/Yeachan-Heo/oh-my-claudecode)는 Claude Code에 **멀티 에이전트 기능**을 얹어주는 플러그인이에요.

### 왜 oh-my-claudecode인가?

| 기능                     | 설명                                                                              |
| ------------------------ | --------------------------------------------------------------------------------- |
| **5가지 실행 모드**      | Autopilot(자율), Ultrapilot(병렬), Swarm(협업), Pipeline(순차), Ecomode(토큰절약) |
| **32개 전문 에이전트**   | 분석, 실행, 설계, 검토 등 도메인별 전문가                                         |
| **37개 스킬**            | 자동화, 최적화, 학습 기능 내장                                                    |
| **31개 라이프사이클 훅** | 상황별 자동 대응                                                                  |
| **토큰 30-50% 절약**     | 스마트 모델 라우팅                                                                |

### 설치 방법

```bash
# Claude Code 실행 후
/plugin marketplace add https://github.com/Yeachan-Heo/oh-my-claudecode

# 플러그인 설치
/plugin install oh-my-claudecode
```

### deep-init 실행 (첫 사용 시 필수)

프로젝트 처음 시작할 때 실행하면 **코드베이스 문서를 자동으로 만들어줘요.**

```
/oh-my-claudecode:deep-init
```

이 명령은:

- 프로젝트 구조를 쭉 훑어서 인덱싱
- AGENTS.md 파일 생성 (디렉토리별 컨텍스트)
- 이후 검색이나 분석할 때 기반이 됨

---

## 4단계: MCP 설정

### oh-my-claudecode 사용 시

```
/oh-my-claudecode:mcp-setup
```

추천 MCP 서버들이 한 번에 세팅돼요.

### oh-my-claudecode를 사용하지 않는 경우

**Context7은 필수로 설치하세요!**

Context7은 라이브러리 최신 문서를 실시간으로 땡겨오는 MCP 서버예요.

```bash
# ~/.claude.json에 추가
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@context7/mcp-server"]
    }
  }
}
```

### Context7 사용 예시

```
# 라이브러리 최신 문서 조회
React 19의 새로운 훅들에 대해 Context7로 찾아봐

# 특정 버전 문서 확인
FastAPI 0.115 버전의 변경사항 알려줘
```

---

## 5단계: 프로젝트 설정 (CLAUDE.md)

프로젝트 루트에 `CLAUDE.md`를 만들면 Claude가 프로젝트 맥락을 바로 파악해요.

```markdown
# 프로젝트 컨텍스트

## 기술 스택

- 언어: Python 3.11
- 프레임워크: FastAPI
- 데이터베이스: PostgreSQL

## 코딩 컨벤션

- PEP 8 준수
- docstring은 Google 스타일

## 주의사항

- /config 폴더의 파일은 수정하지 말 것
- 테스트는 pytest로 실행

## 자주 사용하는 명령어

- `make test` - 테스트 실행
- `make lint` - 린트 검사
```

---

## 유용한 명령어 요약

| 명령어         | 설명                       |
| -------------- | -------------------------- |
| `/help`        | 도움말                     |
| `/mcp`         | MCP 서버 상태 확인         |
| `/plugin list` | 설치된 플러그인 목록       |
| `/compact`     | 대화 요약 후 컨텍스트 정리 |
| `Ctrl+C`       | 현재 작업 중단             |

---

## 다음 단계

환경 설정이 끝났으면 [02. AI 마인드셋](./02-ai-mindset.md)에서 AI를 잘 쓰는 관점부터 잡아보세요.

---

**참고 자료**:

- [oh-my-claudecode GitHub](https://github.com/Yeachan-Heo/oh-my-claudecode)
- [Claude Code 공식 문서](https://code.claude.com/docs/en/overview)
