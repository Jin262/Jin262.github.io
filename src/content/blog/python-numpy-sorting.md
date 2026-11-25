---
title: 'Numpy5 · 축을 따라 집계하고 정렬하기'
description: '점수표에서 축에 따라 합계와 정렬 결과가 어떻게 달라지는지 읽고 이름과 점수를 함께 옮긴다.'
category: 'Python/NumPy'
pubDate: '2026-11-20'
series: numpy
seriesOrder: 5
heroImage: '../../assets/numpy/cover-sorting.webp'
env: ['Python 3.11.9', 'NumPy 2.4.6']
notebook: true
---

2편의 점수표에서 첫 과목 점수만 고르면 `[72, 55, 91]`이다. 세 점수를 더하면 `218`이 되고, 낮은 점수부터 정렬하면 `[55, 72, 91]`이 된다. 점수표 전체에 같은 계산을 적용할 때는 `axis`로 어느 방향의 값을 함께 처리할지 정한다. [NumPy 2편](/blog/python-numpy-indexing/)

## 합계는 지정한 축의 값을 하나로 모은다

점수표에는 학생 세 명이 한 행씩, 과목 네 개가 한 열씩 들어 있다. 모양은 `(3, 4)`다. 여러 값을 합계나 평균처럼 하나의 값으로 모으는 연산을 **집계**라고 한다.

- `axis=0`에서는 열을 고정하고 행 번호를 바꾼다. 첫 과목 점수인 `72 + 55 + 91 = 218`처럼 세 학생의 점수를 더한다. 과목마다 합계 하나가 남아 결과 모양은 `(4,)`다.
- `axis=1`에서는 행을 고정하고 열 번호를 바꾼다. 첫 학생의 점수인 `72 + 88 + 64 + 90 = 314`처럼 네 과목 점수를 더한다. 학생마다 합계 하나가 남아 결과 모양은 `(3,)`다.

![학생 세 명과 과목 네 개의 점수표에서 과목별 합계 218, 229, 227, 256은 각 열 아래에, 학생별 합계 314, 273, 343은 각 행 오른쪽에 표시한 그림](../../assets/numpy/figure-axis-reduction.webp)

아래 코드는 같은 점수표에서 과목별 합계와 학생별 합계를 구하고 결과 모양을 확인하는 예시다.

```python
import numpy as np

scores = np.array([[72, 88, 64, 90], [55, 67, 81, 70], [91, 74, 82, 96]])
by_subject = scores.sum(axis=0)
by_student = scores.sum(axis=1)

print(by_subject, by_subject.shape)
print(by_student, by_student.shape)
```

```output
[218 229 227 256] (4,)
[314 273 343] (3,)
```

