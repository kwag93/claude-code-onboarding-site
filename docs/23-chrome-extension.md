# 23. Chrome 확장 프로그램으로 브라우저 제어하기

Claude Code를 Chrome에 연결해서 웹 앱을 직접 테스트하고 디버깅하는 기능이에요.

> 코드 작성 → 바로 브라우저에서 테스트 → 결과 확인 → 수정. 모두 한 화면에서.

---

## Chrome 통합이란?

Claude Code + Chrome 확장 프로그램의 조합이에요.

**컨텍스트 전환 없이 라이브 테스트**

```
코드 작성 → /chrome 활성화 → 브라우저에서 바로 실행 → 콘솔 오류 확인 → 원인 코드 수정 → 다시 테스트
```

**기존 세션 그대로 활용**

새 탭에서 실행되니까 로그인/세션이 유지돼요. "로그인된 상태에서 양식을 테스트해줘" 바로 가능해요.

**막히면 멈추고 물어봐요**

CAPTCHA나 이미지 클릭처럼 자동화가 안 되는 부분은 잠시 멈추고 수동 처리를 요청해요.

---

## 할 수 있는 것들

### 라이브 디버깅

페이지 로드 시 콘솔에 에러가 뜨나요?

```
> "이 페이지를 열어서 콘솔 에러를 읽고 원인을 찾아줘"
```

브라우저를 열고 에러 메시지를 직접 읽어서 분석해줘요.

### 디자인 검증

Figma 목업과 실제 구현을 비교할 때

```
> "이 URL의 버튼 색상과 크기가 Figma 목업과 같은지 확인해줘"
```

### 웹 앱 테스트

양식 유효성, 사용자 흐름, 에러 처리

```
> "로그인 폼에 빈 이메일로 제출하면 에러 메시지가 나오는지 확인해줘"
> "결제 3단계 흐름을 실행해서 마지막까지 완료되는지 테스트해줘"
```

### 인증된 웹 앱 작업

API 없이 직접 웹 앱에 접근

```
> "Google Docs에 로그인해서 문서 'Project Plan'의 첫 번째 테이블을 읽어줘"
> "Notion 데이터베이스에서 완료된 태스크 수를 세어줘"
```

### 데이터 추출

웹 페이지 → CSV, JSON, 구조화된 데이터

```
> "Amazon 베스트셀러 순위 1-20위의 상품명, 가격, 평점을 추출해줘"
```

### 작업 자동화

반복 작업을 자동으로

```
> "Google Sheets 스프레드시트의 각 행마다 이메일 주소로 회사명을 검색해줘"
> "여러 사이트에서 가격을 비교해서 결과를 표로 정리해줘"
```

### GIF 기록

클릭 흐름을 GIF로 저장해서 팀에 공유하거나 버그 재현 영상으로 쓸 수 있어요:

```
> "이 체크아웃 흐름을 GIF로 기록해줘"
```

---

## 필수 요구사항

| 항목 | 최소 버전 | 주의사항 |
|------|----------|---------|
| Chrome 브라우저 | 최신 | Brave, Arc, Edge 미지원 (Chromium이지만 호환 불가) |
| Claude in Chrome 확장 | v1.0.36+ | Chrome 웹스토어에서 설치 |
| Claude Code | v2.0.73+ | `claude --version` 확인 |
| Anthropic 플랜 | Pro 이상 | 직접 Anthropic 구독만 가능 |
| **불가능한 경우** | - | AWS Bedrock, Google Vertex, Mistral 등 3rd party 제공자 |
| **미지원** | - | WSL (Windows Subsystem for Linux) |

---

## 시작하기

### 1단계: Chrome 확장 설치

