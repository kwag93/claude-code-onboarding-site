# 33. Computer Use — Claude가 컴퓨터를 직접 조작하다

Claude가 스크린샷을 보고, 마우스를 클릭하고, 키보드를 입력하는 기능이에요. 데스크톱 환경을 자율적으로 제어할 수 있죠.

> "코드 작성"을 넘어 "화면을 보고 행동하는" 단계. Claude가 사람처럼 컴퓨터를 사용합니다.

---

## Computer Use란?

Claude API의 베타 기능으로, Claude에게 **데스크톱 환경을 제어하는 도구**를 제공하는 거예요.

| 기능 | 설명 |
|------|------|
| **스크린샷 캡처** | 화면에 무엇이 표시되어 있는지 확인 |
| **마우스 제어** | 클릭, 드래그, 커서 이동 |
| **키보드 입력** | 텍스트 입력, 단축키 사용 |
| **데스크톱 자동화** | 모든 애플리케이션과 상호작용 |

기존 Claude Code가 **코드 편집과 터미널 명령**에 집중한다면, Computer Use는 **GUI 환경 전체**를 다룰 수 있어요.

---

## Chrome 확장 프로그램과 뭐가 다른가요?

| 비교 항목 | Chrome 확장 프로그램 | Computer Use |
|-----------|---------------------|--------------|
| **제어 범위** | Chrome 브라우저만 | 데스크톱 전체 (브라우저 + 앱) |
| **동작 방식** | DOM API 직접 접근 | 스크린샷 기반 시각 인식 + 마우스/키보드 |
| **설정** | 확장 프로그램 설치 | Docker 컨테이너 또는 VM 구성 |
| **용도** | 웹 앱 테스트/디버깅 | 범용 데스크톱 자동화 |
| **정밀도** | 높음 (DOM 직접 조작) | 중간 (시각 기반 좌표 추론) |

> Chrome 확장은 "웹 전문가", Computer Use는 "만능 조작자"라고 생각하면 돼요.

---

## 어떻게 동작하나요?

Computer Use의 핵심은 **에이전트 루프**예요. Claude가 도구를 반복 호출하면서 작업을 완료합니다.

```text
1. 사용자 요청 전달 + Computer Use 도구 제공
     ↓
2. Claude가 도구 사용 결정 (예: 스크린샷 촬영)
     ↓
3. 앱이 도구 실행 → 결과(스크린샷)를 Claude에게 반환
     ↓
4. Claude가 결과 분석 → 다음 행동 결정 (예: 좌표 클릭)
     ↓
5. 작업 완료될 때까지 3-4 반복
```

중요한 점: **Claude가 직접 컴퓨터에 접속하지 않아요.** 여러분의 앱이 중간에서 Claude의 요청을 받아 실제 환경에서 실행하고, 결과를 돌려주는 구조예요.

---

## 지원 모델과 버전

| 모델 | 도구 버전 | 베타 플래그 |
|------|----------|------------|
| Claude Opus 4.6, Sonnet 4.6, Opus 4.5 | `computer_20251124` | `computer-use-2025-11-24` |
| 그 외 지원 모델 | `computer_20250124` | `computer-use-2025-01-24` |

!!! warning "버전 호환성 주의"
    모델에 맞는 도구 버전을 사용해야 해요. 구버전 도구는 신규 모델과 호환이 보장되지 않아요.

---

## 사용 가능한 액션

### 기본 액션 (모든 버전)

| 액션 | 설명 | 예시 |
|------|------|------|
| `screenshot` | 현재 화면 캡처 | 화면 상태 확인 |
| `left_click` | 좌표 클릭 | `[500, 300]` 위치 클릭 |
| `type` | 텍스트 입력 | `"Hello, world!"` 입력 |
| `key` | 키 또는 조합 입력 | `"ctrl+s"` 저장 |
| `mouse_move` | 커서 이동 | 특정 좌표로 이동 |

### 확장 액션 (`computer_20250124`+)

| 액션 | 설명 |
|------|------|
| `scroll` | 상하좌우 스크롤 (양 조절 가능) |
| `left_click_drag` | 드래그 앤 드롭 |
| `right_click` / `middle_click` | 우클릭, 중간 클릭 |
| `double_click` / `triple_click` | 더블/트리플 클릭 |
| `hold_key` | 키를 일정 시간 누르고 있기 |
| `wait` | 액션 사이 대기 |

### Opus 4.5+ 전용 (`computer_20251124`)

| 액션 | 설명 |
|------|------|
| `zoom` | 특정 화면 영역을 원본 해상도로 확대 확인 |

실제 JSON 형태로 보면 이런 식이에요:

```json
{ "action": "screenshot" }
```

