## Development

When starting the dev server, use background mode:

```
astro dev --background
```

Manage the background server with `astro dev stop`, `astro dev status`, and `astro dev logs`.

## Documentation

Full documentation: https://docs.astro.build

Consult these guides before working on related tasks:

- [Adding pages, dynamic routes, or middleware](https://docs.astro.build/en/guides/routing/)
- [Working with Astro components](https://docs.astro.build/en/basics/astro-components/)
- [Using React, Vue, Svelte, or other framework components](https://docs.astro.build/en/guides/framework-components/)
- [Adding or managing content](https://docs.astro.build/en/guides/content-collections/)
- [Adding styles or using Tailwind](https://docs.astro.build/en/guides/styling/)
- [Supporting multiple languages](https://docs.astro.build/en/guides/internationalization/)

## Writing rules

블로그 글의 공용 규칙은 `docs/writing/`에 있다. Claude 스킬(`.claude/skills/tech-blog-writing`)과 Codex 스킬(`.agents/skills/review-technical-draft`)이 같은 파일을 읽는다.

- `docs/writing/korean-errata.md`: 한국어 정오표(K 유형, 사례, 고치지 않은 것)
- `docs/writing/anti-patterns.md`: 글 구조 안티패턴(AP 유형)

두 파일은 원고를 실제로 고친 뒤 Claude 스킬 `.claude/skills/writing-rules-update`로 갱신한다. 리뷰는 수정하지 않고 보고서에 후보로 남긴다.

이 블로그의 입문 글은 처음 보는 용어를 짧게 풀고 작은 입력과 실제 출력으로 설명한다. 중요한 개념은 비슷해 보이는 예제의 차이, 흔히 하는 실수와 확인할 값, 독자가 직접 바꿔 볼 조건까지 다룬다. 분량은 독자가 스스로 결과를 설명하고 예측하는 데 필요한 만큼 잡는다.

목록의 `description`은 핵심을 앞에 둔 한 문장으로 쓴다. 쉬운 글은 짧게 쓰고, 어려운 글은 독자가 이해할 원리나 해결할 문제를 조금 더 구체적으로 밝힌다. 블로그 목록과 카테고리 페이지에서는 최대 두 줄로 표시한다.

본문은 대체로 ‘~다’로 끝나는 평서체로 쓴다. 큰 제목 아래에서 코드나 사례의 목적을 소개할 때는 ‘…하는 예시다’로 마무리한다. 코드 바로 위의 작은 제목에 목적이 드러나면 코드 이해에 필요한 설명만 남기거나 제목에서 바로 코드로 이어간다.

새 글의 구성과 문체는 [NumPy 1편 원고](src/content/blog/python-numpy.md)를 기준으로 삼는다. 도입은 독자가 바로 비교할 수 있는 작은 상황으로 시작한다. 큰 제목은 각 절의 핵심을 설명하고, 예시가 여러 개 있는 절에는 필요한 곳에 작은 제목을 붙인다. 실행 가능한 코드 다음에 실제 출력을 붙이고, 이어지는 설명에서 독자가 어떤 값을 어떻게 읽는지 풀어 쓴다. 표와 그림은 비교에 도움이 될 때 사용하고 예시 값과 일치시킨다. 독자가 바꿔 볼 조건과 확인할 결과가 있으면 ‘직접 확인해 보기’에 담는다. 공식 문서 링크는 관련 설명 끝에 두고, 함수 문서는 ‘NumPy.eye’처럼 라이브러리와 함수 이름을 붙여 표시한다.

### AI 사용 기록

글을 새로 쓰거나 고칠 때 [블로그의 AI 사용 기록](src/content/blog/ai-usage-log.md)에 남길 사례를 검토한다. 공부한 내용을 글로 옮기면서 발견한 개념의 오해, 예시에만 맞는 설명, 코드·출력·그림의 기술적 불일치처럼 검증을 통해 이해나 설명이 달라진 사례를 기록한다. 제목 중복 해소나 단순 이미지 교체처럼 글의 기술적 내용을 바꾸지 않는 편집은 생략한다.

실제 작업일과 글 제목을 `## YYYY년 MM월 DD일 · 글 제목`으로 적고, 사례마다 짧은 3단계 제목을 붙인다. 제목 아래에는 `**문제:**`와 `**해결:**`을 한 문단씩 두고 한 줄을 비운다. 문제에는 무엇이 헷갈리거나 틀리게 읽혔는지 적는다. 해결에는 AI에 요청한 작업, 대조한 공식 문서나 실제 출력, 확인을 거쳐 글에 반영한 내용을 `~다` 문체로 간결하게 적는다. 원고를 검수할 때 기록도 함께 확인한다.

### 내려받기 노트북

내려받기를 제공하는 글은 [NumPy 1편 실습 노트북](public/notebooks/python-numpy.ipynb) 형식으로 함께 작성한다. `public/notebooks/<글 id>.ipynb`에 UTF-8로 저장하고 글의 프런트매터에 `notebook: true`를 지정한다. 첫 셀에 제목과 원문 링크를 넣고, 글의 예제 순서대로 짧은 절 제목과 실행 가능한 코드 셀을 배치한다. 실습에 필요한 안내만 덧붙이고 실제 실행 결과는 각 코드 셀에 저장한다.

게시 전 깨끗한 실행 환경에서 셀을 처음부터 실행해 저장된 출력과 본문의 코드·결과를 대조한다. 글에서 **노트북 내려받기**를 눌러 받은 파일이 노트북으로 열리고 한글 제목·설명과 코드·출력이 올바르게 표시되는지 확인한다. 노트북도 원고와 함께 검수받고 검수가 끝난 뒤 게시한다.
