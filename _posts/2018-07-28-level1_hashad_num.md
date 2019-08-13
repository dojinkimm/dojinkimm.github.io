---
layout: post
title: "Level 1 - 2하샤드 수"
date: 2018-07-28 11:00:00
author: Dojin Kim
categories: Problem Solving
tags: ps algorithm
cover:  "/assets/instacode.png"
---

<h2>문제: 하샤드 수</h2>

양의 정수 x가 하샤드 수이려면 x의 자릿수의 합으로 x가 나누어져야 합니다. 
예를 들어 18의 자릿수 합은 1+8=9이고, 18은 9로 나누어 떨어지므로 18은 하샤드 수입니다. 자연수 n을 입력받아 n이 하샤드 수인지 아닌지 검사하는 함수, solution을 완성해주세요.


<h3>My Solution</h3>
```
class Solution {
  public boolean solution(int x) {
      boolean answer = true;
      int digit=0, original =x;
      while(original>0){
          digit = digit +(original%10);
          original/=10;
      }
      return x%digit==0 ? true : false ;
  }
}
```


<h3>Best solution</h3>
```
  public boolean isHarshad(int num){

    String[] temp = String.valueOf(num).split("");

    int sum = 0;
    for (String s : temp) {
        sum += Integer.parseInt(s);
    }

    if (num % sum == 0) {
            return true;
    } else {
      return false;
    }
    }


```



<bold> source: https://programmers.co.kr/learn/courses/30/lessons/12947?language=java </bold>
