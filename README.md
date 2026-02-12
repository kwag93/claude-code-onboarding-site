# Claude Code 온보딩 가이드

> AI 페어 프로그래밍으로 개발 생산성을 높이기 위한 팀 온보딩 문서

[![Deploy](https://github.com/kwag93/claude-code-onboarding-site/actions/workflows/deploy.yml/badge.svg)](https://github.com/kwag93/claude-code-onboarding-site/actions/workflows/deploy.yml)
[![MkDocs Material](https://img.shields.io/badge/MkDocs-Material-blue?logo=materialformkdocs)](https://squidfunk.github.io/mkdocs-material/)
[![GitHub Pages](https://img.shields.io/badge/Hosted_on-GitHub_Pages-222?logo=githubpages)](https://kwag93.github.io/claude-code-onboarding-site/)

**[사이트 바로가기 →](https://kwag93.github.io/claude-code-onboarding-site/)**

---

## 이 가이드는

Claude Code를 처음 접하는 팀원이 **2.5시간 안에** 실무에 적용할 수 있도록 만든 온보딩 문서입니다.

- "해줘"가 아니라 **질문**하는 방법
- AI가 결정하는 게 아니라 **내가 결정**하는 습관
- 도구를 쓰면서도 **실력이 느는** 워크플로우

---

## 문서 구성

| 카테고리 | 문서 | 내용 |
|----------|------|------|
| **시작하기** | [01. 빠른 시작](https://kwag93.github.io/claude-code-onboarding-site/latest/01-quick-start/) | 설치, oh-my-claudecode, MCP 설정 |
| | [02. AI 마인드셋](https://kwag93.github.io/claude-code-onboarding-site/latest/02-ai-mindset/) | 도서관 사서 비유, 인지 오프로딩 위험 |
| **실무 가이드** | [03. 워크플로우](https://kwag93.github.io/claude-code-onboarding-site/latest/03-workflows/) | 에이전트, 스킬, 훅 실무 예제 |
| | [06. 에이전트 팀과 코워크](https://kwag93.github.io/claude-code-onboarding-site/latest/06-agent-teams-cowork/) | 팀 조율, 코워크, 토큰 비용 |
| | [07. 왜 CLI인가](https://kwag93.github.io/claude-code-onboarding-site/latest/07-why-cli/) | CLI 장점, 이미지 활용, 멀티 인스턴스 |
| | [08. OMC 활용 가이드](https://kwag93.github.io/claude-code-onboarding-site/latest/08-omc-guide/) | autopilot, learner, HUD, 실행 모드 |
| | [09. 자동화의 힘](https://kwag93.github.io/claude-code-onboarding-site/latest/09-automation/) | git, PR, 테스트, 코드 리뷰 자동화 |
| **확장하기** | [10. MCP 생태계](https://kwag93.github.io/claude-code-onboarding-site/latest/10-mcp-ecosystem/) | Context7, Figma MCP, Jira MCP |
| | [11. 스킬과 플러그인](https://kwag93.github.io/claude-code-onboarding-site/latest/11-skills-plugins/) | humanizer, 직접 만들기, 서브에이전트 |
| **철학** | [12. AI와 함께 일하기](https://kwag93.github.io/claude-code-onboarding-site/latest/12-ai-work-philosophy/) | 찾지 말고 물어보기, 메모리 관리 |
| | [04. 나아가는 방법](https://kwag93.github.io/claude-code-onboarding-site/latest/04-evolving/) | 메모리, 룰, 스킬 업데이트 |
| | [05. 요청하는 방법](https://kwag93.github.io/claude-code-onboarding-site/latest/05-prompt-tips/) | 보여주기 중심 요청법 |

---

## 권장 학습 순서

```
1일차 (30분)   환경 구축 + 관점 전환     → 01, 02
2일차 (40분)   실무 적용                 → 03, 05
3일차 (50분)   심화 기능                 → 07, 08, 09
4일차 (30분)   확장 + 습관화             → 06, 10, 11, 12, 04
```

---

## 로컬 개발

```bash
# 가상환경 생성
python -m venv .venv
source .venv/bin/activate

# 의존성 설치
pip install -r requirements.txt

# 로컬 서버 (http://127.0.0.1:8000)
mkdocs serve

# 빌드 테스트
mkdocs build
```

## 배포

`main` 브랜치에 push하면 GitHub Actions가 자동 배포합니다.

수동으로 특정 버전을 배포하려면:
1. Actions 탭 → **Deploy MkDocs** → Run workflow
2. 버전 입력 (예: `v1.2`)

---

## 기술 스택

- [MkDocs Material](https://squidfunk.github.io/mkdocs-material/) - 문서 프레임워크
- [mike](https://github.com/jimporter/mike) - 다중 버전 관리
- [GitHub Pages](https://pages.github.com/) - 호스팅
- [GitHub Actions](https://github.com/features/actions) - CI/CD

## 프로젝트 구조

```
├── .github/workflows/deploy.yml   # 자동 배포 워크플로우
├── docs/
│   ├── index.md                   # 홈페이지
│   ├── 01-quick-start.md          # ~ 12-ai-work-philosophy.md (12개 문서)
│   ├── 99-release-notes.md        # 버전 변경 내역
│   ├── 404.md                     # 404 페이지
│   └── stylesheets/extra.css      # 커스텀 스타일
├── mkdocs.yml                     # MkDocs 설정
└── requirements.txt               # Python 의존성
```

---

## 라이선스

이 프로젝트의 문서는 내부 팀 교육용으로 작성되었습니다.

---

_AI로 빨라졌다고 실력까지 는 건 아닙니다. 하지만 관점만 제대로 잡으면 최고의 동료가 됩니다._
