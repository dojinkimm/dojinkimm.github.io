---
layout: post
title: "Insertion Sort, 삽입 정렬이란?"
date: 2019-09-13 14:00:00
author: Dojin Kim
categories: Algorithm
tags: insertion_sort algorithm
cover:  "/assets/imgs/algorithm_background.jpg"
redirect_to: https://devjin-blog.com/sort-algorithm-3/
---

> CS 기초 알고리즘들을 다시 공부하며 글로 기록을 남기는 중입니다.

# Insertion Sort
<div align="center">
<img src="/assets/imgs/cs/insertion_sort_pseudo.png" style="width:400px"/>
</div>

### Pseudo-code 설명
위는 Insertion Sort(삽입정렬)의 pseudo-코드이다. j가 2부터 시작하고 이는 list내 2번째 item을 의미한다, 즉, 프로그래밍할 때는 index 1을 의미한다. 이 알고리즘은 list를 iterate하면서 list 앞 쪽부터 정렬을 해나간다. 정렬을 하고 나서 그 다음 item으로 이동하고 해당 item을 정렬된 왼쪽 list에 정렬된 순서에 삽입을 한다. <br/>

step별로 풀어보면 다음과 같다:

1. list의 2번째 item부터 시작한다. (첫번째 item은 이미 정렬된 list라고 간주한다, item이 하나인 list에서 그 item은 항상 정렬된 상태이기 때문이다)
2. key라는 변수에 list의 j번째 값을 저장한다.
3. i라는 변수에 j - 1 값을 저장한다. (list의 i번째 값을 왼쪽에 정렬된 list에 비교를 하며 삽입하기 위함이다)
4. while문
    1. key가 list[i]보다 작으면 list[i+1] (j의 위치를 의미한다)에 정렬된 list의 가장 큰 값을 복사한다.
    2. i -= 1 을 하고 4.1번을 반복한다. 만약 list맨 앞까지 도달하면 loop을 종료한다.
5. key 값을 list[i+1]에 저장한다. (이 부분이 정렬된 list에 새로운 숫자를 삽입하는 것처럼 보이게 만든다)
6. j += 1을 하고 2번부터 다시 반복한다.

<br/>
한 iteration에 list내의 모든 item을 한번 살펴본다. 그리고 iteration중에 선택된 item을 정렬된 list에서 다시 비교하며 살펴본다. 그래서, 이 sorting algorithm의 시간 복잡도는 `O(n^2)`가 되는 것이다.


<div align="center">
<img src="/assets/imgs/cs/insertion_sort.png" style="width:400px"/>
</div>

<div align="center">

source: <a href="https://www.geeksforgeeks.org/recursive-insertion-sort/">geeks-for-geeks-insertion-sort</a>

</div>


그림에 있는 iteration 과정을 간단히 살펴보자

첫번째 iteration에서는 9는 이미 정렬된 상태이고, list[1]인 7과 index하나 작은 9와 비교를 한다. 7이 더 작기 때문에 7을 9앞으로 삽입을 한다. 

두번째 iteration에서는 [7,9]는 이미 정렬된 상태이고, list[2]인 6과 9를 비교한다. 9가 더 크기 때문에 한칸 더 왼쪽으로 움직여서 6과 7을 비교한다. 이때 6이 더 작기 때문에 맨 앞으로 삽입을 한다. 

세번째 iteration에서는 15가 정렬된 list [6,7,9] 중에서 가장 큰 9보다도 크기 때문에 추가적인 이동을 하지 않는다. 

...

list의 맨 마지막 item을 왼쪽에 정렬된 list에 정렬된 상태를 유지할만한 위치에 삽입을 하게 되면 ⇒

**리스트가 정렬이 된다.**



### Python Code

```python
import random

def insertion_sort(num):
    for j in range(1,len(num)):
        key = num[j] 
        i = j - 1
        while i>=0 and (num[i] > key): # i가 list맨 앞까지 오던지, num[i]가 위에 저장된 num[j]보다 작으면 loop빠져 나온다.
            num[i+1] = num[i]
            i -= 1
        num[i+1] = key
    return num


number = [i for i in range(10)]
random.shuffle(number)
print(number)
insertion = insertion_sort(number)
print(insertion)
```


## 다른 Sorting 알고리즘
- [Selection Sort(선택정렬)](https://dojinkimm.github.io/algorithm/2019/09/13/sort-algorithm-2.html)
- [Bubble Sort(버블정렬)](https://dojinkimm.github.io/algorithm/2019/09/14/sort-algorithm-4.html)
- [Quick Sort(퀵정렬)](https://dojinkimm.github.io/algorithm/2019/09/15/sort-algorithm-5.html)
- [Merge Sort(합병정렬)](https://dojinkimm.github.io/algorithm/2019/09/18/sort-algorithm-6.html)
- [Heap Sort(힙 정렬)](https://dojinkimm.github.io/algorithm/2019/09/19/sort-algorithm-7.html)
- [Counting Sort(계수정렬)](https://dojinkimm.github.io/algorithm/2019/09/22/sort-algorithm-8.html)
- [Radix Sort(기수 정렬)](https://dojinkimm.github.io/algorithm/2019/09/24/sort-algorithm-9.html)
- [Bucket Sort(버킷 정렬)](https://dojinkimm.github.io/algorithm/2019/09/28/sort-algorithm-10.html)