`sum()`의 기본 설정에서는 더한 축이 결과 모양에서 빠진다. `(3, 4)`에서 0번 축을 따라 더하면 과목 수인 `4`만 남고, 1번 축을 따라 더하면 학생 수인 `3`만 남는다. [NumPy.sum](https://numpy.org/doc/2.4/reference/generated/numpy.sum.html)

## 학생별 평균을 뺄 때는 집계한 축을 길이 1로 남긴다

각 과목 점수가 학생 자신의 평균보다 얼마나 높거나 낮은지 알려면, 같은 행의 네 점수에서 그 학생의 평균을 뺀다. `scores.mean(axis=1)`로 구한 평균의 모양은 `(3,)`이다. 이 배열을 `(3, 4)`인 점수표에서 바로 빼면, 브로드캐스팅이 오른쪽부터 길이를 맞출 때 `4`와 `3`이 달라 오류가 난다. [NumPy 3편](/blog/python-numpy-broadcasting/)

아래 코드는 `keepdims=True`로 1번 축을 길이 `1`로 남겨 학생별 평균을 각 행의 점수에서 빼는 예시다.

```python
import numpy as np

scores = np.array([[72, 88, 64, 90], [55, 67, 81, 70], [91, 74, 82, 96]])
student_means = scores.mean(axis=1, keepdims=True)
differences = scores - student_means

print(scores.mean(axis=1).shape)
print(student_means)
print(student_means.shape)
print(differences[0])
```

```output
(3,)
[[78.5 ]
 [68.25]
 [85.75]]
(3, 1)
[ -6.5   9.5 -14.5  11.5]
```

`(3, 4)`와 `(3, 1)`을 오른쪽부터 비교하면 열 축의 길이는 `4`와 `1`이고 행 축의 길이는 둘 다 `3`이다. 길이 `1`인 열 축 덕분에 각 행의 평균 하나가 그 행의 네 점수에 적용된다. 첫 학생의 평균은 `314 ÷ 4 = 78.5`다. 출력의 `-6.5`와 `9.5`는 첫 과목 점수가 자기 평균보다 6.5점 낮고 둘째 과목 점수가 9.5점 높다는 뜻이다. [NumPy.mean](https://numpy.org/doc/2.4/reference/generated/numpy.mean.html)

## 정렬하면 같은 축 안에서 값의 순서가 바뀐다

같은 점수표에서 `axis=0`으로 합계를 구하면 열마다 한 값이 남는다. 같은 축으로 정렬하면 열마다 점수 세 개를 낮은 순서로 다시 놓는다. `axis=1`로 정렬하면 학생마다 한 행 안에서 과목 점수 네 개의 순서가 바뀐다. 아래 코드는 두 방향으로 정렬한 결과와 모양을 비교하는 예시다.

```python
import numpy as np

scores = np.array([[72, 88, 64, 90], [55, 67, 81, 70], [91, 74, 82, 96]])
by_subject = np.sort(scores, axis=0)
by_student = np.sort(scores, axis=1)

print(by_subject)
print(by_student)
print(by_subject.shape, by_student.shape)
```

```output
[[55 67 64 70]
 [72 74 81 90]
 [91 88 82 96]]
[[64 72 88 90]
 [55 67 70 81]
 [74 82 91 96]]
(3, 4) (3, 4)
```

첫 번째 출력의 첫 행을 보면 첫 과목의 `55`점은 둘째 학생의 점수이고, 세 번째 과목의 `64`점은 첫째 학생의 점수다. 과목마다 따로 정렬했기 때문에 한 행에 여러 학생의 점수가 섞였다. 두 번째 출력의 첫 행에는 첫째 학생의 점수 네 개가 낮은 순서로 들어 있다. 두 경우 모두 행 세 개와 열 네 개가 남는다. [NumPy.sort](https://numpy.org/doc/2.4/reference/generated/numpy.sort.html)

| 호출 | 반환값 | 원래 배열 |
|---|---|---|
| `np.sort(scores, axis=0)` | 정렬한 복사본 | 입력 순서를 유지한다. |
| `scores.sort(axis=0)` | `None` | 각 열이 정렬된 순서로 바뀐다. |

기존 점수표와 정렬 결과를 함께 쓰려면 `np.sort()`로 복사본을 받는다. 표 자체를 바꿀 때는 배열의 `sort()`를 호출한 뒤 같은 배열을 읽는다. [NumPy.ndarray.sort](https://numpy.org/doc/2.4/reference/generated/numpy.ndarray.sort.html)

## 정렬할 위치를 구해 학생 기록을 함께 옮긴다

첫 과목 점수순으로 학생 목록을 만들려면 이름과 네 과목 점수를 함께 옮긴다. `np.argsort()`는 값을 작은 순서로 읽으려면 원래 배열에서 어느 위치를 먼저 꺼내야 하는지 알려 준다. 그 위치 목록을 이름과 점수표에 함께 적용한다.

아래 코드는 세 번째 학생의 첫 과목 점수를 `91`에서 `72`로 바꿔 동점을 만든 다음, 첫 과목을 기준으로 학생의 행 전체를 옮기는 예시다.

```python
import numpy as np

scores = np.array([[72, 88, 64, 90], [55, 67, 81, 70], [72, 74, 82, 96]])
names = np.array(["A", "B", "C"])
order = np.argsort(scores[:, 0], stable=True)

print(order)
print(names[order].tolist())
print(scores[order])
```

```output
[1 0 2]
['B', 'A', 'C']
[[55 67 81 70]
 [72 88 64 90]
 [72 74 82 96]]
```

위치 목록 `[1, 0, 2]`는 `55`점인 `B`의 행을 먼저 꺼내고, 동점인 `A`와 `C`의 행을 차례로 꺼내라는 뜻이다. `stable=True`를 지정하면 기준 점수가 같은 학생끼리는 원래 순서를 유지한다. 2편에서 번호 목록으로 행을 고른 것처럼 `scores[order]`는 행 전체를 꺼낸다. `names[order]`에도 같은 번호를 적용하므로 첫 줄의 `B`는 자신의 네 과목 점수 `[55, 67, 81, 70]`와 함께 이동한다. [NumPy.argsort](https://numpy.org/doc/2.4/reference/generated/numpy.argsort.html)

**직접 확인해 보기.** `np.argsort(scores[:, 0], stable=True)`에서 열 번호를 `1`로 바꾼다. 두 번째 과목 점수 `[88, 67, 74]`를 낮은 순서로 읽는 위치는 `[1, 2, 0]`이다. 출력에서 이름이 `B, C, A` 순으로 바뀌고 둘째 열의 점수가 위에서부터 `67, 74, 88`인지 확인한다.

합계가 필요하면 어느 축의 값을 모을지 정하고, 학생별 기록을 옮길 때는 어느 열을 기준으로 행의 순서를 정할지 살핀다.
