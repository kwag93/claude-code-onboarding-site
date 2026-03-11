# 26. 키보드 단축키 커스터마이징

마우스 없이 Claude Code를 100% 활용하는 방법이에요.

> `/keybindings`로 나만의 단축키를 설정하세요.
> Vim 스타일 바인딩, 코드(Chords), 기본 단축키 해제까지 전부 가능합니다.
>
> 공식 문서: [code.claude.com/docs/en/keybindings](https://code.claude.com/docs/en/keybindings)

---

## 필수 단축키 총정리

설정을 바꾸기 전에 기본 단축키부터 확실히 알아두세요.

### 매일 쓰는 핵심 7개

| 단축키 | 기능 | 왜 중요한가 |
|--------|------|-------------|
| `Esc` | 생성 중지 | Claude가 잘못된 방향으로 갈 때 즉시 멈춤 |
| `Esc Esc` | 리와인드 | 코드 변경을 안전하게 되돌리기 |
| `Shift+Tab` | 모드 전환 | Normal → Plan → Auto 순환 |
| `Ctrl+G` | 외부 에디터 | 긴 프롬프트를 VS Code/Vim에서 편집 |
| `Ctrl+R` | 역방향 검색 | 이전 프롬프트를 빠르게 재사용 |
| `Ctrl+V` | 이미지 붙여넣기 | 스크린샷으로 버그 설명 |
| `Tab` | 제안 수락 | Claude의 자동완성 제안 수락 |

### 네비게이션 & 작업 관리

| 단축키 | 기능 |
|--------|------|
| `Ctrl+C` | 현재 작업 취소 |
| `Ctrl+D` | Claude Code 종료 |
| `Ctrl+T` | 할 일 목록 토글 |
| `Ctrl+O` | 전체 트랜스크립트 토글 (thinking 과정 포함) |
| `Ctrl+B` | 현재 작업을 백그라운드로 |
| `Ctrl+S` | 프롬프트 임시 저장 (stash) |
| `Cmd+P` / `Meta+P` | 모델 선택기 열기 |
| `Cmd+T` / `Meta+T` | Extended Thinking 토글 |

### 모델 선택기 안에서

| 단축키 | 기능 |
|--------|------|
| `←` `→` | Effort Level 조절 (low ↔ high) |

---

## 커스터마이징 방법

### /keybindings 명령어

```text
/keybindings
```

`~/.claude/keybindings.json` 파일을 생성하거나 열어줘요. 변경 사항은 **자동으로 감지**되어 Claude Code를 재시작할 필요 없어요.

### keybindings.json 구조

```json
{
  "$schema": "https://www.schemastore.org/claude-code-keybindings.json",
  "$docs": "https://code.claude.com/docs/en/keybindings",
  "bindings": [
    {
      "context": "Chat",
      "bindings": {
        "ctrl+e": "chat:externalEditor",
        "ctrl+u": null
      }
    }
  ]
}
```

| 필드 | 설명 |
|------|------|
| `$schema` | JSON 스키마 URL (에디터 자동완성용) |
| `bindings` | 바인딩 블록 배열 |
| `context` | 바인딩이 적용되는 컨텍스트 |
| `bindings` (내부) | `"키": "액션"` 또는 `"키": null` (해제) |

### 컨텍스트 종류

바인딩은 특정 상황에서만 작동해요. 컨텍스트로 구분합니다.

| 컨텍스트 | 설명 |
|----------|------|
| `Global` | 앱 전체에서 작동 |
| `Chat` | 메인 채팅 입력 |
| `Autocomplete` | 자동완성 메뉴 열린 상태 |
| `Confirmation` | 권한 확인 다이얼로그 |
| `HistorySearch` | Ctrl+R 검색 모드 |
| `Task` | 백그라운드 작업 실행 중 |
| `DiffDialog` | Diff 뷰어 |
| `ModelPicker` | 모델 선택기 |
| `Select` | 일반 선택/리스트 |
| `Plugin` | 플러그인 다이얼로그 |
| `Settings` | 설정 메뉴 |
| `Help` | 도움말 메뉴 |
| `Transcript` | 트랜스크립트 뷰어 |
| `Footer` | 하단 인디케이터 (작업, 팀, diff) |
| `MessageSelector` | 리와인드/요약 메시지 선택 |
| `Tabs` | 탭 네비게이션 |
| `Attachments` | 이미지/첨부 바 |
| `ThemePicker` | 테마 선택기 |

---

## 키 표기법

### 수정자 키

`+`로 조합해요.

| 수정자 | 별칭 |
|--------|------|
| `ctrl` | `control` |
| `alt` | `opt`, `option` |
| `shift` | - |
| `meta` | `cmd`, `command` |

```text
ctrl+k          Ctrl + K
shift+tab       Shift + Tab
meta+p          Command + P (macOS)
ctrl+shift+c    Ctrl + Shift + C
```

### 대문자

대문자 단독 사용은 Shift를 의미해요: `K` = `shift+k`

수정자와 함께 쓰면 Shift 아님: `ctrl+K` = `ctrl+k`

### 코드 바인딩 (Chords)

키를 순서대로 누르는 조합이에요. 공백으로 구분합니다.

```text
ctrl+k ctrl+s    Ctrl+K 누르고, 떼고, Ctrl+S
```

### 특수 키

`escape`, `enter`, `tab`, `space`, `up`, `down`, `left`, `right`, `backspace`, `delete`

---

## 실전 레시피

### 레시피 1: 외부 에디터를 Ctrl+E로 변경

기본 `Ctrl+G` 대신 `Ctrl+E`로 외부 에디터를 여는 설정이에요.

```json
{
  "bindings": [
    {
      "context": "Chat",
      "bindings": {
        "ctrl+e": "chat:externalEditor",
        "ctrl+g": null
      }
    }
  ]
}
```

### 레시피 2: 코드 바인딩으로 빠른 액션

`Ctrl+K → Ctrl+T`로 할 일 목록, `Ctrl+K → Ctrl+O`로 트랜스크립트 토글.

```json
{
  "bindings": [
    {
      "context": "Global",
      "bindings": {
        "ctrl+k ctrl+t": "app:toggleTodos",
        "ctrl+k ctrl+o": "app:toggleTranscript"
      }
    }
  ]
}
```

### 레시피 3: 기본 단축키 해제

쓰지 않는 단축키가 다른 프로그램과 충돌할 때 `null`로 해제하세요.

```json
{
  "bindings": [
    {
      "context": "Chat",
      "bindings": {
        "ctrl+s": null
      }
    }
  ]
}
```

### 레시피 4: 확인 다이얼로그 커스터마이징

권한 확인 화면에서 Y/N 외에 추가 바인딩을 설정해요.

```json
{
  "bindings": [
    {
      "context": "Confirmation",
      "bindings": {
        "ctrl+e": "confirm:toggleExplanation"
      }
    }
  ]
}
```

---

## 전체 액션 목록

### 앱 전체 (Global)

| 액션 | 기본 키 | 설명 |
|------|---------|------|
| `app:interrupt` | `Ctrl+C` | 작업 취소 |
| `app:exit` | `Ctrl+D` | 종료 |
| `app:toggleTodos` | `Ctrl+T` | 할 일 목록 |
| `app:toggleTranscript` | `Ctrl+O` | 트랜스크립트 |

### 채팅 (Chat)

| 액션 | 기본 키 | 설명 |
|------|---------|------|
| `chat:submit` | `Enter` | 메시지 전송 |
| `chat:cancel` | `Escape` | 입력 취소 |
| `chat:cycleMode` | `Shift+Tab` | 모드 순환 |
| `chat:modelPicker` | `Cmd+P` | 모델 선택기 |
| `chat:thinkingToggle` | `Cmd+T` | Extended Thinking |
| `chat:externalEditor` | `Ctrl+G` | 외부 에디터 |
| `chat:stash` | `Ctrl+S` | 프롬프트 저장 |
| `chat:imagePaste` | `Ctrl+V` | 이미지 붙여넣기 |
| `chat:undo` | `Ctrl+_` | 실행 취소 |

### 자동완성 (Autocomplete)

| 액션 | 기본 키 | 설명 |
|------|---------|------|
| `autocomplete:accept` | `Tab` | 제안 수락 |
| `autocomplete:dismiss` | `Escape` | 메뉴 닫기 |
| `autocomplete:previous` | `Up` | 이전 제안 |
| `autocomplete:next` | `Down` | 다음 제안 |

### 히스토리 검색 (HistorySearch)

| 액션 | 기본 키 | 설명 |
|------|---------|------|
| `historySearch:next` | `Ctrl+R` | 다음 결과 |
| `historySearch:accept` | `Escape`, `Tab` | 선택 확정 |
| `historySearch:cancel` | `Ctrl+C` | 검색 취소 |
| `historySearch:execute` | `Enter` | 바로 실행 |

### 작업 (Task)

| 액션 | 기본 키 | 설명 |
|------|---------|------|
| `task:background` | `Ctrl+B` | 백그라운드로 |

### Diff 뷰어 (DiffDialog)

| 액션 | 기본 키 | 설명 |
|------|---------|------|
| `diff:dismiss` | `Escape` | 닫기 |
| `diff:previousSource` | `Left` | 이전 소스 |
| `diff:nextSource` | `Right` | 다음 소스 |
| `diff:previousFile` | `Up` | 이전 파일 |
| `diff:nextFile` | `Down` | 다음 파일 |
| `diff:viewDetails` | `Enter` | 상세 보기 |

### 레시피 5: 보이스 Push-to-Talk 키 변경

보이스 모드의 Push-to-Talk 키를 스페이스바 대신 다른 키로 바꿔요.

```json
{
  "bindings": [
    {
      "context": "Chat",
      "bindings": {
        "ctrl+m": "voice:pushToTalk"
      }
    }
  ]
}
```

> 보이스 모드에 대한 자세한 내용은 [27. 보이스 모드](./27-voice-mode.md)를 참고하세요.

---

## Vim 모드와의 관계

`/vim`으로 Vim 모드를 켜면 h/j/k/l, dd, ciw 같은 Vim 키가 작동해요.

**독립 동작 원칙**

- **Vim 모드**: 텍스트 입력 레벨 (커서 이동, 모드 전환, 모션)
- **키바인딩**: 컴포넌트 레벨 (할 일 토글, 메시지 전송 등)
- `Escape`는 Vim에서 INSERT → NORMAL 전환이지, `chat:cancel`이 아님
- 대부분의 `Ctrl+` 조합은 Vim 모드를 통과해서 키바인딩 시스템으로 전달
- Vim NORMAL 모드에서 `?`는 도움말 메뉴 표시

---

## 터미널 멀티플렉서 충돌

tmux나 screen을 쓴다면 주의하세요.

| 단축키 | 충돌 | 해결 방법 |
|--------|------|-----------|
| `Ctrl+B` | tmux prefix | 두 번 누르면 Claude에 전달 |
| `Ctrl+A` | GNU screen prefix | 다른 키로 리바인딩 |
| `Ctrl+Z` | Unix 프로세스 일시 중지 | 리바인딩 불가 (OS 레벨) |

### 변경 불가능한 단축키

| 단축키 | 이유 |
|--------|------|
| `Ctrl+C` | 하드코딩된 인터럽트 |
| `Ctrl+D` | 하드코딩된 종료 |

---

## 검증

Claude Code가 키바인딩 파일을 자동으로 검증해요. 문제가 있으면 경고를 보여줍니다.

```text
/doctor
```

검증 항목:

- JSON 파싱 에러
- 잘못된 컨텍스트 이름
- 예약된 단축키 충돌
- 터미널 멀티플렉서 충돌
- 같은 컨텍스트 내 중복 바인딩

---

## 다음 단계

- [25. Status Line 커스터마이징](./25-statusline-guide.md) — 하단 바도 내 맘대로
- [13. 파워 유저 70가지 팁](./13-power-user-tips.md) — 더 많은 단축키 활용법
- [16. Hooks 실전 가이드](./16-hooks-practical.md) — 이벤트 기반 자동화
