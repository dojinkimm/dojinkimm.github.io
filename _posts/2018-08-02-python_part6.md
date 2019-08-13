---
layout: post
title: "[Python] Part 5_파이썬의 기초_loops"
date: 2018-08-02 17:00:00
author: Dojin Kim
categories: Python
tags: python
cover:  "/assets/imgs/python_cover.jpg"
---


<h2>no 17. for 사용법 - 1 </h2>
- for는 횟수를 기준으로 while은 조건을 기준으로 반복한다

	예)

```
numbers = [1,2,3,4,5]
for num in numbers:
	print(num)
1
2
3
4
5

```

<h2>no 18. for 사용법 -2 </h2>
- range(stop) 은 0부터 stop전 까지 반복한다
- range(start,stop) 은 start부터 stop전까지 반복한다

	예)

```
for n in range(3):
    print(n)

0
1
2
```
```
for n in range(4, 6):
    print(n)

4
5
```

<h2>no 19. for 사용법 -3 comprehension</h2>
- 리스트를 만드는 강력하고 간결한 방법입니다.

	예)

```
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
odd_numbers = []

for number in numbers:
    if number % 2 == 1:  # 2로 나눴을 때 1이 남으면 홀수입니다.
        odd_numbers.append(number)
```

```
odd_numbers = [number for number in numbers if number % 2 == 1]
```