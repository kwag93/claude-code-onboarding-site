# 문서 업데이트 내역

이 문서의 버전 변경 사항을 기록합니다.

---

## v1.8.2 (2026-03-25)

**Computer Use 가이드 추가**

### 새 문서

- **[33. Computer Use](33-computer-use.md)**: Claude API의 Computer Use Tool 개요. 데스크톱 자율 제어(스크린샷/마우스/키보드), 에이전트 루프 패턴, 실행 환경 구성, 보안 고려사항, Chrome 확장과의 비교

---

## v1.8.1 (2026-03-25)

**Superpowers 활용 가이드 추가**

### 새 문서

- **[32. Superpowers 활용 가이드](32-superpowers-guide.md)**: 개발 방법론 프레임워크 소개. brainstorming → plan → TDD → review 7단계 워크플로우, 주요 스킬 소개, OMCC와의 비교/선택 가이드

### 기존 문서 업데이트

- **[08. OMC 활용 가이드](08-omc-guide.md)**: 다음 단계에 Superpowers 상호 링크 추가
- **index.md**: 실무 가이드 테이블, 핵심 리소스에 Superpowers 추가
- **README.md**: 문서 테이블, 학습 순서, 프로젝트 구조 업데이트

---

## v1.8.0 (2026-03-23)

**대규모 콘텐츠 개편: 새 기능 + 철학 강화 + 실전 가이드**

### 새 문서 추가 (4개)

- **[Channels](28-channels.md)**: 외부 이벤트를 Claude Code 세션으로 푸시하는 채널 기능 (단방향/양방향, 웹훅 예제, 권한 릴레이, 센더 게이팅)
- **[멀티 모델 협업](29-multi-model.md)**: Claude, Codex, Gemini를 함께 활용하는 방법 (/ask, /ccg, 합의 기반 의사결정 패턴)
- **[학습 기능 활용하기](30-learning-features.md)**: Learner, Insights, Memory 계층 통합 가이드 (잘못된 학습 바로잡기 포함)
- **[직접 만들기](31-build-your-own.md)**: 나만의 스킬/플러그인/채널/MCP 서버 만들기 튜토리얼 (spot-orbit-plugin 실전 사례 포함)

### 기존 문서 보강

- **[자동화의 힘](09-automation.md)**: /loop 간격 구문 상세화, Cron 섹션 대폭 확장 (일회성 리마인더, CronCreate/List/Delete 도구, 세션 범위 스케줄링, Jitter, Cron 표현식 참조, tmux 세션 유지 팁). 잘못된 정보("실행 중이지 않아도 동작") 수정
- **[AI 마인드셋](02-ai-mindset.md)**: Anthropic RCT 연구 결과 추가 (AI 보조 그룹 50% vs 대조군 67%, 디버깅 격차, 인지적 노력의 중요성)
- **[AI와 함께 일하는 방법](12-ai-work-philosophy.md)**: RCT 연구와 "갈군다" 패턴의 연결 블록쿼트 추가
- **[흔한 실수 피하기](17-anti-patterns.md)**: 실수 3개 추가 (컨텍스트 오염, /clear 타이밍, HANDOFF.md 활용). Status Line 교차 링크 포함
- **[스킬과 플러그인](11-skills-plugins.md)**: spot-orbit-plugin 실전 사례 참조 추가

### 네비게이션 개선

- "실무 가이드"에 **멀티 모델 협업** 추가 (OMC 가이드 다음)
- "확장하기"에 **학습 기능**, **직접 만들기**, **Channels** 3개 문서 추가
- 학습 기능을 "확장하기" 앞쪽에 배치 (Gemini 리뷰 반영: 실용적 기능이므로 철학보다 확장에 적합)

### 리뷰

- Gemini CLI 리뷰 피드백 7개 항목 반영 (비용/한계 경고, 잘못된 학습 바로잡기, 트러블슈팅 등)

---

## v1.7.0 (2026-03-03)

**빌드 시스템 마이그레이션: MkDocs Material → Zensical**

### 빌드 시스템 변경

