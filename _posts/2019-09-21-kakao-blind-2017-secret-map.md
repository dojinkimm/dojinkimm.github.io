---
layout: post
title: "[프로그래머스 - 카카오 블라인드 2017] 비밀지도 (파이썬 풀이)"
date: 2019-09-21 11:00:00
author: Dojin Kim
categories: Problem_Solving
tags: ps python kakao_blind
cover:  "/assets/imgs/algorithm_background.jpg"
---
# 카카오 블라인드 2017 - 비밀지도

출처 : [프로그래머스 - 카카오 블라인드 2017 - 비밀지도](https://programmers.co.kr/learn/courses/30/lessons/17681)

## 문제 간단 요약
10진수로 된 숫자들 array 2개와 array의 길이를 전달받는다. 해당 10진수들을 이진수로 변환한 다음에 array들끼리 `OR`연산을 해야 한다. <br/>

ex) 10001 과 01010 이면 11011이 되는 것이다. <br/>

이 때, 1을 #, 그리고 0을 공백으로 변환을 하는 것이 문제이다.


## 답안 설명
### decimal_to_binary 함수
- 라인 3 - 10진수가 0보다 클 때까지 loop 진행한다
    - 라인 4 - 10진수 숫자를 2로 나눈 나머지를 array에 추가한다
    - 라인 5 - 주어진 숫자를 2로 계속 나눈다
- 라인 7~8 - 2에 딱 떨어지고 n보다 작은 숫자들은 앞에 0이 추가되어야 한다

### solution 함수
- 라인 14~15 - array1과 array2에 있는 10진수들을 `decimal_to_binary` 함수에 보내서 2진수들의 list를 반환받는다 
- 라인 17~21 - 두 2진수로 된 array를 비교 하고 `OR` 연산을 적용한다. 그러고 나서 1은 # 0은 공백으로 변환한다


<script src="https://gist.github.com/dojinkimm/86503a90ca71c56d516408c4225504a0.js"></script>