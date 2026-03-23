# Claude Code 온보딩 사이트

## 프로젝트 개요

Zensical(Material for MkDocs 후속) 기반 정적 문서 사이트.
Claude Code를 처음 접하는 팀원을 위한 한국어 온보딩 가이드.

- 배포: https://kwag93.github.io/claude-code-onboarding-site/
- 레포: https://github.com/kwag93/claude-code-onboarding-site
- GA4: G-V8LTX9P6WX

## 빌드 & 개발

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
zensical serve          # 로컬 서버 http://127.0.0.1:8000
zensical build --clean  # 빌드 테스트
```

main 브랜치 push → GitHub Actions 자동 배포.

## 문서 작성 규칙

- 캐주얼 한국어 ("~에요", "~세요"). 과도하게 캐주얼하지 않은 전문적 톤
- `---` 수평선으로 섹션 구분
- `>` 블록쿼트로 핵심 메시지
- 테이블로 비교, 코드 블록에 실전 예제
- 마지막에 "다음 단계" 섹션
- 짧은 문단, 스캔 가능한 구조

## 문서 구조

- `docs/` — 마크다운 문서 (01~31 + 99-release-notes)
- `mkdocs.yml` — nav 구조, 테마, 플러그인 설정
- `overrides/` — 커스텀 템플릿
- `docs/stylesheets/extra.css` — 커스텀 CSS

## 네비게이션 (mkdocs.yml nav)

5개 섹션: 시작하기, 실무 가이드, 확장하기, 철학, 업데이트.
새 문서 추가 시 mkdocs.yml nav + README.md 테이블 + 99-release-notes.md 모두 업데이트.

## Git 워크플로우

- main 브랜치 직접 커밋 금지 → feature 브랜치에서 PR
- 브랜치 네이밍: `docs/<설명>` (예: `docs/v1.8.0-content-overhaul`)
- 커밋: Conventional Commits 한국어 (`docs(scope): 설명`)
- PR은 `gh pr create` 사용 (GitHub 프로젝트)
