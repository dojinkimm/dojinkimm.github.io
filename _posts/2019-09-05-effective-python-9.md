---
layout: post
title: "[파이썬 코딩의 기술] Built-in 모듈 - datetime, algorithm, decimal"
date: 2019-09-05 17:00:00
author: Dojin Kim
categories: Python
tags: python effective_python 파이썬코딩의기술
cover:  "/assets/imgs/python_cover.jpg"
---

> Effective Python - Brett Slatkin을 읽으면서 공부 및 정리를 하며 글을 쓰고 있습니다.

# Item 45: 지역시간은 time 대신에 datetime으로 표현하기

Python은 time을 2가지 방법으로 나타낸다. 오래된 방법은 `time` built-in 모듈을 사용하는 것인데, 에러가 매우 많다... `datetime`은 새로운 방법으로 커뮤니티에서 만든 `pytz`패키지와 같이 사용하면 매우 유용하다.

`datetime`예시를 살펴보기 전에 왜 `time`모듈은 사용하지 않는 것이 좋은지 살펴보자.

### `time` Module

`localtime`함수는 UNIX timestamp를 컴퓨터 타임 존의 local 시간으로 변환을 해준다.

```python
from time import localtime, strftime

now = 1407694710
local_tuple = localtime(now)
time_format = ‘%Y-%m-%d %H:%M:%S’
time_str = strftime(time_format, local_tuple)
print(time_str)

# 2014-08-10 11:18:30
```

하지만, 다른 타임 존의 local시간으로 변경하려면 어떻게 해야 할까? 예를 들어, 샌프란시스코에서 뉴욕의 시간을 알고 싶을 때, `time, localtime, strptime`함수들로 시간을 알아내기 힘들 것이다.

현재 컴퓨터가 Pacific Daylight Time 이라는 타임 존에 있다고 생각해보자. 이때 시간에 접근하는 것은 올바른 결과를 리턴해줄 것이다. 하지만 현 컴퓨터에서 다른 타임존 예를 들어 Eastern Daylight Time에 접근하려고 하면 에러가 생기게 된다.

```python
parse_format = '%Y-%m-%d %H:%M:%S %Z'
depart_sfo = '2014-05-01 15:45:16 PDT'
time_tuple = strptime(depart_sfo, parse_format)
time_str = strftime(time_format, time_tuple)
print(time_str)

# 2014-05-01 15:45:16

arrival_nyc = '2014-05-01 23:33:24 EDT'
time_tuple = strptime(arrival_nyc, time_format)

# ValueError: unconverted data remains: EDT
```

이렇게 에러가 발생하는 이유는 `time` 모듈이 플랫폼에 종속적이기 때문이다. 이러한 성질 때문에 `time` 모듈 보다 `datetime` 모듈을 사용한는 것이 더 낫다.

### `datetime` Module

Python 커뮤니티에서 모든 타임 존에 대한 database를 `pytz`라는 모듈에 진니고 있다. 이 `pytz` 모듈을 사용하면 다른 타임존의 시간도 쉽게 알아낼 수 있다.

```python
from datetime import datetime, timezone

arrival_nyc = '2014-05-01 23:33:24'
nyc_dt_naive = datetime.strptime(arrival_nyc, time_format)
eastern = pytz.timezone('US/Eastern')
nyc_dt = eastern.localize(nyc_dt_naive)
utc_dt = pytz.utc.normalize(nyc_dt.astimezone(pytz.utc))
print(utc_dt)

# 2014-05-02 03:33:24+00:00

pacific = pytz.timezone('US/Pacific')
sf_dt = pacific.normalize(utc_dt.astimezone(pacific))
print(sf_dt)

# 2014-05-01 20:33:24-07:00
```



# Item 46: Built-in 알고리즘과 데이터 구조 사용하기

Python에서 작은 데이터에 대해 직접 구현한 알고리즘을 적용하면 속도가 저하되는 일이 때때로 있다. Python 자체가 느리기 보다는 보통 유저가 문제에 최적화된 알고리즘을 적용하지 않았기 때문에 발생하는 것이다.

이러한 문제점을 해결하기 위해 Python은 알고리즘과 데이터구조를 사용할 수 있는 표준 라이브러리들을 제공한다.  



### Double-ended Queue

`collections` 모듈의 `deque` class는 doube-ended queue이다. 그렇기 때문에 item을 앞이나 뒤에서 삽입 혹은 제거하는 시간은 상수 시간만큼 걸린다 O(1). 


```python
que = deque()
que.append(1)
x = que.popleft()
```


사실 `list`도 queue 형식의 데이터 구조이다. 하지만 `deque`와는 달리 뒤에 삽입 혹은 제거할 때만 상수 시간만큼 걸린다. 맨 앞에 삽입 혹은 제거할 때는 linear 시간만큼 걸린다.



### Ordered Dictionary

표준 dictionary는 정렬되어있지 않다. 두개의 dictionary가 있고 같은 key와 value를 지녔더라도 iteration할 때 다른 순서대로 진행될 수 있다. 하지만 이 방법 덕분에 빠른 hash table을 구현할 수 있었다.

