---
title: '블로그의 AI 사용 기록'
description: '공부한 내용을 글로 옮기며 발견한 오해의 소지와 AI를 활용해 검증하고 고친 과정을 기록한다.'
category: '블로그'
pubDate: '2026-11-15'
heroImage: '../../assets/cover-ai-usage-log.webp'
---

공부한 내용을 글로 옮기다 보면 예시에서는 맞더라도 다른 경우에 적용하면 틀리는 설명이 있다. 설명에 오해의 소지가 있거나 코드와 출력이 예상과 다를 때, AI에 무엇을 확인해 달라고 했는지와 공식 문서나 실행 결과를 바탕으로 무엇을 고쳤는지 기록한다.

## 2026년 11월 16일 · NumPy 1편

### 스칼라와 난수 함수 설명

**문제:** 스칼라 값과 그 값을 담은 0차원 배열의 구분이 불분명했다. `rand`와 `randn`은 2차원 예시에서 인자를 두 개 받지만, 설명은 두 함수가 항상 인자 두 개를 받는 것처럼 읽힐 수 있었다.

**해결:** AI에 두 설명을 NumPy 공식 문서와 실행 결과로 검토해 달라고 요청했다. `np.array(5)`의 자료형이 `ndarray`이고 축이 0개임을 확인해, 숫자 `5`를 담은 0차원 배열로 설명했다. `rand`와 `randn`에는 길이를 한 개, 두 개, 세 개씩 넘겨 각각 1차원, 2차원, 3차원 배열이 만들어지는 것을 확인했다. 본문에는 두 함수가 축별 길이를 각각의 인자로 받는다고 적었다. [NumPy.array](https://numpy.org/doc/2.4/reference/generated/numpy.array.html) · [NumPy 기존 난수 생성 함수](https://numpy.org/doc/2.4/reference/random/legacy.html#simple-random-data)

## 2026년 11월 17일 · NumPy 2편

### 정수 배열 인덱싱으로 읽기와 쓰기

**문제:** 번호 목록으로 값을 꺼내면 복사본이 생긴다는 설명만 두면, 같은 인덱싱으로 원본에 값을 대입할 때도 복사본만 바뀐다고 이해할 수 있었다.

**해결:** AI가 NumPy 공식 문서와 실행 결과를 대조했다. 번호 목록으로 읽은 복사본을 수정했을 때 원본의 두 번째 행은 `[5 6 7 8]`로 남았다. `table[1, [0, 1]]`을 대입문 왼쪽에 직접 썼을 때는 같은 행의 앞 두 값이 `99`와 `100`으로 바뀌었다. 두 결과를 비교하는 예제를 본문과 노트북에 넣었다. [NumPy 복사본과 뷰](https://numpy.org/doc/2.4/user/basics.copies.html#indexing-operations)

## 2026년 11월 25일 · NumPy 5편

### 배열의 `sort()`와 `argsort()`가 반환하는 값

**문제:** 강의 자료에서 배열의 `sort()`가 정렬할 인덱스를 반환한다고 설명했다. 이 설명을 따르면 `sort()`의 반환값을 학생의 이름과 점수표를 함께 옮기는 데 쓸 수 있다고 이해하기 쉽다.

**해결:** AI에 `sort()`와 `argsort()`의 반환값과 원본이 바뀌는지 확인해 달라고 요청했다. 공식 문서와 실행 결과를 대조해 배열의 `sort()`는 원본을 정렬하고 `None`을 반환하며, `np.argsort()`는 정렬에 쓸 위치를 반환함을 확인했다. 본문에서는 원본을 바꾸는 호출을 비교표에 두고, 학생의 이름과 점수에 같은 위치를 적용하는 예제에는 `np.argsort()`를 썼다. [NumPy.ndarray.sort](https://numpy.org/doc/2.4/reference/generated/numpy.ndarray.sort.html) · [NumPy.argsort](https://numpy.org/doc/2.4/reference/generated/numpy.argsort.html)
