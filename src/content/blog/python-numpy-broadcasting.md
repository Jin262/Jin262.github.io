---
title: 'Numpy3 · 행과 열에 값을 더하는 브로드캐스팅'
description: '배열의 모양을 오른쪽부터 비교하고 행과 열에 각각 값을 더한다.'
category: 'Python/NumPy'
pubDate: '2026-11-18'
series: numpy
seriesOrder: 3
heroImage: '../../assets/numpy/cover-broadcasting.webp'
env: ['Python 3.11.9', 'NumPy 2.4.6']
notebook: true
---

## 같은 표에 값 세 개는 더할 수 있고 두 개는 오류가 난다

2편에서 사용한 2행 3열 배열에 열마다 더할 값 `10`, `20`, `30`을 준비한다. 행마다 더하려면 `100`, `200`처럼 값 두 개가 필요하다. 아래 코드는 두 배열을 같은 표에 각각 더해 결과를 비교하는 예시다. [NumPy 2편](/blog/python-numpy-indexing/)

```python
import numpy as np

grid = np.array([[1, 2, 3], [4, 5, 6]])
columns = np.array([10, 20, 30])
rows = np.array([100, 200])

print(grid + columns)

try:
    print(grid + rows)
except ValueError as error:
    print(f"ValueError: {error}".rstrip())
```

```output
[[11 22 33]
 [14 25 36]]
ValueError: operands could not be broadcast together with shapes (2,3) (2,)
```

첫 번째 덧셈에서 첫 행의 `11`은 `1 + 10`, `22`는 `2 + 20`의 결과다. 둘째 행에도 열별로 같은 값을 더한다. 두 번째 덧셈은 `ValueError`가 난다. 예제의 `try`와 `except`는 그 오류를 잡아 메시지를 출력한다. 메시지 끝의 `(2,3)`과 `(2,)`은 각각 `grid`와 `rows`의 모양이다.

## 브로드캐스팅은 모양을 오른쪽 끝부터 비교한다

모양이 다른 배열에도 축의 길이가 맞으면 원소별 계산을 적용할 수 있다. 이 규칙을 **브로드캐스팅**이라고 한다. NumPy는 두 배열의 `shape`를 오른쪽 끝에 맞춘 뒤 오른쪽 축부터 왼쪽으로 비교한다. 대응하는 길이가 같거나 둘 중 하나가 `1`이면 그 축은 맞는다. 축 개수가 다르면 축이 적은 배열의 왼쪽에서 모자란 길이를 `1`로 본다. [NumPy 브로드캐스팅 규칙](https://numpy.org/doc/2.4/user/basics.broadcasting.html#general-broadcasting-rules)

| 덧셈에 넘긴 모양 | 오른쪽 축 비교 | 왼쪽 축 비교 | 결과 |
|---|---|---|---|
| `(2, 3)`과 `(3,)` | `3`과 `3`이 같다. | `2`와 `1` 중 하나가 `1`이다. | `(2, 3)` |
| `(2, 3)`과 `(2,)` | `3`과 `2`가 맞지 않는다. | 오른쪽에서 이미 멈춘다. | `ValueError` |

`columns.shape`는 `(3,)`이므로 비교할 때 `(1, 3)`처럼 읽는다. 오른쪽의 `3`이 표의 세 열에 해당하고, 왼쪽의 `1` 덕분에 같은 세 값을 표의 두 행에 각각 더할 수 있다.

`rows.shape`는 `(2,)`다. 이 `2`도 먼저 표의 오른쪽 축인 열에 대응한다. 표의 열은 세 개라서 여기서 비교가 멈춘다. 행마다 다른 값을 더하려면 표의 행 축에 길이 `2`가 오도록 모양을 바꿔야 한다.

## 행에 하나씩 더할 값은 열이 하나인 배열로 만든다

표에는 행이 두 개 있으므로 행별 값 두 개를 2행 1열로 배치한다. `reshape(2, 1)`은 `100`, `200`의 순서를 유지하면서 행마다 값 하나가 들어 있는 배열을 만든다. 아래 코드는 이 배열의 모양과 덧셈 결과를 확인하는 예시다. [NumPy.reshape](https://numpy.org/doc/2.4/reference/generated/numpy.reshape.html)

```python
import numpy as np

grid = np.array([[1, 2, 3], [4, 5, 6]])
rows = np.array([100, 200])
row_values = rows.reshape(2, 1)

print(row_values)
print(row_values.shape)
print(grid + row_values)
```

```output
[[100]
 [200]]
(2, 1)
[[101 102 103]
 [204 205 206]]
```

`(2, 3)`과 `(2, 1)`을 오른쪽부터 비교하면 열의 길이는 `3`과 `1)이고 행의 길이는 둘 다 `2`다. 그래서 첫 행의 `100`은 `1`, `2`, `3`에 각각 더해지고, 둘째 행의 `200`은 `4`, `5`, `6`에 각각 더해진다.

## 행별 값과 열별 값을 더해 새 표를 만든다

행마다 하나씩 쓸 값 두 개와 열마다 하나씩 쓸 값 세 개를 서로 더하면, 두 행과 세 열의 모든 조합을 얻을 수 있다. 아래 코드는 `(2, 1)`인 배열과 `(3,)`인 배열을 더해 2행 3열짜리 표를 만드는 예시다.

```python
import numpy as np

rows = np.array([100, 200]).reshape(2, 1)
columns = np.array([10, 20, 30])
result = rows + columns

print(result)
print(result.shape)
```

```output
[[110 120 130]
 [210 220 230]]
(2, 3)
```

첫 행은 `100`에 `10`, `20`, `30`을 차례로 더한 결과다. 둘째 행에는 `200`에 같은 세 값을 더한 결과가 들어간다.

**직접 확인해 보기.** `rows`의 두 번째 값 `200`을 `300`으로 바꾼다. 첫 행은 그대로이고 둘째 행은 `[310, 320, 330]`이 된다. 행별 값 하나가 결과의 한 행 전체에 영향을 주며, 표의 모양은 `(2, 3)`으로 유지된다.

이번 편에서는 두 배열의 값을 짝지어 칸마다 더했다. 

[NumPy 4편](/blog/python-numpy-matrix-multiplication/)에서는 왼쪽 배열의 한 행과 오른쪽 배열의 한 열을 이용해 행렬곱의 한 칸을 계산한다.