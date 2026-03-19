# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 이 저장소 소개

Hugo 기반 정적 블로그로, MLOps/ML 플랫폼 엔지니어링 학습 여정을 기록합니다. 콘텐츠는 주로 한국어로 작성되며, 라이브 사이트는 https://keonhoban.github.io/mlops-journey/ 에서 확인할 수 있습니다.

## Hugo 명령어

```bash
# 라이브 리로드로 로컬 서버 실행
hugo server -D

# 사이트 빌드 (/public 디렉토리에 출력)
hugo --minify

# 새 포스트 생성
hugo new posts/<카테고리>/<포스트명>/index.md
```

사용 중인 Hugo 버전: **v0.145.0** (CI에 고정).

## 배포

`main` 브랜치에 push하면 `.github/workflows/deploy.yml`이 트리거되어 `hugo --minify`로 빌드 후 `peaceiris/actions-gh-pages@v3`를 통해 GitHub Pages에 자동 배포됩니다. 별도의 수동 배포 작업은 없습니다.

PaperMod 테마는 git 서브모듈(`themes/PaperMod`)입니다. 클론 시 `git clone --recurse-submodules`를 사용하거나, 이후 `git submodule update --init`을 실행해야 합니다.

## 콘텐츠 구조

```
content/
├── posts/          # 블로그 포스트 (카테고리별 서브디렉토리)
│   ├── airflow/
│   ├── aws/
│   ├── kubernetes/
│   ├── mlflow/
│   ├── mlops-platform-e2e/
│   ├── mlops-platform-feature-store/
│   ├── mlops-platform-gitops/
│   ├── mlops-platform-observability/
│   ├── triton/
│   └── ...         # linux, network, python, online-judge, TroubleShoot 등
└── projects/       # 프로젝트 수준 문서 (11개 프로젝트)
```

각 포스트는 `index.md`(및 선택적으로 이미지)를 포함하는 디렉토리 구조입니다. Front matter는 Hugo 표준 필드(`categories`, `tags`, `weight`)를 사용합니다.

## 주요 설정 파일

- **`config.toml`** — Hugo 설정: base URL, 테마(`PaperMod`), 메뉴, 커스텀 CSS 경로
- **`static/css/custom.css`** — 테이블 스타일링 (테두리, 행 교차 색상)
- **`layouts/partials/footer.html`** — 커스텀 푸터: 스크롤 상단 버튼, 코드 복사 버튼, 테마 토글

## 블로그 시리즈 일관성

여러 포스트가 멀티파트 시리즈를 구성합니다(특히 `mlops-platform-*` 카테고리). 콘텐츠 수정 시:
- 같은 시리즈 내 포스트 간 용어 및 컴포넌트 이름을 일관되게 유지할 것
- 포스트 내 코드 스니펫, 아키텍처 다이어그램, 설정 예시는 실제 참조 저장소/코드 상태를 반영해야 함
- `projects/` 페이지는 각 시리즈를 요약하므로, 대응하는 `posts/` 항목과 내용을 맞춰 유지할 것
