---
layout: post
title: "Level 1 - 문자열 다루기 기본"
date: 2018-07-27 21:00:00
author: Dojin Kim
categories: Problem_Solving
tags: ps algorithm
cover:  "/assets/instacode.png"
---


<h2>문제: 문자열 다루기 기본</h2>

문자열 s의 길이가 4혹은 6이고, 숫자로만 구성되있는지 확인해주는 함수, solution을 완성하세요.
예를들어 s가 a234이면 False를 리턴하고 1234라면 True를 리턴하면 됩니다.


## Java

<h3>My Solution</h3>
```java
class Solution {
  public boolean solution(String s) {
      boolean answer = true;
      
      if(s.length()!=4&&s.length()!=6){
          answer=false;
      }else{
          for(int i=0;i<s.length();i++){
              if(s.charAt(i)<48||s.charAt(i)>58){
                  answer=false;
                  break;
                }
          }
      }
      
      return answer;
  }
}
```

<h3>Best solution</h3>
```java

class Solution {
  public boolean solution(String s) {
      if(s.length() == 4 || s.length() == 6){
          try{
              int x = Integer.parseInt(s);
              return true;
          } catch(NumberFormatException e){
              return false;
          }
      }
      else return false;
  }
}
```



<bold> source: https://programmers.co.kr/learn/courses/30/lessons/12918?language=java </bold>
