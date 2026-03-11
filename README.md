# Claude Code 온보딩 가이드

> AI 페어 프로그래밍으로 개발 생산성을 높이기 위한 팀 온보딩 문서

[![Deploy](https://github.com/kwag93/claude-code-onboarding-site/actions/workflows/deploy.yml/badge.svg)](https://github.com/kwag93/claude-code-onboarding-site/actions/workflows/deploy.yml)
[![Zensical](https://img.shields.io/badge/Zensical-blue?logo=materialformkdocs)](https://github.com/zensical/zensical)
[![GitHub Pages](https://img.shields.io/badge/Hosted_on-GitHub_Pages-222?logo=githubpages)](https://kwag93.github.io/claude-code-onboarding-site/)

**[사이트 바로가기 →](https://kwag93.github.io/claude-code-onboarding-site/)**

---

## 이 가이드는

Claude Code를 처음 접하는 팀원이 실무에 적용할 수 있도록 만든 온보딩 문서입니다.

- "해줘"가 아니라 **질문**하는 방법
- AI가 결정하는 게 아니라 **내가 결정**하는 습관
- 도구를 쓰면서도 **실력이 느는** 워크플로우

---

## 문서 구성

| 카테고리 | 문서 | 내용 |
|----------|------|------|
| **시작하기** | [01. 빠른 시작](https://kwag93.github.io/claude-code-onboarding-site/01-quick-start/) | 설치 (macOS/Linux/Windows), oh-my-claudecode, MCP 설정 |
| | [14. 프로젝트 초기화](https://kwag93.github.io/claude-code-onboarding-site/14-init-guide/) | /init으로 프로젝트 세팅 |
| | [02. AI 마인드셋](https://kwag93.github.io/claude-code-onboarding-site/02-ai-mindset/) | 도서관 사서 비유, 인지 오프로딩 위험 |
| **실무 가이드** | [03. 워크플로우](https://kwag93.github.io/claude-code-onboarding-site/03-workflows/) | 에이전트, 스킬, 훅 실무 예제 |
| | [21. 모범 사례](https://kwag93.github.io/claude-code-onboarding-site/21-best-practices/) | 컨텍스트 관리, 검증, 탐색→계획→코딩 |
| | [15. CLAUDE.md 마스터하기](https://kwag93.github.io/claude-code-onboarding-site/15-claude-md-mastery/) | 60줄 원칙, 3가지 계층, @import |
| | [16. Hooks 실전 가이드](https://kwag93.github.io/claude-code-onboarding-site/16-hooks-practical/) | 17가지 이벤트, 4가지 훅 타입, 실전 레시피 9개 |
| | [06. 팀 세션 가이드](https://kwag93.github.io/claude-code-onboarding-site/06-agent-teams-cowork/) | 팀 세션 구성, 서브에이전트 비교, 토큰 비용 |
| | [19. 코워크 가이드](https://kwag93.github.io/claude-code-onboarding-site/19-cowork-guide/) | 코워크 시작법, 엑셀/파워포인트 활용 |
| | [22. 리모트 컨트롤](https://kwag93.github.io/claude-code-onboarding-site/22-remote-control/) | 모바일/브라우저에서 세션 이어서 사용 |
| | [07. 왜 CLI인가](https://kwag93.github.io/claude-code-onboarding-site/07-why-cli/) | CLI 장점, 이미지 활용, 멀티 인스턴스 |
| | [08. OMC 활용 가이드](https://kwag93.github.io/claude-code-onboarding-site/08-omc-guide/) | autopilot, learner, HUD, 실행 모드 |
| | [09. 자동화의 힘](https://kwag93.github.io/claude-code-onboarding-site/09-automation/) | git, PR, 테스트, 코드 리뷰, /loop, Cron 자동화 |
| | [24. GitLab CI/CD](https://kwag93.github.io/claude-code-onboarding-site/24-gitlab-cicd/) | CI/CD 파이프라인 통합, @claude 멘션 |
| | [13. 파워 유저 팁](https://kwag93.github.io/claude-code-onboarding-site/13-power-user-tips/) | 70가지+ 실전 팁, 단축키, 자동화 |
| **확장하기** | [20. 확장 기능 개요](https://kwag93.github.io/claude-code-onboarding-site/20-features-overview/) | 기능 비교표, 컨텍스트 비용, 조합 패턴 |
| | [10. MCP 생태계](https://kwag93.github.io/claude-code-onboarding-site/10-mcp-ecosystem/) | Context7, Figma MCP, Stitch MCP, Jira MCP |
| | [11. 스킬과 플러그인](https://kwag93.github.io/claude-code-onboarding-site/11-skills-plugins/) | humanizer, 직접 만들기, 서브에이전트 |
| | [23. Chrome 확장 프로그램](https://kwag93.github.io/claude-code-onboarding-site/23-chrome-extension/) | 브라우저 자동화, 라이브 디버깅 |
| | [25. Status Line](https://kwag93.github.io/claude-code-onboarding-site/25-statusline-guide/) | 하단 대시보드 커스터마이징 |
| | [26. 키보드 단축키](https://kwag93.github.io/claude-code-onboarding-site/26-keybindings-guide/) | 커스텀 키바인딩, Vim 모드, Chords |
| | [27. 보이스 모드](https://kwag93.github.io/claude-code-onboarding-site/27-voice-mode/) | 음성 코딩, Push-to-Talk, Hands-Free |
| | [18. 추천 리소스](https://kwag93.github.io/claude-code-onboarding-site/18-resources/) | 공식 문서, 한국어 가이드, 커뮤니티 허브 |
| **철학** | [12. AI와 함께 일하기](https://kwag93.github.io/claude-code-onboarding-site/12-ai-work-philosophy/) | 찾지 말고 물어보기, 메모리 관리 |
| | [17. 흔한 실수 피하기](https://kwag93.github.io/claude-code-onboarding-site/17-anti-patterns/) | 8가지 안티패턴과 해결법 |
| | [04. 나아가는 방법](https://kwag93.github.io/claude-code-onboarding-site/04-evolving/) | 메모리, 룰, 스킬 업데이트 |
| | [05. 요청하는 방법](https://kwag93.github.io/claude-code-onboarding-site/05-prompt-tips/) | 보여주기 중심 요청법 |

---

## 권장 학습 순서

```
1일차 (40분)   환경 구축 + 관점 전환     → 01, 14, 02
2일차 (40분)   실무 적용                 → 03, 05, 21
3일차 (80분)   심화 기능                 → 07, 08, 09, 15, 16
4일차 (50분)   확장 + 습관화             → 06, 19, 10, 11, 17
5일차 (40분)   고급 기능                 → 20, 22, 23, 24, 25, 26
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
zensical serve

# 빌드 테스트
zensical build --clean
```

## 배포

`main` 브랜치에 push하면 GitHub Actions가 자동 배포합니다.

수동 배포가 필요한 경우:
1. Actions 탭 → **Deploy to GitHub Pages** → Run workflow

---

## 기술 스택

- [Zensical](https://github.com/zensical/zensical) - 문서 프레임워크 (Material for MkDocs 후속)
- [GitHub Pages](https://pages.github.com/) - 호스팅
- [GitHub Actions](https://github.com/features/actions) - CI/CD

## 프로젝트 구조

```
├── .github/workflows/deploy.yml   # 자동 배포 워크플로우
├── docs/
│   ├── index.md                   # 홈페이지
│   ├── 01-quick-start.md ~ 27-voice-mode.md (27개 문서)
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
