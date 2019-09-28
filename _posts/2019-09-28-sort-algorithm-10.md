---
layout: post
title: "Bucket Sort, 버킷 정렬이란?"
date: 2019-09-28 15:00:00
author: Dojin Kim
categories: Algorithm
tags: bucket_sort algorithm
cover:  "/assets/imgs/algorithm_background.jpg"
---
> CS 기초 알고리즘들을 다시 공부하며 글로 기록을 남기는 중입니다.

# Bucket Sort
Bucket Sort(버킷정렬)은 리스트 내의 값들이 범위 내에 일정하게 분포되어 있을 때 사용하는 것이 가장 적합하다. 0\~1 사이의 숫자들을 정렬하는 것과 같은 경우에 이 bucket sort를 사용할 수 있다. 

이 정렬 알고리즘을 쉽게 설명하자면, 리스트 내의 값들을 큼직하게 분류할 수 있을만한 카테고리 혹은 버킷들을 만든다. 그 다음에 리스트에서 버킷에 포함되는 값들을 정렬한다. 마지막에는, 버킷들을 기준으로 정렬을 한다. 설명은 어렵지만 그림으로 보면 이해가 더 쉽게 된다. 

<div align="center">
<img src="/assets/imgs/cs/bucket_sort.png" style="width:500px"/>
</div>

0\~1 사이 값들을 지닌 리스트가 있다고 가정해보자. 

1. Input Array의 값들을 일정한 값들을 기준으로 버킷으로 나눈다. 이 때 버킷의 갯수도 주어진 리스트와 길이를 같게 만들어서 분류를 한다.
2. 그 다음에는 각 버킷에 해당되는 값에 기존 리스트의 값들을 집어넣는다. 이 때 집어넣으면서 정렬을 한다. 여기서 사용되는 알고리즘은 이전에 배웠던 정렬 알고리즘을 사용해도 뙨다. 
3. 마지막으로 버킷들을 다 이어서 정렬된 하나의 리스트를 만든다.

```python
def bucket_sort(num):
    # 버킷을 담을 비어 있는 list를 하나 생성한다
    arr = [[] for _ in range(len(num))]

    # 버킷에다 각각 elements들을 넣는다
    # ex. 0.78 * 10 = 7.8
    # int(7.8)을 해서 arr[7] 버킷에 해당 값을 넣는다
    for n in num: 
        index = int(len(num) * n)  
        arr[index].append(n) 
    
    # 각 sub list들을 정렬한다
    for i in range(len(num)):
        arr[i] = insertion_sort(arr[i])

    # 각 버킷들을 연결한다
    k = 0
    for i in range(len(num)):
        for j in range(len(arr[i])):
            num[k] = arr[i][j]
            k += 1
            
    return num

number = [0.78, 0.17, 0.39, 0.26, 0.72, 0.94, 0.21, 0.12, 0.23, 0.68] 
print(number)
bucket = bucket_sort(number)
print(bucket)
```

## 다른 Sorting 알고리즘
- [Selection Sort(선택정렬)](https://dojinkimm.github.io/algorithm/2019/09/13/sort-algorithm-2.html)
- [Insertion Sort(삽입정렬)](https://dojinkimm.github.io/algorithm/2019/09/13/sort-algorithm-3.html)
- [Bubble Sort(버블정렬)](https://dojinkimm.github.io/algorithm/2019/09/14/sort-algorithm-4.html)
- [Quick Sort(퀵정렬)](https://dojinkimm.github.io/algorithm/2019/09/15/sort-algorithm-5.html)
- [Merge Sort(합병정렬)](https://dojinkimm.github.io/algorithm/2019/09/18/sort-algorithm-6.html)
- [Heap Sort(힙 정렬)](https://dojinkimm.github.io/algorithm/2019/09/19/sort-algorithm-7.html)
- [Counting Sort(계수정렬)](https://dojinkimm.github.io/algorithm/2019/09/22/sort-algorithm-8.html)
- [Radix Sort(기수 정렬)](https://dojinkimm.github.io/algorithm/2019/09/24/sort-algorithm-9.html)