- **MkDocs Material → Zensical 마이그레이션**: MkDocs 2.0의 Material 호환 불가 이슈에 대응하여, Material for MkDocs 팀이 만든 후속 SSG인 Zensical로 전환
- **mike 버전 관리 제거**: 온보딩 가이드 특성상 다중 버전이 불필요하여 배포 파이프라인 단순화
- **GitHub Pages 배포 방식 변경**: gh-pages 브랜치 → GitHub Actions artifact 배포로 전환

### 변경 내역

- `requirements.txt`: `mkdocs-material`, `mike` 제거 → `zensical` 추가
- `mkdocs.yml`: emoji namespace를 `zensical.extensions.emoji`로 변경, mike 플러그인 및 버전 설정 제거
- `.github/workflows/deploy.yml`: `zensical build --clean` + Pages artifact 배포로 전면 교체

### 참조

- [Zensical](https://github.com/squidfunk/zensical)

---

## v1.6.0 (2026-03-02)

**Status Line & 키보드 단축키 가이드 추가, 파워 유저 팁 대폭 보강**

### 새 문서 추가 (2개)

- [25. Status Line 커스터마이징](./25-statusline-guide.md): 하단 대시보드 만들기, /statusline 명령어, 수동 설정, Bash/Python 스크립트 6개 예제 (컨텍스트 바, Git 상태, 비용 추적, 멀티라인, 클릭 링크, 캐싱), 사용 가능한 JSON 필드, 트러블슈팅
- [26. 키보드 단축키 커스터마이징](./26-keybindings-guide.md): /keybindings 설정, keybindings.json 구조, 18개 컨텍스트별 액션 목록, Chord 바인딩, Vim 모드 상호작용, 터미널 멀티플렉서 충돌 해결, 실전 레시피 4개

### 기존 문서 보강

- [13. 파워 유저 팁](./13-power-user-tips.md): **jay_ai__ 커뮤니티 팁 반영**
  - 모델 선택 전략 추가 (Opus/Sonnet/Haiku/opusplan, effort level, /fast)
  - 세션 이동 추가 (/teleport, /desktop, --from-pr)
  - Notification hook 레시피 (macOS osascript)
  - /sandbox 안전한 자율 모드
  - Boris Cherny(창시자) 습관 5가지
  - 치트시트 대폭 확장 (명령어 20개, 단축키 15개, CLI 플래그 9개)

### 네비게이션 개선

- **확장하기** 섹션에 Status Line, 키보드 단축키 문서 추가
- 총 26개 문서 체계로 확장

### 참조

- [공식 Status Line](https://code.claude.com/docs/en/statusline)
- [공식 Keybindings](https://code.claude.com/docs/en/keybindings)
- [jay_ai__ 스레드](https://www.threads.com/@jay_ai__/post/DVIozZbD_nW)
- [Boris Cherny 팁](https://howborisusesclaudecode.com)

---

## v1.5.0 (2026-02-25)

**공식 문서 기반 대규모 업데이트: Windows 설치 수정, 신규 문서 5개 추가, 확장 기능 체계 개편**

### 설치 가이드 수정

- [01. 빠른 시작](./01-quick-start.md): **Windows 네이티브 설치 지원 반영** (PowerShell, CMD, WinGet)
  - 기존 WSL2 전용 안내를 네이티브 Windows 설치 메인으로 변경
  - 공식 설치 스크립트(`curl`/`irm`) 기반으로 전면 재작성
  - Homebrew, WinGet 패키지 매니저 옵션 추가
  - VS Code, Desktop App, Web, JetBrains 등 다양한 환경 안내 추가

### 새 문서 추가 (5개)

- [20. 확장 기능 개요](./20-features-overview.md): CLAUDE.md, Skills, Subagents, Agent Teams, MCP, Hooks 기능 비교표, 비슷한 기능 비교 (Skill vs Subagent 등), 컨텍스트 비용 이해, 기능 조합 패턴
- [21. 모범 사례](./21-best-practices.md): 컨텍스트 관리, 검증 방법 제공, 탐색→계획→코딩, 세션 관리, 자동화/확장, 흔한 실패 패턴 (공식 best-practices 기반)
- [22. Remote Control](./22-remote-control.md): 로컬 세션을 모바일/브라우저에서 이어서 사용, Pro/Max 전용 (Team/Enterprise 불가), 연결/보안, 활용 시나리오
- [23. Chrome 확장 프로그램](./23-chrome-extension.md): 브라우저 자동화, 라이브 디버깅, 양식 자동화, 데이터 추출, GIF 기록 등 예제 워크플로우
- [24. GitLab CI/CD](./24-gitlab-cicd.md): CI/CD 파이프라인 통합, @claude 멘션, AWS Bedrock/Google Vertex AI 설정, 모범 사례

### 네비게이션 재구성

- **실무 가이드**: 모범 사례, 리모트 컨트롤, GitLab CI/CD 추가
- **확장하기**: 확장 기능 개요를 섹션 첫 문서로 추가, Chrome 확장 프로그램 추가
- 총 24개 문서 체계로 확장

### 참조

- [공식 Overview](https://code.claude.com/docs/en/overview)
- [공식 Quickstart](https://code.claude.com/docs/en/quickstart)
- [공식 Features Overview](https://code.claude.com/docs/en/features-overview)
- [공식 Best Practices](https://code.claude.com/docs/en/best-practices)
- [공식 Remote Control](https://code.claude.com/docs/ko/remote-control)
- [공식 Chrome](https://code.claude.com/docs/ko/chrome)
- [공식 GitLab CI/CD](https://code.claude.com/docs/ko/gitlab-ci-cd)

---

## v1.4.0 (2026-02-25)

**디자인 리뉴얼, 팀 세션/코워크 분리, Stitch MCP 추가, Windows 설치 가이드**를 진행했습니다.

### 디자인 개선

- 컨텐츠 영역 대폭 확장 (75ch → 55rem), 짤림 문제 해결
- 색상 테마를 Claude Code Docs 스타일로 변경 (clean dark header + warm accent)
- blockquote, 네비게이션, 링크에 accent 색상 적용

### 문서 구조 변경

- **팀 세션과 코워크 분리**: 06번 문서를 팀 세션 전용으로 리라이트, 코워크는 [19. 코워크 가이드](./19-cowork-guide.md)로 독립
- 날짜 특정 표현("~년 업데이트") 제거, 시간에 구애받지 않는 서술로 개선
- "에이전트 팀" → "팀 세션"으로 용어 통일

### 새 콘텐츠

- [01. 빠른 시작](./01-quick-start.md): **Windows WSL2 설치 가이드** 추가
- [10. MCP 생태계](./10-mcp-ecosystem.md): **Stitch MCP** 섹션 추가 (텍스트로 UI 생성)
- [13. 파워 유저 팁](./13-power-user-tips.md): 다른 문서와의 연결 가이드 추가
- [19. 코워크 가이드](./19-cowork-guide.md): 코워크 시작법, 엑셀/파워포인트 활용 가이드

### 기타

- index.md 총 소요시간 표기 제거
- README 전체 업데이트 (18개 문서 구성 반영)
- 네비게이션 구조 개선 (총 18개 문서)

---

## v1.3.0 (2026-02-25)

**새 문서 5개 추가, Analytics 통합, 외부 리소스 큐레이션**을 진행했습니다.

### 새 문서 추가

- [14. 프로젝트 초기화](./14-init-guide.md): /init 명령어 가이드, /init vs deep-init 비교, 팀 온보딩 워크플로우
- [15. CLAUDE.md 마스터하기](./15-claude-md-mastery.md): 60줄 원칙, 3가지 계층 구조, @import 점진적 공개, 좋은/나쁜 예시 비교
- [16. Hooks 실전 가이드](./16-hooks-practical.md): 4가지 Hook 이벤트, 실전 레시피 5개 (자동 린팅, 민감 파일 보호, 타입체크 등)
- [17. 흔한 실수 피하기](./17-anti-patterns.md): 8가지 안티패턴과 해결법 (컨텍스트 관리, 60줄 원칙, 작업 분해 등)
- [18. 추천 리소스](./18-resources.md): 공식 문서, 한국어 가이드, 커뮤니티 허브, 심화 학습 자료 큐레이션

### 사이트 개선

- Google Analytics 4 통합 (방문자 추적)
- 문서 피드백 위젯 추가 ("도움이 되었나요?")
- 네비게이션 구조 개선 (17개 문서 체계)

### 네비게이션 재구성

- 시작하기: /init 가이드 추가
- 실무 가이드: CLAUDE.md 마스터하기, Hooks 실전 가이드 추가
- 확장하기: 추천 리소스 추가
- 철학: 흔한 실수 피하기 추가

---

## v1.2.0 (2026-02-12)

**실전 가이드를 대폭 늘리고 새 문서 6개를 추가**했습니다.

### 새 문서 추가

- [07. 왜 CLI인가](./07-why-cli.md): CLI vs 익스텐션 비교, 이미지 활용(Ctrl+V, 드래그앤드롭), 멀티 인스턴스 병렬 작업
- [08. OMC 활용 가이드](./08-omc-guide.md): autopilot 모드, learner 스킬, HUD 설정, 실행 모드 총정리, 업데이트 방법
- [09. 자동화의 힘](./09-automation.md): git commit/PR/테스트/코드 리뷰 자동화, 병렬 CLI 활용, 비대화형 모드
- [10. MCP 생태계](./10-mcp-ecosystem.md): Context7(최신 문서 접근), Figma MCP(디자인→코드), Jira/Confluence MCP(이슈 관리)
- [11. 스킬과 플러그인](./11-skills-plugins.md): humanizer(한국어 자연화), 커뮤니티 스킬, 직접 스킬/플러그인 만들기, 서브에이전트 활용
- [12. AI와 함께 일하는 방법](./12-ai-work-philosophy.md): 찾지 말고 물어보기, 스킬 등록 워크플로우, 메모리 관리, 갈군다 철학

### 기존 문서 개선

- [06. 에이전트 팀과 코워크](./06-agent-teams-cowork.md): 코워크 실전 예시 추가(이 가이드가 만들어진 과정), 서브에이전트 vs 에이전트 팀 비교표, 토큰 비용 상세 분석 및 최적화 전략
- [홈 페이지](./index.md): 12개 문서 구성으로 재구성, 4일 학습 순서, 핵심 리소스 확장(MCP, 스킬, 연구 링크)

### 네비게이션 재구성

- 기존: 시작하기 / 실무 가이드 / 팁 / 업데이트
- 변경: 시작하기 / 실무 가이드 / 확장하기 / 철학 / 업데이트

### 참조 링크 추가

- oh-my-claudecode GitHub, Context7, Figma MCP 가이드, MCP Atlassian
- humanizer 스킬, Agent Skills 디렉토리
- Claude Code 에이전트 팀 공식 문서

---

## v1.1.0 (2026-02-06)

이번 버전은 **최신 Claude 기능 반영 + 가독성 개선**에 집중했습니다.

주요 변경:

- 에이전트 팀/코워크/오푸스 4.6/엑셀·파워포인트 실전 가이드 추가
  → [06. 에이전트 팀과 코워크 실전 가이드](./06-agent-teams-cowork.md)
- 전역 타이포그래피 조정(본문 줄간격, 제목 계층, 코드/표 크기, 모바일 가독성)
- 홈 페이지 정보 구조 개선(표 중심 → 스캔 가능한 리스트 중심)
- 문서 빌드 환경 정리(`.venv` 무시 규칙 추가)
- 중복/구버전 문서 정리(워크플로우/프롬프트 팁 구버전 파일 제거)

---

## v1.0.0 (2026-02-05)

초기 온보딩 문서 공개 버전

- 빠른 시작/AI 마인드셋/실무 워크플로우/나아가는 방법/요청하는 방법 구성
- MkDocs Material 기반 사이트 구성
