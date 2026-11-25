---
title: 'Numpy4 · 행렬곱의 한 칸과 결과 모양 읽기'
description: '왼쪽 행과 오른쪽 열로 행렬곱의 한 칸을 계산하고 두 배열에서 결과 모양을 읽는다.'
category: 'Python/NumPy'
pubDate: '2026-11-19'
series: numpy
seriesOrder: 4
heroImage: '../../assets/numpy/cover-matmul.webp'
env: ['Python 3.11.9', 'NumPy 2.4.6']
notebook: true
---

## 같은 배열에 `*`와 `@`를 쓰면 다른 결과가 나온다

3편에서는 두 배열의 모양을 오른쪽부터 맞춰 같은 자리에 대응하는 값을 더했다. 이번에는 3행 2열 배열과 2행 3열 배열을 곱한다. 아래 코드는 같은 두 배열에 원소별 곱 `*`와 행렬곱 `@`를 써서 결과를 비교하는 예시다. [NumPy 3편](/blog/python-numpy-broadcasting/)

```python
import numpy as np

m = np.array([[1, 2], [4, 5], [6, 7]])
n = np.array([[3, 2, 1], [6, 5, 4]])

try:
    print(m * n)
except ValueError as error:
    print(f"ValueError: {error}".rstrip())

print(m @ n)
```

```output
ValueError: operands could not be broadcast together with shapes (3,2) (2,3)
[[15 12  9]
 [42 33 24]
 [60 47 34]]
```

`m * n`은 모양을 오른쪽부터 비교할 때 `2`와 `3`이 맞지 않아 오류가 난다. `m @ n`은 왼쪽 배열의 행과 오른쪽 배열의 열을 짝지어 3행 3열을 만든다. 결과의 왼쪽 위 칸에 있는 `15`도 행 하나와 열 하나에서 나온 값이다.

## 결과의 한 칸은 왼쪽 한 행과 오른쪽 한 열로 계산한다

결과의 왼쪽 위 칸에는 왼쪽 배열의 첫 행 `[1, 2]`와 오른쪽 배열의 첫 열 `[3, 6]`이 대응한다. 첫 값끼리 곱하면 `1 × 3 = 3`, 둘째 값끼리 곱하면 `2 × 6 = 12`다. 대응하는 값끼리 곱한 뒤 하나로 더하는 계산을 **내적**이라고 한다. 두 곱을 더하면 `15`가 된다.

![왼쪽 첫 행의 1과 2, 오른쪽 첫 열의 3과 6을 짝지어 결과의 첫 칸 15를 계산하는 그림](../../assets/numpy/figure-matmul.webp)

아래 코드는 첫 행과 첫 열로 계산한 내적이 행렬곱의 왼쪽 위 칸과 일치하는지 확인하는 예시다. `sum()`은 곱한 값들을 더한다.

```python
import numpy as np

m = np.array([[1, 2], [4, 5], [6, 7]])
n = np.array([[3, 2, 1], [6, 5, 4]])

first_row = m[0, :]
first_column = n[:, 0]
products = first_row * first_column

print(products)
print(products.sum())
print((m @ n)[0, 0])
```

```output
[ 3 12]
15
15
```

결과의 다른 칸도 같은 방법으로 구한다. 예를 들어 결과의 첫 행 두 번째 칸에는 왼쪽의 첫 행과 오른쪽의 두 번째 열이 대응한다. 결과의 칸에서 행 번호와 열 번호를 읽으면, 어느 행과 열을 짝지을지 알 수 있다. [NumPy.matmul](https://numpy.org/doc/2.4/reference/generated/numpy.matmul.html)

## 안쪽 길이로 곱할 수 있는지, 바깥쪽 길이로 결과 모양을 읽는다

두 2차원 배열을 행렬곱할 때는 왼쪽에서 뽑은 한 행과 오른쪽에서 뽑은 한 열의 길이가 같아야 한다. 왼쪽 모양의 두 번째 값은 열 수이고, 오른쪽 모양의 첫 번째 값은 행 수다. 두 모양을 나란히 적으면 이 길이들이 안쪽에서 만난다.

| 왼쪽 모양 | 오른쪽 모양 | 안쪽 길이 비교 | 결과 |
|---|---|---|---|
| `(3, 2)` | `(2, 3)` | `2`와 `2`가 같다. | `(3, 3)` |
| `(3, 2)` | `(3, 2)` | `2`와 `3`이 다르다. | `ValueError` |

곱할 수 있다면 왼쪽의 각 행을 결과의 한 행으로, 오른쪽의 각 열을 결과의 한 열로 대응시킨다. 그래서 결과 모양은 바깥쪽 두 길이로 정해진다. 아래 코드는 모양의 안쪽 길이가 맞는 경우와 맞지 않는 경우를 비교하는 예시다.

```python
import numpy as np

m = np.array([[1, 2], [4, 5], [6, 7]])
n = np.array([[3, 2, 1], [6, 5, 4]])

print((m @ n).shape)

try:
    print(m @ m)
except ValueError as error:
    print(f"ValueError: {error}".rstrip())
```

```output
(3, 3)
ValueError: matmul: Input operand 1 has a mismatch in its core dimension 0, with gufunc signature (n?,k),(k,m?)->(n?,m?) (size 3 is different from 2)
```

`m @ m`에서 왼쪽 한 행은 값 두 개, 오른쪽 한 열은 값 세 개를 담고 있다. 오류 메시지 끝의 `size 3 is different from 2`도 이 두 길이가 다르다는 뜻이다. [NumPy.matmul](https://numpy.org/doc/2.4/reference/generated/numpy.matmul.html)

## 곱하는 순서를 바꾸고 다른 칸을 계산해 본다

`m @ n`에서는 왼쪽의 행이 세 개이고 오른쪽의 열이 세 개여서 결과가 `(3, 3)`이다. `n @ m`은 안쪽 길이가 둘 다 `3`이라 곱할 수 있고, 바깥쪽 길이가 둘 다 `2`라 결과는 `(2, 2)`다. 아래 코드는 순서를 바꾼 결과와 원래 순서의 한 칸을 확인하는 예시다.

```python
import numpy as np

m = np.array([[1, 2], [4, 5], [6, 7]])
n = np.array([[3, 2, 1], [6, 5, 4]])
reversed_product = n @ m

print(reversed_product)
print(reversed_product.shape)
print((m @ n)[1, 2])
```

```output
[[17 23]
 [50 65]]
(2, 2)
24
```

**직접 확인해 보기.** 원래 순서인 `m @ n`에서 두 번째 행 세 번째 열의 값을 손으로 계산한다. 왼쪽 두 번째 행 `[4, 5]`와 오른쪽 세 번째 열 `[1, 4]`를 짝지으면 `4 × 1 + 5 × 4 = 24`가 된다. 코드에서 `(m @ n)[1, 2]`를 출력한 값과 같다.

이번 편에서는 행과 열을 짝지어 새로운 배열을 만들었다. 

[NumPy 5편](/blog/python-numpy-sorting/)에서는 배열의 축을 정해 값을 집계하거나 정렬한다.