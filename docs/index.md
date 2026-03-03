<div class="hero-section" markdown>

# Claude Code 온보딩 가이드

<p class="hero-subtitle">AI 페어 프로그래밍으로 개발 생산성을 높이기 위한 팀 온보딩 문서</p>

<div class="hero-quote" markdown>
**AI는 도구가 아니라, 넓은 지식을 가진 도서관 사서입니다.**
</div>

<p class="hero-meta">v1.7.0 · CLOBOT</p>

</div>

---

## 학습 경로

<div class="grid cards" markdown>

- :material-rocket-launch:{ .lg .middle } **1일차: 환경 구축 + 관점 전환**

    ---

    설치와 기본 설정을 완료하고 AI 협업 관점을 정리합니다.

    **약 40분** · [빠른 시작](./01-quick-start.md) → [프로젝트 초기화](./14-init-guide.md) → [AI 마인드셋](./02-ai-mindset.md)

- :material-code-braces:{ .lg .middle } **2일차: 실무 적용**

    ---

    에이전트, 스킬, 훅을 실전에 적용하고 커뮤니케이션 패턴을 익힙니다.

    **약 40분** · [워크플로우](./03-workflows.md) → [요청하는 방법](./05-prompt-tips.md)

- :material-lightning-bolt:{ .lg .middle } **3일차: 심화 기능**

    ---

    CLI 장점을 이해하고 CLAUDE.md, Hooks, 자동화 기능을 활용합니다.

    **약 80분** · [왜 CLI인가](./07-why-cli.md) → [OMC 가이드](./08-omc-guide.md) → [자동화](./09-automation.md) → [CLAUDE.md](./15-claude-md-mastery.md) → [Hooks](./16-hooks-practical.md)

- :material-trending-up:{ .lg .middle } **4일차+: 확장 + 습관화**

    ---

    팀 협업, MCP 연동, 나만의 스킬을 만들어 지속 발전합니다.

    **약 50분** · [팀 세션](./06-agent-teams-cowork.md) → [코워크](./19-cowork-guide.md) → [MCP](./10-mcp-ecosystem.md) → [스킬](./11-skills-plugins.md) → [안티패턴](./17-anti-patterns.md)

</div>

---

## 문서 구성

### 시작하기

| 문서 | 내용 | 소요시간 |
|------|------|----------|
| [01. 빠른 시작](./01-quick-start.md) | 설치, oh-my-claudecode, MCP 설정 | 10분 |
| [14. 프로젝트 초기화](./14-init-guide.md) | /init으로 프로젝트 세팅 | 10분 |
| [02. AI 마인드셋](./02-ai-mindset.md) | 도서관 사서 비유, 인지 오프로딩 위험 | 10분 |

### 실무 가이드

| 문서 | 내용 | 소요시간 |
|------|------|----------|
| [03. 실무 워크플로우](./03-workflows.md) | 에이전트/스킬/훅, 스팟 SDK 예제 | 20분 |
| [21. 모범 사례](./21-best-practices.md) | 컨텍스트 관리, 검증, 탐색→계획→코딩 | 15분 |
| [15. CLAUDE.md 마스터하기](./15-claude-md-mastery.md) | 60줄 원칙, 3가지 계층, @import | 15분 |
| [16. Hooks 실전 가이드](./16-hooks-practical.md) | 4가지 이벤트, 실전 레시피 5개 | 15분 |
| [06. 팀 세션 가이드](./06-agent-teams-cowork.md) | 팀 세션 구성, 서브에이전트 비교, 토큰 비용 | 15분 |
| [19. 코워크 가이드](./19-cowork-guide.md) | 코워크 시작법, 엑셀/파워포인트 활용 | 15분 |
| [22. 리모트 컨트롤](./22-remote-control.md) | 모바일/브라우저에서 세션 이어서 사용 | 10분 |
| [07. 왜 CLI인가](./07-why-cli.md) | CLI 장점, 이미지 활용, 멀티 인스턴스 | 10분 |
| [08. OMC 활용 가이드](./08-omc-guide.md) | autopilot, learner, HUD, 실행 모드 | 15분 |
| [09. 자동화의 힘](./09-automation.md) | git, PR, 테스트, 코드 리뷰 자동화 | 15분 |
| [24. GitLab CI/CD](./24-gitlab-cicd.md) | CI/CD 파이프라인 통합, @claude 멘션 | 10분 |
| [13. 파워 유저 팁](./13-power-user-tips.md) | 70가지+ 실전 팁, 단축키, 자동화 | 20분 |

