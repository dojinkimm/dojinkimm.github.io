---
layout: post
title: "Level 1 - 정수 내림차순 구하기"
date: 2018-07-28 12:00:00
author: Dojin Kim
categories: Problem_Solving
tags: ps algorithm
cover:  "/assets/instacode.png"
---


<h2>문제: 정수 내림차순 구하기</h2>

함수 solution은 정수 n을 매개변수로 입력받습니다. n의 각 자릿수를 큰것부터 작은 순으로 정렬한 새로운 정수를 리턴해주세요. 예를들어 n이 118372면 873211을 리턴하면 됩니다.



<h3>My Solution</h3>
```
import java.util.*;

class Solution {
  public long solution(long n) {
      long answer = 0;
      char[] array=Long.toString(n).toCharArray();
      Arrays.sort(array);
     
      String reverse = new StringBuffer(new String(array)).reverse().toString();
      
      return Long.parseLong(reverse);
  }
}
```


<h3>Best solution</h3>
```
  public int reverseInt(int n){

        String str = Integer.toString(n);
        char[] c = str.toCharArray();
        Arrays.sort(c);
        StringBuilder sb = new StringBuilder(new String(c,0,c.length));  
        return Integer.parseInt(((sb.reverse()).toString()));
    }

```




<bold> source: https://programmers.co.kr/learn/courses/30/lessons/12933?language=java </bold>
