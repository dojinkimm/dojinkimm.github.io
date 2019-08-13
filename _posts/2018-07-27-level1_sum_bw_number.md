---
layout: post
title: "Level 1 - 두 정수 사이의 합"
date: 2018-07-27 22:30:00
author: Dojin Kim
categories: Problem_Solving
tags: ps algorithm
cover:  "/assets/instacode.png"
---


<h2>문제: 두 정수 사이의 합</h2>

두 정수 a, b가 주어졌을 때 a와 b 사이에 속한 모든 정수의 합을 리턴하는 함수, solution을 완성하세요. 
예를 들어 a = 3, b = 5인 경우, 3 + 4 + 5 = 12이므로 12를 리턴합니다.


<h3>My Solution</h3>
```
class Solution {
  public long solution(int a, int b) {
      long answer = 0;
      int start=a<b? a:b;
      int end=a>b? a:b;
      for(int i=start; i<=end;i++)
          answer+=i;
      return answer;
  }
}
```

<h3>Best solution</h3>
```

class Solution {

    public long solution(int a, int b) {
        return sumAtoB(Math.min(a, b), Math.max(b, a));
    }

    private long sumAtoB(long a, long b) {
        return (b - a + 1) * (a + b) / 2;
    }
}

```



<bold> source: https://programmers.co.kr/learn/courses/30/lessons/12912?language=java </bold>
