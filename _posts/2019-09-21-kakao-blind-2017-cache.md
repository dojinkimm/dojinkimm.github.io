---
layout: post
title: "[프로그래머스 - 카카오 블라인드 2017] 캐시 (파이썬 풀이)"
date: 2019-09-20 10:00:00
author: Dojin Kim
categories: Problem_Solving
tags: ps python kakao_blind
cover:  "/assets/imgs/algorithm_background.jpg"
---
# 카카오 블라인드 2017 - 캐시

출처 : [프로그래머스 - 카카오 블라인드 2017 - 캐시](https://programmers.co.kr/learn/courses/30/lessons/17680)

## 문제 간단 요약
도시 이름 리스트 `cities`와 캐시 사이즈 `cacheSize`가 주어진다. `cities` 리스트 순서대로 도시 이름을 읽게 된다.

만약, 도시 이름이 캐시에 없으면 
- cache miss가 되서, 실행시간 += 5가 되고, 캐시에서 가장 이전에 사용되었던(LRU) 도시 이름을 삭제하고, 새롭게 읽어들인 도시 이름을 추가한다.
캐시에 있으면
- cache hit가 되서, 실행시간 += 1이 된다

`cities` 리스트를 다 읽었을 때 총 실행시간을 찾는 문제이다.


## 답안 설명
- 라인 2~3 - `cacheSize`가 0이면, 항상 cache miss이기 때문에 `cities`의 길이에 5를 곱한 만큼의 실행시간이 걸리게 된다.
- 라인 10 - cache miss일 때,
    - 라인 11~12 - cache 리스트 꽉 찬 상황에서, LRU에 따라 도시 이름 하나를 제거한다.
    - 라인 13 - cache miss이기 때문에 실행시간 += 5 를 한다.
- 라인 14 - cache hit일 때,
    - 라인 15 - hit된 도시 이름을 제거한다.
    - 라인 16 - cache hit이기 때문에 실행시간 += 1 을 한다
- 라인 18 - cache 리스트 맨 앞에다 새롭게 읽은 도시 이름을 추가한다.


<script src="https://gist.github.com/dojinkimm/86563d4ff601080757449eefb53491c4.js"></script>
