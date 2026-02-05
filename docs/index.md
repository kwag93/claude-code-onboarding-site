# Claude Code 온보딩 가이드

AI 페어 프로그래밍으로 개발 생산성을 높이기 위한 팀 온보딩 문서입니다.

---

## 핵심 메시지

> **AI는 도구가 아니라, 넓은 지식 베이스를 가진 도서관 사서입니다.**

"해줘"가 아니라 **"질문"**하세요.
결과를 받는 게 아니라 **선택지**를 받으세요.
AI가 결정하는 게 아니라 **내가 결정**하세요.

---

## 문서 구성

| 순서 | 문서                                 | 내용                                 | 소요시간 |
| :--: | ------------------------------------ | ------------------------------------ | :------: |
|  01  | [빠른 시작](./01-quick-start.md)     | 설치, oh-my-claudecode, MCP 설정     |   10분   |
|  02  | [AI 마인드셋](./02-ai-mindset.md)    | 도서관 사서 비유, 인지 오프로딩 위험 |   10분   |
|  03  | [실무 워크플로우](./03-workflows.md) | 에이전트/스킬/훅, SPOT SDK 예제      |   20분   |
|  04  | [나아가는 방법](./04-evolving.md)    | 메모리, 룰, 스킬 업데이트            |   10분   |
|  05  | [요청하는 방법](./05-prompt-tips.md) | Show don't tell, 프롬프팅 무용론     |   10분   |

**총 소요시간**: 약 1시간

---

## 권장 학습 순서

### Day 1: 환경 구축

1. [01. 빠른 시작](./01-quick-start.md) - 설치 및 설정
2. oh-my-claudecode 설치 + deep-init 실행

### Day 2: 관점 전환

3. [02. AI 마인드셋](./02-ai-mindset.md) - AI를 대하는 방법
4. [05. 요청하는 방법](./05-prompt-tips.md) - 효과적인 커뮤니케이션

### Day 3~: 실무 적용

5. [03. 실무 워크플로우](./03-workflows.md) - 에이전트/스킬 만들기
6. [04. 나아가는 방법](./04-evolving.md) - 지속적 개선

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

- [Claude Code 공식 문서](https://code.claude.com/docs/en/overview)

### 필수 플러그인

- [oh-my-claudecode](https://github.com/Yeachan-Heo/oh-my-claudecode) - 멀티 에이전트 오케스트레이션

### 참고 연구

- [AI가 코딩 스킬에 미치는 영향](https://www.anthropic.com/research/AI-assistance-coding-skills) - Anthropic
- [Prompting 101: Show, don't tell](https://www.haskellforall.com/2026/01/prompting-101-show-dont-tell.html)

---

## 도움이 필요할 때

- `/help` - Claude Code 내장 도움말
- `/oh-my-claudecode:help` - 플러그인 도움말
- 이 가이드 문서 다시 읽기
- 팀 슬랙 채널에서 경험 공유

---

_AI-enhanced productivity is not a shortcut to competence._
_But with the right mindset, it becomes your most powerful ally._
