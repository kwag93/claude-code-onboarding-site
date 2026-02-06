# Claude Code 온보딩 가이드

AI 페어 프로그래밍으로 개발 생산성을 높이기 위한 팀 온보딩 문서입니다.

현재 문서 버전: **v1.1.0** (2026-02-06)

---

## 핵심 메시지

> **AI는 도구가 아니라, 넓은 지식 베이스를 가진 도서관 사서입니다.**

- "해줘"가 아니라 **질문**하세요.
- 결과를 받는 게 아니라 **선택지**를 받으세요.
- AI가 결정하는 게 아니라 **내가 결정**하세요.

---

## 문서 구성

약 1시간 20분 안에 전체 흐름을 파악할 수 있도록 구성했습니다.

1. [빠른 시작](./01-quick-start.md): 설치, oh-my-claudecode, MCP 설정 (10분)
2. [AI 마인드셋](./02-ai-mindset.md): 도서관 사서 비유, 인지 오프로딩 위험 (10분)
3. [실무 워크플로우](./03-workflows.md): 에이전트/스킬/훅, 스팟 SDK 예제 (20분)
4. [에이전트 팀과 코워크](./06-agent-teams-cowork.md): 신기능 소개, 오푸스 4.6, 엑셀/파워포인트 실전 (20분)
5. [나아가는 방법](./04-evolving.md): 메모리, 룰, 스킬 업데이트 (10분)
6. [요청하는 방법](./05-prompt-tips.md): 보여주기 중심 요청법, 프롬프팅 무용론 (10분)

**총 소요시간**: 약 1시간 20분

---

## 권장 학습 순서

### 1일차: 환경 구축 (약 20분)

1. [01. 빠른 시작](./01-quick-start.md)으로 설치와 기본 설정을 완료합니다.
2. oh-my-claudecode 설치 후 `deep-init`을 실행합니다.

### 2일차: 관점 전환 (약 20분)

1. [02. AI 마인드셋](./02-ai-mindset.md)으로 AI 협업 관점을 정리합니다.
2. [05. 요청하는 방법](./05-prompt-tips.md)으로 실전 커뮤니케이션 패턴을 익힙니다.

### 3일차 이후: 실무 적용 (약 40분)

1. [03. 실무 워크플로우](./03-workflows.md)로 에이전트/스킬/훅을 적용합니다.
2. [06. 에이전트 팀과 코워크](./06-agent-teams-cowork.md)로 최신 기능과 협업 방식을 적용합니다.
3. [04. 나아가는 방법](./04-evolving.md)으로 지속 개선 루프를 만듭니다.

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

### 필수 플러그인

- [oh-my-claudecode](https://github.com/Yeachan-Heo/oh-my-claudecode) - 멀티 에이전트 오케스트레이션

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

_AI를 활용한 생산성 향상은 역량 향상의 지름길이 아닙니다._
_하지만 올바른 관점이 있다면 가장 강력한 동료가 됩니다._
