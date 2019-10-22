---
layout: post
title: "[백준 - DP] 1912 - 연속합 - 파이썬"
date: 2019-10-21 17:00:00
author: Dojin Kim
categories: Problem_Solving
tags: ps algorithm python
cover:  "/assets/instacode.png"
---

# 1912 - 연속합 [DP]

### 출처 : <a href="https://www.acmicpc.net/problem/1912"> 백준 1912 연속합</a>

## 문제
이 문제는 연속으로 더 했을 때 가장 큰 값이 나올 수 있는 수를 구하는 것이다. [10, -4, 3, 1, 5, 6, -35, 12, 21, -1]라는 배열이 있을 때 12+21=33이 연속 합 중에서는 가장 큰 수가 되기 때문에 답은 33이 된다. 

DP 문제이기 때문에 기존 배열과 같은 배열을 선언하고, 자기 자신 & 자신과 자신 앞의 수를 더했을 때의 수 중에서 더 큰 수를 저장하면 된다. 그런 다음에 그 중에서 가장 큰 수를 찾으면 그 값이 답이 된다.

## 풀이
```python
import sys
r = sys.stdin.readline

N = int(r())
arr = list(map(int, r().split()))
ans = [0] * N
ans[0] = arr[0]
for i in range(1, N):
    ans[i] = max(arr[i], arr[i]+ans[i-1])
print(max(ans))
```