---
layout: post
title: "Level 1 - 가운데 글자 가져오기"
date: 2018-07-26 16:00:00
author: Dojin Kim
categories: Problem Solving
tags: ps algorithm
cover:  "/assets/instacode.png"
---

<h2>Problem: Finding a letter in the center of the word</h2>

When a word s is given, a function must return a letter in the center. If the word's length is even it should return two letters.


<h2>문제: 가운데 글자 알아내기</h2>

단어 s의 가운데 글자를 반환하는 함수, solution을 만들어 보세요. 단어의 길이가 짝수라면 가운데 두글자를 반환하면 됩니다.



<h3>My Solution</h3>
```
class Solution {
  public String solution(String s) {
      String answer = "";
      int length = s.length()/2;
      return s.length()%2 ==0 ? s.substring(length-1,length+1)
          : s.substring(length, length+1);
  }
}
```

<h3>Best solution</h3>
```
 String getMiddle(String word){

        return word.substring((word.length()-1) / 2, word.length()/2 + 1);
    }
```



<bold> source: https://programmers.co.kr/learn/courses/30/lessons/12903?language=java </bold>
