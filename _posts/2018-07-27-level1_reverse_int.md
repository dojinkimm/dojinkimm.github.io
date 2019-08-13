---
layout: post
title: "Level 1 - 자연수 뒤집어 배열로 만들기"
date: 2018-07-27 22:00:00
author: Dojin Kim
categories: Problem Solving
tags: ps algorithm
cover:  "/assets/instacode.png"
---


<h2>문제: 자연수 뒤집어 배열로 만들기</h2>

자연수 n을 뒤집어 각 자리 숫자를 원소로 가지는 배열 형태로 리턴해주세요. 예를들어 n이 12345이면 [5,4,3,2,1]을 리턴합니다.


<h3>My Solution</h3>
```
class Solution {
  public int[] solution(long n) {      
      int[] answer = new int[Long.toString(n).length()];
      int i=0;
      while(n>0){
        answer[i++]=(int)(n%10);
        n=n/10;
      }
      return answer;
  }
}
```

<h3>Best solution</h3>
```

class Solution {
  public int[] solution(long n) {
      String a = "" + n;
        int[] answer = new int[a.length()];
        int cnt=0;

        while(n>0) {
            answer[cnt]=(int)(n%10);
            n/=10;
            System.out.println(n);
            cnt++;
        }
      return answer;
  }
}

```



<bold> source: https://programmers.co.kr/learn/courses/30/lessons/12932?language=java </bold>