[Claude in Chrome - Chrome 웹스토어](https://chromewebstore.google.com/)에서 공식 확장을 설치하세요.

### 2단계: Claude Code에서 활성화

터미널에서

```bash
claude --chrome
```

또는 기존 Claude Code 세션에서:

```
/chrome
```

### 3단계: 기본 활성화 설정

처음 실행하면 활성화 옵션을 물어봐요:

```
Choose Chrome integration preference:
> Default on (모든 세션에서 자동 활성화)
  Session only (이 세션만)
  Disabled (비활성화)
```

**권장**: "기본 활성화"를 선택하면 `claude --chrome` 없이 그냥 `/chrome`만 입력해도 돼요.

### 4단계: 확인

```
> /chrome
Chrome integration is now active. Opening new tab...
```

새 탭이 열리고 브라우저 대기 모드가 되면 성공이에요.

---

## 예제 워크플로우

### 예 1: 로컬 웹 앱 테스트

```
> "localhost:3000의 회원가입 폼을 테스트해줘.
> 빈 필드로 제출했을 때 필수 입력 에러가 나오는지 확인해.
> 그 다음 유효한 이메일과 비밀번호로 제출해줘."
```

localhost:3000에 접속해서 빈 필드 제출 → 에러 확인 → 유효한 데이터 입력 → 결과 스크린샷까지 알아서 처리해줘요.

### 예 2: 콘솔 디버깅

```
> "이 페이지의 콘솔에 에러가 있어.
> 에러 메시지를 읽고, 원인이 뭔지 코드와 함께 설명해줘"
```

결과:

```
콘솔 에러: "Uncaught TypeError: Cannot read properties of undefined (reading 'map')"

원인 분석:
- 파일: src/components/UserList.tsx:42
- 상황: users 배열이 undefined인 상태에서 map() 호출
- 근본 원인: useEffect에서 API 응답을 기다리지 않음

권장 수정:
const [users, setUsers] = useState([]);  // 초기값 설정
```

### 예 3: 양식 자동 작성

CSV 데이터를 웹 폼에 입력

```
> "CRM 시스템의 고객 추가 폼을 열어줘.
> 이 CSV 데이터를 각 행마다 입력해줘:
> 이름, 이메일, 전화번호, 회사명
> Alice,alice@example.com,010-1234-5678,Tech Corp
> Bob,bob@example.com,010-2345-6789,Innovation Inc"
```

각 행을 자동으로 입력하고 제출해줘요.

### 예 4: Google Docs 작성

```
> "Google Docs에 로그인해서 '2025년 로드맵' 문서를 열어줘.
> 현재 코드의 주요 변경 사항을 마크다운 형식으로
> 그 문서의 '개발 진행' 섹션에 추가해줄래?"
```

### 예 5: 데이터 추출

```
> "이 GitHub 저장소의 이슈 목록을 열어줘.
> 오픈된 모든 이슈의 제목, 작성자, 라벨을 CSV로 뽑아줘"
```

### 예 6: 다중 사이트 워크플로우

```
> "1. 회사 캘린더에 로그인해서 내일의 일정을 확인해줘
> 2. 각 미팅의 회사명을 Crunchbase에서 검색해줘
> 3. 회사 정보(창립년도, 투자액, 직원 수)를 정리해줘
> 4. 결과를 Google Sheets에 추가해줄래?"
```

### 예 7: GIF 데모 녹화

```
> "이 온라인 쇼핑몰의 장바구니 추가부터 결제까지 흐름을 GIF로 기록해줘.
> 해상도는 1920x1080, 속도는 2배 정도로"
```

---

## 사이트별 권한 관리

특정 사이트에 접근하려면 권한을 미리 설정해두세요.

### Chrome 확장 설정 열기

1. Chrome 주소창 옆 "확장 프로그램" 아이콘 클릭
2. "Claude in Chrome" 옆 톱니바퀴 아이콘 → "확장 프로그램 옵션"

### 권한 설정

| 설정 | 설명 |
|------|------|
| **All sites** | 모든 웹사이트 접근 (권장하지 않음) |
| **Specific sites** | 특정 도메인만 허용 (권장) |
| **On click** | 확장 아이콘을 클릭할 때만 활성화 |

**권장 설정**

```
localhost:3000          (로컬 개발)
github.com              (리포지토리 확인)
docs.anthropic.com      (공식 문서)
```

---

## 문제 해결

### 확장 프로그램이 감지되지 않음

**증상**: `Chrome extension not found` 에러

**해결 방법**

1. Chrome 웹스토어에서 "Claude in Chrome" 설치 확인
2. Chrome 버전 확인: `chrome://version/`
3. 확장 프로그램 비활성화 → 다시 활성화
4. Chrome 재시작

```bash
# Mac
killall "Google Chrome"
open -a "Google Chrome"

# Linux
killall chrome
chrome &
```

### 브라우저가 응답하지 않음

**증상**: Claude가 명령을 보냈는데 브라우저가 반응 없음

**해결 방법**

1. **모달 확인**: 로그인 팝업이나 알림이 떠있나요? → 수동으로 처리
2. **새 탭 열기**: Chrome에서 새 탭 생성 (`Cmd+T` / `Ctrl+T`)
3. **확장 재활성화**: Claude Code에서 `/chrome` 재실행
4. **Port 확인**: 명명된 파이프(macOS) 또는 IPC 포트 충돌 → 포트 변경

### 연결 끊김

**증상**: 작업 중간에 "Connection lost" 메시지

**해결 방법**

```
/chrome
> "확장 프로그램 다시 연결"
```

또는 완전히 재설정:

```bash
# Claude Code 재시작
claude --chrome

# 또는 기존 세션에서
/chrome
```

### Windows 특정 문제

WSL에서는 Chrome 통합이 안 돼요. 네이티브 Windows 환경에서만 가능해요. WSL에서 작업하려면:

1. Windows 탐색기에서 로컬 폴더 마운트
2. Windows의 Claude Code 사용
3. 또는 Docker 컨테이너 내 Ubuntu + Chrome

---

## 고급 사용법

### 복합 작업 체이닝

장시간의 복합 작업은 단계별로 나누면 안정적이에요:

```
> "1단계: 로그인 페이지 테스트 완료 후 결과 보고해줘"
[완료 후 확인]

> "2단계: 대시보드 UI 검증해줄래?"
[완료 후 확인]
```

### 에러 로그 저장

```
> "이 페이지의 전체 콘솔 출력을 텍스트 파일로 저장해줄래?
> 파일명: console-output.txt"
```

### 스크린샷 및 비디오

```
> "현재 페이지의 스크린샷을 찍어줄래?"
> "이 상호작용을 비디오로 기록해줘"
```

---

## 주의사항

### 민감한 정보 조심

로그인 정보, API 키, 개인 데이터가 포함된 페이지에서는 주의하세요.

```
❌ "Gmail에 로그인해서 모든 이메일을 읽어줘"
✅ "테스트 Gmail 계정에 로그인해서 특정 폴더의 이메일만 확인해줘"
```

### 구독 플랜 확인

Chrome 통합은 **Pro 플랜 이상**에서만 돼요. `/usage`로 현재 플랜을 확인하세요.

### 자동화 규칙 준수

하루에 수백 개 요청을 보내면 막히는 사이트가 있어요. 그럴 땐:

- 단계마다 수동 확인 포인트 추가
- CAPTCHA는 직접 처리
- 요청 사이에 딜레이 추가

```
> "각 요청 사이에 3초씩 쉬어줄래?"
```

---

## 다음 단계

✅ **Chrome 확장 설치** → `/chrome` 테스트
✅ **간단한 웹 앱 테스트** → 로컬 폼 유효성 검사
✅ **라이브 디버깅** → 페이지 콘솔 에러 확인
✅ **데이터 추출** → 웹 페이지 → CSV
✅ **자동화 작업** → 복합 워크플로우

## 참고 자료

| 자료 | 링크 |
|------|------|
| Claude in Chrome - 공식 | [Chrome 웹스토어](https://chromewebstore.google.com/) |
| Claude Code 공식 문서 | [code.claude.com/docs](https://code.claude.com/docs/) |
| Chrome 확장 권한 가이드 | [developer.chrome.com/extensions](https://developer.chrome.com/docs/extensions/) |
| 관련 가이드 | [라이브 디버깅과 테스트](./06-agent-teams-cowork.md) |