```json
{ "action": "left_click", "coordinate": [500, 300] }
```

```json
{ "action": "type", "text": "Hello, world!" }
```

```json
{
  "action": "scroll",
  "coordinate": [500, 400],
  "scroll_direction": "down",
  "scroll_amount": 3
}
```

---

## Quick Start — 최소 코드로 시작하기

Computer Use는 **베타 기능**이라 베타 헤더가 필요해요.

### Python

```python
import anthropic

client = anthropic.Anthropic()

response = client.beta.messages.create(
    model="claude-opus-4-6",
    max_tokens=1024,
    tools=[
        {
            "type": "computer_20251124",
            "name": "computer",
            "display_width_px": 1024,
            "display_height_px": 768,
            "display_number": 1,
        },
        {"type": "text_editor_20250728", "name": "str_replace_based_edit_tool"},
        {"type": "bash_20250124", "name": "bash"},
    ],
    messages=[{"role": "user", "content": "바탕화면에 고양이 사진을 저장해줘."}],
    betas=["computer-use-2025-11-24"],
)
print(response)
```

### curl

```bash
curl https://api.anthropic.com/v1/messages \
  -H "content-type: application/json" \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "anthropic-beta: computer-use-2025-11-24" \
  -d '{
    "model": "claude-opus-4-6",
    "max_tokens": 1024,
    "tools": [
      {
        "type": "computer_20251124",
        "name": "computer",
        "display_width_px": 1024,
        "display_height_px": 768,
        "display_number": 1
      },
      { "type": "text_editor_20250728", "name": "str_replace_based_edit_tool" },
      { "type": "bash_20250124", "name": "bash" }
    ],
    "messages": [
      { "role": "user", "content": "Save a picture of a cat to my desktop." }
    ]
  }'
```

---

## 에이전트 루프 구현하기

Computer Use의 핵심 패턴인 **에이전트 루프**를 간략하게 구현하면 이래요:

```python
async def computer_use_loop(
    model: str,
    messages: list[dict],
    max_iterations: int = 10,
):
    client = anthropic.Anthropic()

    tools = [
        {
            "type": "computer_20251124",
            "name": "computer",
            "display_width_px": 1024,
            "display_height_px": 768,
        },
        {"type": "text_editor_20250728", "name": "str_replace_based_edit_tool"},
        {"type": "bash_20250124", "name": "bash"},
    ]

    for _ in range(max_iterations):
        response = client.beta.messages.create(
            model=model,
            max_tokens=4096,
            messages=messages,
            tools=tools,
            betas=["computer-use-2025-11-24"],
        )

        messages.append({"role": "assistant", "content": response.content})

        # 도구 호출 결과 수집
        tool_results = []
        for block in response.content:
            if block.type == "tool_use":
                result = execute_tool(block.name, block.input)  # 직접 구현 필요
                tool_results.append({
                    "type": "tool_result",
                    "tool_use_id": block.id,
                    "content": result,
                })

        # 도구 호출이 없으면 작업 완료
        if not tool_results:
            return messages

        messages.append({"role": "user", "content": tool_results})

    return messages
```

> `max_iterations`를 반드시 설정하세요. 무한 루프는 예상치 못한 API 비용으로 이어질 수 있어요.

---

## 실행 환경 구성

Claude는 컴퓨터에 직접 접속하지 않아요. **샌드박스 환경**을 구성해야 합니다.

### 레퍼런스 구현 (Docker)

