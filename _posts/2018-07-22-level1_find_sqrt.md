---
layout: post
title: "Level 1 - 정수 제곱근 판별하기"
date: 2018-07-22 14:00:00
author: Dojin Kim
categories: Problem Solving
tags: ps algorithm
cover:  "/assets/instacode.png"
---

## Problem: Determining square root of a number.
When given a random long n, we are trying to determine whether a number has a square root.
```python
if (it has a square root called 'x') {
      return 'x+1' * 'x+1' ;
}else { return -1 }
```


<h2>문제: 정수 제곱근 판별</h2>

임의의 정수 n에 대해, n이 어떤 정수 x의 제곱인지 아닌지 판단하려 합니다.
n이 정수 x의 제곱이라면 (x+1)^2을 리턴하고, 
n이 정수 x의 제곱이 아니라면 -1을 리턴하는 함수를 완성하세요.

```python
class Solution {
  public long solution(long n) {
      long answer = 0; 
      String s =Double.toString(Math.sqrt(n));  //제곱근을 찾아서 String으로 저장한다.
      if(s.charAt(s.length()-2) == '.'){ //제곱근이 딱 나누어 떨어지는 수가 아니면 -1을 반환한다.
        answer = (long)Math.sqrt(n)+1;
        return answer * answer;
      }
      return -1;
  }
}

```


<bold> source: https://programmers.co.kr/learn/courses/30/lessons/12934 </bold>