`OrderedDict`는 `collections` 모듈의 class이고 key가 삽입되는 대로 순서를 저장하는 특별한 dictionary 타입이다. `OrderedDict`에서 key를 예측 가능하게 iterate(순회)한다.

```python
a = OrderedDict()
a[‘foo’] = 1
a[‘bar’] = 2

b = OrderedDict()
b[‘foo’] = 'red'
b[‘bar’] = 'blue'

for value1, value2 in zip(a.values(), b.values()):
    print(value1, value2)

# 1 red
# 2 blue
```


### Default Dictionary

Dictionary는 bookkeeping이나 지표들을 추적할 때 유용하다. 하나 문제점은 key가 dictionary에 이미 있는지 확인할 수 없다는 것이다. 그래서 밑의 코드와 같이 불필요한 작업을 하게 되는 것이다.

```python
stats = {}
key = 'my_counter'
if key not in stats:
    stats[key] = 0
stats[key] += 1

```


`collections` 모듈의 `defaultdict` class는 자동적으로 없는 key에 default 값을 저장해서 위 문제를 해결한다. parameter로 default 값이 무엇일지 정해줄 수 있다. `int`는 built-in 함수로 0을 리턴한다.

```python
stats = defaultdict(int)
stats['counter'] += 1
```



### Heap Queue

Heap은 우선순위 queue를 유지하는데 유용한 데이터 구조이다. `heapq`라는 모듈은 표준 `list`타입에 `heappush`, `heappop`, `nsmallest`와 같은 기능들을 사용할 수 있게 해준다.

Item 들은 아무 순서로 heap에 추가가 가능하다. 제거할 때는 항상 높은 우선순위(낮은 숫자)부터 제거된다. 

```python
a = []
heappush(a, 5)
heappush(a, 3)
heappush(a, 7)
heappush(a, 4)

print(heappop(a), heappop(a), heappop(a), heappop(a))

# 3 4 5 7
```

0 index는 항상 가장 작은 item을 리턴한다. `heapq`의 자겁들은 항상 list길이에 비례해 log 시간만큼 걸린다.

```python
a = []
heappush(a, 5)
heappush(a, 3)
heappush(a, 7)
heappush(a, 4)
assert a[0] == nsmallest(1, a)[0] == 3
```



### Bisection

`index` method를 사용해서 `list`의 item을 찾을 때 linear 시간만큼 걸린다. `bisect` 모듈의 함수인 `bisect_left`는 정렬된 item에서 더 효율적인 binary search(이진 탐색)을 제공한다. 해당 값이 삽입된 index를 리턴한다.

binary search의 시간 복잡도는 O(logn)이다. 반면 `list`의 시간복잡도는 O(n)이기 때문에, index를 찾을 때는 `bisect`를 사용하면 더 빠르게 찾을 수 있다.

```python

x = list(range(10**6))
i = x.index(991234)

i = bisect_left(x, 991234)
```



### Iterator Tools

`itertools`는 iterator를 다루는 모듈이다. `itertools`는 크게 3개의 메인 카테고리로 나뉘어져있다:

1. iterator들을 link
    - `chain` - 여러 iterators를 하나의 순차적인 iterator로 합침
    - `cycle` - iterator의 item들을 무한대로 반복한다
    - `tee` - 하나의 iterator를 여러개의 parallel한 iterator들로 나눈다
    - `zip_longest` - `zip` 함수인데 다른 길이의 iterator와도 잘 작동한다
2. iterator에서 item 필터링
    - `islice` - 복사하지 않고 여러 index들로 iterator를 slice한다
    - `takewhile` - 함수가 True일 때 item을 계속 리턴한다
    - `dropwhile` - 함수가 처음으로 False일 때 item을 리턴한다
    - `filterfalse` - 함수가 False일 때 모든 item들을 리턴한다. `filter` 함수의 반대 역할을 한다
3. item의 combination
    - `product` - item들의 Cartesian product를 리턴한다.
    - `permutations` - 순열을 리턴한다
    - `combination` - 조합을 리턴한다

이외에도 많은 함수들이 `itertools`에 있다.



# Item 47: 정밀도가 중요할 때는 decimal 사용하기

Python에서 소수점을 다룰 때 조심해야 한다. 밑에 처럼 반올림될때 소숫점 2번째 자리까지 나타내려고 할 때 그냥 0으로 만들어버리는 경우가 있다. 이런 결과는 유저가 원하지 않은 결과일 가능성이 높다.  

```python
rate = 0.05
seconds = 5
cost = rate * seconds / 60
print(cost)
# 0.004166666666666667

print(round(cost, 2))
# 0.0
```


이러한 문제점을 해결하기 위해서는 `decimal` built-in 모듈의 `Decimal` class를 사용해야 한다. `Decimal` class는 default로 28개의 고정된 decimal points를 제공한다. 요구한다면 더 많은 숫자의 decimal points를 제공할 수도 있다. 

`quantize` method를 사용하면 round될 때 정확한 approximation을 얻을 수 있다.

```python
rate = Decimal(‘0.05’)
seconds = Decimal(‘5’)
cost = rate * seconds / Decimal(‘60’)
print(cost)

# 0.004166666666666666666666666667

rounded = cost.quantize(Decimal(‘0.01’), rounding=ROUND_UP)
print(rounded)

# 0.01
```