### 확장하기

| 문서 | 내용 | 소요시간 |
|------|------|----------|
| [20. 확장 기능 개요](./20-features-overview.md) | 기능 비교표, 컨텍스트 비용, 조합 패턴 | 15분 |
| [10. MCP 생태계](./10-mcp-ecosystem.md) | Context7, Figma MCP, Stitch MCP, Jira MCP | 15분 |
| [11. 스킬과 플러그인](./11-skills-plugins.md) | humanizer, 직접 만들기, 서브에이전트 | 15분 |
| [23. Chrome 확장 프로그램](./23-chrome-extension.md) | 브라우저 자동화, 라이브 디버깅 | 10분 |
| [25. Status Line](./25-statusline-guide.md) | 하단 대시보드 커스터마이징, 셸 스크립트 예제 | 15분 |
| [26. 키보드 단축키](./26-keybindings-guide.md) | 커스텀 키바인딩, Vim 모드, Chords | 10분 |
| [18. 추천 리소스](./18-resources.md) | 공식 문서, 한국어 가이드, 커뮤니티 허브 | 10분 |

### 철학

| 문서 | 내용 | 소요시간 |
|------|------|----------|
| [12. AI와 함께 일하는 방법](./12-ai-work-philosophy.md) | 찾지 말고 물어보기, 메모리 관리 | 10분 |
| [17. 흔한 실수 피하기](./17-anti-patterns.md) | 8가지 안티패턴과 해결법 | 10분 |
| [04. 나아가는 방법](./04-evolving.md) | 메모리, 룰, 스킬 업데이트 | 10분 |
| [05. 요청하는 방법](./05-prompt-tips.md) | 보여주기 중심 요청법 | 10분 |

---

## 필수 설정 체크리스트

- [ ] Claude Code 설치
- [ ] oh-my-claudecode 플러그인 설치
- [ ] `/oh-my-claudecode:deep-init` 실행
- [ ] MCP 설정 (oh-my-claudecode 사용 시 `/oh-my-claudecode:mcp-setup`)
- [ ] 프로젝트에 CLAUDE.md 생성

---

## 핵심 리소스

<div class="grid cards" markdown>

- :material-book-open-variant:{ .lg .middle } **공식 문서**

    ---

    - [Claude Code 공식 문서](https://code.claude.com/docs/ko/overview)
    - [에이전트 팀 가이드](https://code.claude.com/docs/ko/agent-teams)

- :material-puzzle:{ .lg .middle } **필수 플러그인 & 스킬**

    ---

    - [oh-my-claudecode](https://github.com/Yeachan-Heo/oh-my-claudecode) - 멀티 에이전트 오케스트레이션
    - [humanizer](https://github.com/blader/humanizer) - AI 글을 자연스럽게
    - [Agent Skills 디렉토리](https://agent-skills.md) - 커뮤니티 스킬 모음

- :material-connection:{ .lg .middle } **MCP 서버**

    ---

    - [Context7](https://context7.com) - 최신 라이브러리 문서 접근
    - [Figma MCP](https://help.figma.com/hc/ko/articles/32132100833559) - 디자인-코드 연동
    - [MCP Atlassian](https://github.com/sooperset/mcp-atlassian) - Jira/Confluence 연동

- :material-school:{ .lg .middle } **참고 연구**

    ---

    - [AI가 코딩 스킬에 미치는 영향](https://www.anthropic.com/research/AI-assistance-coding-skills) - Anthropic
    - [프롬프팅 101](https://www.haskellforall.com/2026/01/prompting-101-show-dont-tell.html) - 보여주고 설명은 줄이기

</div>

---

## 도움이 필요할 때

- `/help` - Claude Code 내장 도움말
- `/oh-my-claudecode:help` - 플러그인 도움말
- 이 가이드 문서 다시 읽기
- 팀 슬랙 채널에서 경험 공유

---

_AI로 빨라졌다고 실력까지 는 건 아닙니다._
_하지만 관점만 제대로 잡으면 최고의 동료가 됩니다._
