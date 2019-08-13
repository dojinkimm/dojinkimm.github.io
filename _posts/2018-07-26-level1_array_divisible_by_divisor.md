---
layout: post
title: "Level 1 - 나누어 떨어지는 숫자 배열"
date: 2018-07-26 14:00:00
author: Dojin Kim
categories: Problem Solving
tags: ps algorithm
cover:  "/assets/instacode.png"
---

<h2>Problem: array divisible by a divisor</h2>

Create a function that returns an array with numbers that are divisible by a given divisor. If elements of initial array are not divisible by a divisor return -1.
For exampe) if an arr is [5,9,7,10] and divisor is 5, return [5,10]


<h2>문제: 나누어 떨어지는 숫자 배열</h2>

array의 각 element 중 divisor로 나누어 떨어지는 값을 오름차순으로 정렬한 배열을 반환하는 함수, solution을 작성해주세요. divisor로 나누어 떨어지는 element가 하나도 없다면 배열에 -1을 담아 반환하세요.

예를들어) arr 가 [5,9,7,10]이고 divisor가 5이면 [5,10]을 리턴한다.



<h3>My Solution</h3>
```
import java.util.*;
class Solution {
  public int[] solution(int[] arr, int divisor) {
      int[] answer = {};
      int count=0;
      for(int i=0;i<arr.length;i++){
          if(arr[i]%divisor==0)
              count++;
      }
      if(count==0){
        answer=new int[1];
        answer[0]=-1;
      }else{
        answer=new int[count];
        count=0;
        for(int i=0;i<arr.length;i++){
          if(arr[i]%divisor==0){
              answer[count++]=arr[i];
          }
        }
      }
     Arrays.sort(answer);

      return answer;
  }
}
```

<h3>Best solution</h3>
```
public int[] divisible(int[] array, int divisor) {
        //ret에 array에 포함된 정수중, divisor로 나누어 떨어지는 숫자를 순서대로 넣으세요.
        return Arrays.stream(array).filter(factor -> factor % divisor == 0).toArray();
    }
```



<bold> source: https://programmers.co.kr/learn/courses/30/lessons/12910?language=java </bold>
