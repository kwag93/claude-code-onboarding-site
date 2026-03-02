# 18. 추천 리소스

공식 문서를 직접 읽는 게 가장 좋지만, 현실적으로는 쉽지 않죠. 잘 정리된 자료부터 시작하세요.

> "좋은 자료를 많이 아는 것보다, 하나라도 직접 써보는 게 중요합니다."

---

## 1. 공식 문서

꼭 읽어야 할 것들이에요.

| 문서 | 설명 | 언어 |
|------|------|------|
| [Claude Code 공식 문서](https://code.claude.com/docs/ko/overview) | 전체 기능 개요 | 한국어 |
| [모범 사례](https://code.claude.com/docs/ko/best-practices) | 공식 권장 사용법 | 한국어 |
| [에이전트 팀 가이드](https://code.claude.com/docs/ko/agent-teams) | 팀 협업 기능 | 한국어 |
| [CLAUDE.md 활용법](https://claude.com/blog/using-claude-md-files) | CLAUDE.md 작성 가이드 | 영어 |
| [MCP 연결 가이드](https://code.claude.com/docs/en/mcp) | MCP 서버 설정 | 영어 |
| [Skills 가이드](https://code.claude.com/docs/en/skills) | 스킬 만들기 | 영어 |
| [Status Line 커스터마이징](https://code.claude.com/docs/en/statusline) | 하단 대시보드 만들기 | 영어 |
| [Keybindings 커스터마이징](https://code.claude.com/docs/en/keybindings) | 키보드 단축키 설정 | 영어 |
| [Common Workflows](https://code.claude.com/docs/en/common-workflows) | 일반적인 워크플로우 | 영어 |

!!! tip "영어 문서 읽는 쉬운 방법"
    Claude에게 "이 URL 읽고 한국어로 요약해줘"라고 하면 돼요. 공식 문서를 직접 번역받는 셈이죠.

---

## 2. 한국어 가이드

바로 읽을 수 있는 것들이에요.

### revfactory/claude-code-mastering

- **URL**: [github.com/revfactory/claude-code-mastering](https://github.com/revfactory/claude-code-mastering)
- **저자**: 황민호
- **내용**: 13개 장으로 구성된 한국어 완전 가이드. 기초 → 설정 → 고급 → 실전 → 협업 순으로 체계적으로 다룹니다. GitHub Pages에서 온라인 열람 가능.
- **추천 대상**: Claude Code를 처음부터 끝까지 체계적으로 배우고 싶은 분

### 하이퍼리즘 기술 블로그

- **URL**: [tech.hyperithm.com/claude_code_guides](https://tech.hyperithm.com/claude_code_guides)
- **내용**: Git Worktree + Claude Squad 병렬 세션 관리, CLAUDE.md 계층 구분 등 실무 환경에 맞춘 심화 내용.
- **추천 대상**: 현업에서 바로 적용하고 싶은 분

### PyTorch KR 포럼 - 30가지 팁

- **URL**: [discuss.pytorch.kr - 30가지 Claude Code 팁](https://discuss.pytorch.kr/t/30-claude-code-feat-ykdojo-claude-code-tips/8368)
- **내용**: ykdojo의 45가지 팁을 한국어로 정리한 버전. 빠르게 훑기 좋습니다.
- **추천 대상**: 시간 없을 때 핵심 팁만 빠르게 확인하고 싶은 분

### Velog - Claude Code 완벽 가이드 한글 요약

- **URL**: [velog.io/@skysoo/Claude-Code-완벽-가이드-한글-요약본](https://velog.io/@skysoo/Claude-Code-완벽-가이드-한글-요약본)
- **내용**: 세 가지 채팅 모드, CLAUDE.md 계층 구조, 커스텀 슬래시 명령까지 전체 기능을 한눈에 정리.
- **추천 대상**: 전체 기능 지형도를 빠르게 파악하고 싶은 분

### Prompt Hacker Danny - 클로드 코드 총정리

- **URL**: [magicaiprompts.com/docs/claude/claude-code](https://www.magicaiprompts.com/docs/claude/claude-code/)
- **내용**: 설치부터 실전 활용까지 단계별로 정리된 입문 가이드.
- **추천 대상**: Claude Code를 처음 접하는 분

### F-Lab - Claude Code 실전 가이드

- **URL**: [f-lab.ai - Claude Code 가이드](https://f-lab.ai/en/blog/article-claude-code-guide-20250731)
- **내용**: "AI는 곱셈" 철학, Plan & Act 사이클 소개. 시니어 개발자 생산성 +22% vs 주니어 +4% 데이터 포함.
- **추천 대상**: AI 도구 도입 효과를 설득해야 하는 팀 리더

---

## 3. 커뮤니티 허브

직접 만든 도구와 패턴이 모이는 곳이에요.

### awesome-claude-code (24.9k stars)

- **URL**: [github.com/hesreallyhim/awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code)
- **내용**: Hooks, Slash Commands, CLAUDE.md 예제, Agent Skills, Status Lines 등 커뮤니티 리소스 종합.

주목할 하위 프로젝트:

| 프로젝트 | 내용 |
|---------|------|
| [wshobson/commands](https://github.com/wshobson/commands) | 실무용 슬래시 커맨드 모음 (1.7k stars) |
| [avifenesh/awesome-slash](https://github.com/avifenesh/awesome-slash) | 40개 에이전트, 26개 스킬 컬렉션 |

### ykdojo/claude-code-tips

- **URL**: [github.com/ykdojo/claude-code-tips](https://github.com/ykdojo/claude-code-tips)
- **내용**: Anthropic 해커톤 우승자 ykdojo가 10억 토큰 넘게 쓰면서 정리한 45가지 실전 팁.

핵심 팁 맛보기:

- 음성 입력이 타이핑보다 빠르고 컨텍스트 전달이 풍부합니다
- `/clear` + `/compact`로 컨텍스트를 깔끔하게 관리하세요
- tmux로 자가 검증 사이클을 구성하면 검토 속도가 올라갑니다

### jay_ai__ — Claude Code 19가지 입문~전문가 팁

- **URL**: [threads.com/@jay_ai__](https://www.threads.com/@jay_ai__/post/DVIozZbD_nW)
- **내용**: 모델 선택, 단축키, CLAUDE.md, Hooks, Status Line, 컨텍스트 관리까지 19개 팁을 입문자부터 전문가까지 단계별로 정리.
- **추천 대상**: 한국어로 빠르게 전체 기능을 훑고 싶은 분

### How Boris Uses Claude Code

- **URL**: [howborisusesclaudecode.com](https://howborisusesclaudecode.com)
- **내용**: Claude Code 창시자 Boris Cherny의 일상 사용법 40가지. 5개 Worktree 병렬, Plan-Review-Plan 패턴, CLAUDE.md 지속 갱신 습관 등.
- **추천 대상**: 만든 사람이 어떻게 쓰는지 궁금한 분

### ClaudeLog

- **URL**: [claudelog.com](https://claudelog.com/)
- **내용**: 커뮤니티 주도 문서 허브. 가이드, 튜토리얼, 모범 사례가 꾸준히 추가되고 있어요.

### Awesome Claude AI 디렉토리

- **URL**: [awesomeclaude.ai/awesome-claude-code](https://awesomeclaude.ai/awesome-claude-code)
- **내용**: 시각적으로 정리된 리소스 디렉토리. 원하는 카테고리를 빠르게 탐색할 수 있습니다.

---

## 4. 심화 학습

아키텍처와 원칙을 이해하고 싶다면 여기서 시작하세요.

### Hooks/MCP/Skills 전체 스택 해설

- **URL**: [alexop.dev - Claude Code 풀스택 이해](https://alexop.dev/posts/understanding-claude-code-full-stack/)
- **내용**: CLAUDE.md, Slash Commands, Skills, Hooks 4가지 핵심 요소를 비교하며 설명.
- **추천 대상**: 왜 이렇게 구성되어 있는지 아키텍처 수준에서 이해하고 싶은 분

### CLAUDE.md 잘 쓰는 법

- **URL**: [humanlayer.dev - Writing a Good CLAUDE.md](https://www.humanlayer.dev/blog/writing-a-good-claude-md)
- **내용**: 60줄 원칙, 린터 역할 거부, 점진적 공개 전략. CLAUDE.md를 얼마나 길게 써야 하는지에 대한 실용적 가이드.
- **추천 대상**: CLAUDE.md를 최적화하고 싶은 분

### 8가지 황금 규칙과 재사용 워크플로우

- **URL**: [Medium - Claude Code 8 Golden Rules](https://medium.com/aimonks/claude-code-8-golden-rules-and-one-reusable-workflow-9430008ff5fa)
- **내용**: Writer/Reviewer 패턴, 헤드리스 모드 활용법, 반복 가능한 워크플로우 구성.
- **추천 대상**: 작업 패턴을 체계화하고 싶은 분

### Advent of Claude 2025 팁 컬렉션

- **URL**: [dev.to - The Ultimate Claude Code Tips Collection](https://dev.to/damogallagher/the-ultimate-claude-code-tips-collection-advent-of-claude-2025-5b73)
- **내용**: Anthropic DevRel이 매일 공유한 25가지 팁을 한 곳에 모아둔 글.
- **추천 대상**: 짧은 팁을 하나씩 꾸준히 소화하고 싶은 분

---

## 5. 교육 과정

### SFEIR Institute

- **URL**: [institute.sfeir.com - Claude Code Best Practices](https://institute.sfeir.com/en/claude-code/claude-code-resources/best-practices/)
- **내용**: 기업 교육 수준의 Claude Code 커리큘럼. 단계별 학습 경로가 잡혀 있습니다.

---

## 6. 연구 자료

데이터와 원칙이 궁금하다면 여기를 보세요.

| 제목 | 출처 | 핵심 내용 |
|------|------|----------|
| [AI가 코딩 스킬에 미치는 영향](https://www.anthropic.com/research/AI-assistance-coding-skills) | Anthropic | AI 의존도와 실력 관계 연구 |
| [프롬프팅 101 - Show Don't Tell](https://www.haskellforall.com/2026/01/prompting-101-show-dont-tell.html) | 커뮤니티 | 설명을 줄이고 예시를 보여주는 전략 |

---

## 7. 학습 순서 추천

처음이라면 이 순서가 가장 효율적이에요.

1. **이 가이드의 [01. 빠른 시작](./01-quick-start.md)**으로 환경 설정
2. **[revfactory/claude-code-mastering](https://github.com/revfactory/claude-code-mastering)**으로 체계적 학습
3. **[awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code)**에서 필요한 도구 탐색
4. **[공식 모범 사례](https://code.claude.com/docs/ko/best-practices)**로 검증

---

> "자료를 많이 읽는 것보다, 하나라도 직접 써보는 게 중요합니다."

---

링크가 깨졌거나 업데이트가 필요한 자료를 발견하셨다면 [GitHub Issue](https://github.com/kwag93/claude-code-onboarding-site/issues)로 제보해주세요.
