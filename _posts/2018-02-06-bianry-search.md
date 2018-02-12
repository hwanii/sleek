---
layout: post
title: 이진 탐색(Binary Search)
summary:
category: etc.
featured-img: emile-perron-190221
---

## 이진 탐색(Binary Search)

> 알고리즘에 대한 내용은 모두 'Hello Coding 그림으로 개념을 이해하는 알고리즘'을 참고하였습니다.


정렬된 원소 리스트에서 원하는 원소를 찾을 때 사용하는 탐색 알고리즘 중 하나로, 처음부터 끝까지 순차적으로 탐색하는 단순 탐색과 다르게 리스트의 중간 값을 찾는 값과 비교해서 그 범위를 좁혀나가며 탐색하는 것을 의미합니다.
> 1, 2, 3, ... 99, 100

다음과 같은 단순 탐색은 최대 리스트의 길이만큼 탐색을 해야 합니다.
> 100 -> 50 -> 25 -> 13 -> 7 -> 4 -> 2 -> 1

반면에, 이진 탐색은 매 단계마다 절반씩 탐색할 원소들이 줄어들기 때문에 단순 탐색보다 훨씬 빠른 속도로 탐색할 수 있습니다. 즉 이진 탐색은 최대 \log_{2} n 번 만에 답을 찾을 수 있습니다.<br><br>

파이썬으로 이진 탐색을 구현해보겠습니다. 우선 처음에는 전체를 탐색해야 합니다.
```python
low = 0
high = len(list) - 1
```
그리고 이진 탐색의 원리대로 가운데 있는 원소를 확인합니다.
```python
mid = (low+high) // 2
guess = list[mid]
```
추측한 값이 작다면 low를, 크다면 high 값을 변경해줍니다. 같다면 추측 값을 return 합니다.
```python
# item은 찾는 원소의 값
if guess == item:
  return mid
if guess < item:
  low = mid + 1
else:
  high = mid-1
```
전체 소스코드를 작성해보면,
```python
def binary_search(list, item):
  low = 0
  high = len(list) - 1

  while low <= high:
    mid = (low+high) // 2
    guess = list[mid]

    if guess == item:
      return mid
    if guess < item:
      low = mid +1
    else:
      high = mid-1
  return None  #  찾는 값이 리스트에 없을 때 None 리턴
```
