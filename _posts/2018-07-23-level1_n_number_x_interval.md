---
layout: post
title: "Level 1 - x만큼 간격있는 n개의 숫자 찾기"
date: 2018-07-23 14:00:00
author: Dojin Kim
categories: Problem_Solving
tags: ps algorithm
cover:  "/assets/instacode.png"
---

<h2>Problem: Finding n numbers with x intervals</h2>

Class solution gets x and n as inputs.
It has to return an array of n that starts from x and increases by x.

<h2>문제: x 만큼 간격있는 n개의 숫자 찾기</h2>

함수 solution은 정수 x와 자연수 n을 입력 받아, x부터 시작해 x씩 증가하는 숫자를 n개 지니는 리스트를 리턴해야 합니다.

## Java

```java
class Solution {
  public long[] solution(int x, int n) {
      long[] answer = new long[n];
      answer[0] = x;
      for (int i=1; i<n;i++)
          answer[i]= answer[i-1] + x;

      return answer;
  }
}

```

## Python

```python
def solution(x, n):
    answer = []
    answer.append(x)
    for i in range(1,n):
        answer.append(answer[i-1]+x)
    return answer
```


<bold> source: https://programmers.co.kr/learn/courses/30/lessons/12954 </bold>
