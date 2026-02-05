# Claude Code 온보딩 가이드 - 배포 프로젝트

MkDocs Material + GitHub Pages를 사용한 문서 사이트입니다.

## 🚀 빠른 시작

### 1. GitHub 저장소 생성

```bash
# 새 저장소 생성 후
git clone https://github.com/your-username/claude-code-onboarding.git
cd claude-code-onboarding

# 이 폴더의 내용을 복사
cp -r /path/to/this/folder/* .
```

### 2. mkdocs.yml 수정

`mkdocs.yml`에서 다음 부분을 수정하세요:

```yaml
site_url: https://your-username.github.io/claude-code-onboarding/
repo_url: https://github.com/your-username/claude-code-onboarding
```

### 3. GitHub Pages 활성화

1. GitHub 저장소 → Settings → Pages
2. Source: **Deploy from a branch**
3. Branch: **gh-pages** / **root**

### 4. 배포

```bash
git add .
git commit -m "Initial commit: Claude Code onboarding docs"
git push origin main
```

푸시하면 GitHub Actions가 자동으로 배포합니다.

---

## 📁 프로젝트 구조

```
claude-code-onboarding-site/
├── .github/
│   └── workflows/
│       └── deploy.yml      # GitHub Actions 배포 워크플로우
├── docs/
│   ├── index.md            # 홈페이지 (README)
│   ├── 01-quick-start.md   # 빠른 시작
│   ├── 02-ai-mindset.md    # AI 마인드셋
│   ├── 03-workflows.md     # 실무 워크플로우
│   ├── 04-evolving.md      # 나아가는 방법
│   ├── 05-prompt-tips.md   # 요청하는 방법
│   └── stylesheets/
│       └── extra.css       # 커스텀 스타일
├── mkdocs.yml              # MkDocs 설정
├── requirements.txt        # Python 의존성
└── README.md               # 이 파일
```

---

## 🔧 로컬 개발

### 환경 설정

```bash
# 가상환경 생성 (선택)
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 의존성 설치
pip install -r requirements.txt
```

### 로컬 서버 실행

```bash
mkdocs serve
```

브라우저에서 http://127.0.0.1:8000 접속

### 빌드 테스트

```bash
mkdocs build
```

`site/` 폴더에 정적 HTML이 생성됩니다.

---

## 📌 버전 관리 (mike)

### 새 버전 배포

**방법 1: GitHub Actions 수동 실행**

1. Actions 탭 → Deploy MkDocs
2. "Run workflow" 클릭
3. 버전 입력 (예: `v1.0`)
4. "기본 버전으로 설정" 체크

**방법 2: 로컬에서 배포**

```bash
# v1.0 버전 배포
mike deploy --push v1.0

# v1.0을 latest로 설정
mike deploy --push --update-aliases v1.0 latest
mike set-default --push latest
```

### 버전 목록 확인

```bash
mike list
```

### 특정 버전 삭제

```bash
mike delete --push v1.0
```

---

## 🎨 커스터마이징

### 테마 색상 변경

`mkdocs.yml`에서:

```yaml
theme:
  palette:
    primary: deep purple # 변경 가능: red, pink, purple, indigo, blue...
    accent: amber # 강조색
```

### 네비게이션 수정

`mkdocs.yml`의 `nav:` 섹션 수정

### 로고 추가

```yaml
theme:
  logo: assets/logo.png
  favicon: assets/favicon.png
```

`docs/assets/` 폴더에 이미지 추가

---

## 📝 문서 작성 팁

### 경고 박스 (Admonition)

```markdown
!!! note "참고"
이것은 참고 사항입니다.

!!! warning "주의"
이것은 경고 사항입니다.

!!! tip "팁"
유용한 팁입니다.
```

### 코드 블록 (복사 버튼 자동)

````markdown
```python
def hello():
    print("Hello, World!")
```
````

### 탭 컨텐츠

````markdown
=== "Python"
`python
    print("Hello")
    `

=== "JavaScript"
`javascript
    console.log("Hello");
    `
````

---

## 🔗 참고 링크

- [MkDocs Material 공식 문서](https://squidfunk.github.io/mkdocs-material/)
- [mike 버전 관리](https://github.com/jimporter/mike)
- [GitHub Pages 문서](https://docs.github.com/en/pages)

---

## 📜 라이선스

이 프로젝트의 문서는 내부 팀 교육용으로 작성되었습니다.