Anthropic이 제공하는 [레퍼런스 구현](https://github.com/anthropics/anthropic-quickstarts/tree/main/computer-use-demo)을 사용하면 빠르게 시작할 수 있어요.

포함된 것들:

- Docker 컨테이너 (가상 디스플레이 + 데스크톱 환경)
- Computer Use 도구 구현체
- 에이전트 루프 코드
- 웹 인터페이스

### 직접 구성할 때 필요한 것

| 구성 요소 | 설명 |
|-----------|------|
| **가상 디스플레이** | Xvfb 등으로 가상 X11 디스플레이 구성 |
| **데스크톱 환경** | 윈도우 매니저(Mutter) + 패널(Tint2) |
| **애플리케이션** | Firefox, 텍스트 편집기 등 필요한 앱 |
| **도구 구현** | Claude의 추상 요청을 실제 동작으로 변환 |
| **에이전트 루프** | Claude와 환경 간 통신 처리 |

---

## 보안 고려사항

!!! danger "Computer Use는 고유한 보안 위험이 있어요"
    일반 API 기능과는 다른 수준의 주의가 필요합니다.

### 반드시 지켜야 할 것들

1. **격리된 환경 사용** — 전용 VM 또는 컨테이너에서 최소 권한으로 실행
2. **민감 데이터 차단** — 계정 로그인 정보 등 민감한 데이터 접근 제한
3. **네트워크 제한** — 허용된 도메인만 접근 가능하도록 설정
4. **사람의 확인** — 금융 거래, 약관 동의 등 중요한 결정은 반드시 사람이 확인

### 프롬프트 인젝션 주의

웹페이지나 이미지에 포함된 악의적 지시가 Claude의 동작을 바꿀 수 있어요. Anthropic은 자동 분류기로 방어하지만, 완벽하지는 않습니다.

---

## 성능 최적화 팁

### 프롬프팅

- **명확한 단계별 지시**를 주세요. 복잡한 작업일수록 구체적으로.
- 매 단계마다 스크린샷을 찍어서 결과를 확인하도록 지시하면 정확도가 올라가요:

```text
각 단계 후 스크린샷을 찍고 결과를 확인하세요.
"X 단계를 평가했습니다..." 형태로 검증하고,
확인된 후에만 다음 단계로 넘어가세요.
```

- UI 요소(드롭다운, 스크롤바) 조작이 어려우면 **키보드 단축키**를 사용하도록 유도하세요.

### 해상도 설정

| 용도 | 권장 해상도 |
|------|------------|
| 일반 데스크톱 작업 | 1024x768 또는 1280x720 |
| 웹 애플리케이션 | 1280x800 또는 1366x768 |
| 최대 권장 | 1920x1080 이하 |

### 좌표 스케일링

API는 이미지를 최대 1568px(긴 변) / ~115만 픽셀로 자동 축소해요. Claude가 축소된 이미지 좌표를 반환하므로, 원본 해상도로 **역변환**이 필요합니다:

```python
import math

def get_scale_factor(width, height):
    long_edge = max(width, height)
    total_pixels = width * height
    long_edge_scale = 1568 / long_edge
    total_pixels_scale = math.sqrt(1_150_000 / total_pixels)
    return min(1.0, long_edge_scale, total_pixels_scale)

scale = get_scale_factor(screen_width, screen_height)

# Claude 좌표 → 실제 화면 좌표
def execute_click(x, y):
    screen_x = x / scale
    screen_y = y / scale
    perform_click(screen_x, screen_y)
```

---

## 알려진 제한사항

| 제한 | 설명 |
|------|------|
| **지연 시간** | 사람의 직접 조작보다 느림. 속도가 중요하지 않은 작업에 적합 |
| **시각 정확도** | 좌표 추론 시 실수 가능. Thinking 활성화로 추론 과정 확인 가능 |
| **도구 선택** | 여러 앱을 동시에 다루면 신뢰도 하락 |
| **스프레드시트** | 셀 선택에 정밀 마우스 컨트롤 필요 |
| **소셜 미디어** | 계정 생성, 콘텐츠 게시 등은 제한됨 |

---

## 비용

일반 도구 사용 요금 + 추가 비용이 있어요.

| 항목 | 토큰 수 |
|------|---------|
| 시스템 프롬프트 오버헤드 | 466~499 토큰 |
| Computer Use 도구 정의 | 735 토큰/도구 |
| 스크린샷 이미지 | Vision 요금 별도 |

> bash, text editor 도구를 함께 사용하면 각각의 토큰 비용도 추가돼요.

---

## 실무 활용 시나리오

### QA 자동화

```text
> "이 웹앱에서 회원가입 → 로그인 → 프로필 수정 흐름을 테스트해줘"
```

브라우저를 열고 직접 클릭하며 E2E 테스트를 수행해요.

### 레거시 시스템 자동화

API가 없는 오래된 사내 시스템도 GUI로 조작할 수 있어요.

```text
> "ERP 시스템에서 이번 달 매출 보고서를 다운로드해줘"
```

### 크로스 앱 워크플로우

여러 앱을 넘나드는 작업도 가능해요.

```text
> "이메일에서 첨부파일을 다운로드하고, 스프레드시트에서 열어서 합계를 계산해줘"
```

### 스크린샷 기반 디버깅

```text
> "이 화면에서 레이아웃이 깨져 보이는 부분을 찾아줘"
```

---

## 다음 단계

- [Anthropic 레퍼런스 구현](https://github.com/anthropics/anthropic-quickstarts/tree/main/computer-use-demo)으로 바로 시작하기
- [공식 문서](https://platform.claude.com/docs/en/agents-and-tools/tool-use/computer-use-tool)에서 전체 API 레퍼런스 확인
- [Chrome 확장 프로그램 가이드](23-chrome-extension.md)와 비교해서 적합한 도구 선택하기
- [직접 만들기 가이드](31-build-your-own.md)로 나만의 Computer Use 환경 구축하기
