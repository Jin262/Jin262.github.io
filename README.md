# github_blog

[Astro](https://astro.build)로 만든 블로그 템플릿입니다. GitHub Pages에 바로 배포할 수 있고, Claude Code로 기술 블로그 글을 쓰고 링크를 스크랩하는 스킬이 들어 있습니다.

## 들어 있는 것

| 기능 | 위치 |
|---|---|
| 홈, 블로그(카테고리·시리즈·목차·RSS), 스크랩, 소개, 404 페이지 | `src/pages/` |
| 글 | `src/content/blog/` (Markdown·MDX) |
| 시리즈 | `src/content/series/<id>.yaml` |
| 스크랩 | `src/content/links/` (`pnpm scrap <url>`로 초안 생성) |
| 사이트 이름, 홈 문구, 프로필, 연락처 | `src/consts.ts` |
| 소개 본문 | `src/content/about.mdx` |
| Claude Code 스킬 | `.claude/skills/` |
| 글쓰기 스킬의 근거 자료(한국 기술 블로그 124편 분석) | `docs/references/` |

## 시작하기

1. 이 저장소를 fork하거나 **Use this template**으로 복사합니다. 저장소 이름을 `<내 아이디>.github.io`로 하면 `https://<내 아이디>.github.io`에서 열립니다.
2. 의존성을 설치합니다. Node.js 22 이상과 pnpm이 필요합니다.

   ```bash
   pnpm install
   pnpm dev   # http://localhost:4321
   ```

3. 내 정보로 바꿉니다.
   - `astro.config.mjs`의 `site`: 블로그 주소
   - `src/consts.ts`: 사이트 이름, 홈 첫 화면 문구, 프로필, 연락처, 자격증 카드(없으면 `null`)
   - `src/content/about.mdx`: 소개 본문
   - `public/favicon.*`: 파비콘
4. 샘플을 지웁니다. 첫 글을 쓴 뒤 아래 파일을 지우면 됩니다.
   - `src/content/blog/getting-started-1-first-post.md`
   - `src/content/series/getting-started.yaml`
   - `src/content/links/2026-11-19-github-com-astro.yaml`
5. GitHub 저장소의 **Settings → Pages → Build and deployment → Source**를 **GitHub Actions**로 바꿉니다. 그다음 `main`에 push하면 `.github/workflows/deploy.yml`이 빌드해서 배포합니다.

> 저장소 이름이 `<아이디>.github.io`가 아니면 주소가 `https://<아이디>.github.io/<저장소 이름>/`이 됩니다. 이때는 `astro.config.mjs`에 `base: '/<저장소 이름>'`을 추가해야 하고, 사이트 안 링크가 `/`로 시작하는 곳도 고쳐야 합니다. `<아이디>.github.io` 저장소나 개인 도메인을 쓰는 편이 간단합니다.

## 명령어

| 명령어 | 설명 |
|---|---|
| `pnpm install` | 의존성 설치 |
| `pnpm dev` | 개발 서버 실행 (`http://localhost:4321`) |
| `pnpm build` | `./dist/`에 프로덕션 빌드 |
| `pnpm preview` | 빌드 결과 미리보기 |
| `pnpm scrap <url> [태그 ...]` | 스크랩 초안 YAML과 썸네일 생성 |

## Claude Code 스킬

저장소 폴더에서 [Claude Code](https://claude.com/claude-code)를 실행하면 `.claude/skills/`의 스킬을 바로 쓸 수 있습니다.

- **tech-blog-writing**: 한국어 기술 블로그 글을 기획 → 개요 → 섹션 → 마무리의 4단계 체크포인트로 나눠 쓰고, 단계마다 검토를 받습니다. 실행 출력·수치를 지어내지 않도록 근거 체크리스트로 점검합니다. "블로그 글 써줘", "이 글 검토해줘"처럼 말하면 시작합니다.
  - 초안은 `drafts/<slug>/`, 글의 원자료(노트북, 로그)는 `raw/<주제>/`에 둡니다. 두 폴더는 `.gitignore`에 들어 있어 커밋되지 않습니다.
  - `references/korean-errata.md`의 사례 표는 비어 있습니다. 글을 검토하며 고친 표현이 쌓이면 다음 글의 점검이 좋아집니다.
- **link-scrap**: URL을 주면 원문을 읽고 한국어 요약을 써서 스크랩 카드를 만듭니다. 코멘트(왜 골랐는지)는 직접 말해야 하고 지어내지 않습니다. "이 링크 스크랩해줘"처럼 말하면 시작합니다.
