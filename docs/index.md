# Claude Code 온보딩 가이드

AI 페어 프로그래밍으로 개발 생산성을 높이기 위한 팀 온보딩 문서예요.

현재 문서 버전: **v1.2.0** (2026-02-12)

---

## 핵심 메시지

> **AI는 도구가 아니라, 넓은 지식을 가진 도서관 사서입니다.**

- "해줘"가 아니라 **질문**하세요.
- AI가 결정하는 게 아니라 **내가 결정**하세요.

---

## 문서 구성

### 시작하기

1. [빠른 시작](./01-quick-start.md): 설치, oh-my-claudecode, MCP 설정 (10분)
2. [AI 마인드셋](./02-ai-mindset.md): 도서관 사서 비유, 인지 오프로딩 위험 (10분)

### 실무 가이드

3. [실무 워크플로우](./03-workflows.md): 에이전트/스킬/훅, 스팟 SDK 예제 (20분)
4. [에이전트 팀과 코워크](./06-agent-teams-cowork.md): 팀 조율, 코워크 실전 예시, 토큰 비용 (20분)
5. [왜 CLI인가](./07-why-cli.md): CLI 장점, 이미지 활용, 멀티 인스턴스 (10분)
6. [OMC 활용 가이드](./08-omc-guide.md): autopilot, learner, HUD, 실행 모드 (15분)
7. [자동화의 힘](./09-automation.md): git, PR, 테스트, 코드 리뷰 자동화 (15분)

### 확장하기

8. [MCP 생태계](./10-mcp-ecosystem.md): Context7, Figma MCP, Jira MCP (15분)
9. [스킬과 플러그인](./11-skills-plugins.md): humanizer, 직접 만들기, 서브에이전트 (15분)

### 철학

10. [AI와 함께 일하는 방법](./12-ai-work-philosophy.md): 찾지 말고 물어보기, 메모리 관리 (10분)
11. [나아가는 방법](./04-evolving.md): 메모리, 룰, 스킬 업데이트 (10분)
12. [요청하는 방법](./05-prompt-tips.md): 보여주기 중심 요청법 (10분)

**총 소요시간**: 약 2시간 30분

---

## 권장 학습 순서

### 1일차: 환경 구축 + 관점 전환 (약 30분)

1. [01. 빠른 시작](./01-quick-start.md)으로 설치와 기본 설정을 완료합니다.
2. [02. AI 마인드셋](./02-ai-mindset.md)으로 AI 협업 관점을 정리합니다.

### 2일차: 실무 적용 (약 40분)

1. [03. 실무 워크플로우](./03-workflows.md)로 에이전트/스킬/훅을 적용합니다.
2. [05. 요청하는 방법](./05-prompt-tips.md)으로 실전 커뮤니케이션 패턴을 익힙니다.

### 3일차: 심화 기능 (약 50분)

1. [07. 왜 CLI인가](./07-why-cli.md)로 CLI의 장점을 이해합니다.
2. [08. OMC 활용 가이드](./08-omc-guide.md)로 autopilot, learner 등을 활용합니다.
3. [09. 자동화의 힘](./09-automation.md)으로 반복 작업을 자동화합니다.

### 4일차 이후: 확장 + 습관화 (약 30분)

1. [06. 에이전트 팀과 코워크](./06-agent-teams-cowork.md)로 최신 협업 기능을 적용합니다.
2. [10. MCP 생태계](./10-mcp-ecosystem.md)로 외부 도구를 연동합니다.
3. [11. 스킬과 플러그인](./11-skills-plugins.md)으로 나만의 확장을 만듭니다.
4. [12. AI와 함께 일하는 방법](./12-ai-work-philosophy.md)으로 지속적인 발전 루프를 구축합니다.
5. [04. 나아가는 방법](./04-evolving.md)으로 시스템을 지속 개선합니다.

---

## 필수 설정 체크리스트

- [ ] Claude Code 설치
- [ ] oh-my-claudecode 플러그인 설치
- [ ] `/oh-my-claudecode:deep-init` 실행
- [ ] MCP 설정 (oh-my-claudecode 사용 시 `/oh-my-claudecode:mcp-setup`)
- [ ] 프로젝트에 CLAUDE.md 생성

---

## 핵심 리소스

### 공식 문서

- [Claude Code 공식 문서](https://code.claude.com/docs/ko/overview)
- [에이전트 팀 가이드](https://code.claude.com/docs/ko/agent-teams)

### 필수 플러그인

- [oh-my-claudecode](https://github.com/Yeachan-Heo/oh-my-claudecode) - 멀티 에이전트 오케스트레이션

### MCP 서버

- [Context7](https://context7.com) - 최신 라이브러리 문서 접근
- [Figma MCP](https://help.figma.com/hc/ko/articles/32132100833559) - 디자인-코드 연동
- [MCP Atlassian](https://github.com/sooperset/mcp-atlassian) - Jira/Confluence 연동

### 스킬

- [humanizer](https://github.com/blader/humanizer) - AI 글을 자연스럽게
- [Agent Skills 디렉토리](https://agent-skills.md) - 커뮤니티 스킬 모음

### 참고 연구

- [AI가 코딩 스킬에 미치는 영향](https://www.anthropic.com/research/AI-assistance-coding-skills) - Anthropic
- [프롬프팅 101: 보여주고 설명은 줄이기](https://www.haskellforall.com/2026/01/prompting-101-show-dont-tell.html)

---

## 도움이 필요할 때

- `/help` - Claude Code 내장 도움말
- `/oh-my-claudecode:help` - 플러그인 도움말
- 이 가이드 문서 다시 읽기
- 팀 슬랙 채널에서 경험 공유

---

_AI로 빨라졌다고 실력까지 는 건 아닙니다._
_하지만 관점만 제대로 잡으면 최고의 동료가 됩니다._
