---
layout: post
title: "[Python] Part 3_파이썬의 기초_리스트"
date: 2018-08-02 15:00:00
author: Dojin Kim
categories: Python
tags: python
cover:  "/assets/imgs/python_cover.jpg"
---

<h2>no 11. 리스트 사용법 - 1 추가</h2>
- [] 안에 숫자 혹은 문자열을 넣는다.
- 리스트 안은 항상 같은 자료형을 지녀야 한다. (예) 문자열과 숫자 혹은 숫자와 실수 섞일 수 없다.
- 리스트 안의 값들은 언제든지 바뀔 수 있다.
- .append()를 사용하면 리스트의 뒤로 값을 계속 추가 할 수 있다.
- index로 각 리스트의 값에 접근할 수 있다

	예)
```
>>> soccer = ["Henry","Ronaldo","Messi"]
>>> soccer.append("Bale")
>>> print(soccer)
["Henry","Ronaldo","Messi","Bale"]
>>> print(soccer[2])
Messi
```

<h2>no 12. 리스트 사용법 - 2 메소드들</h2>
- sort()메소드는 정렬을 해준다
- count()메소드는 그 값이 리스트에 몇개가 있는지 알려준다
- reverse()메소드는 역순으로 뒤집어 준다 (이때 정렬해서 역순으로 만들어주는 것이 아닌걸 주의해야 한다)
- index()메소드는 해당 값이 몇번째 index에 있는지 알려준다
- insert(a,b)메소드는 a번째 위치에 b를 추가 시킨다
- remove()메소드는 해당 값을 리스트에서 제거한다 (이때 리스트에서 첫번째로 같은 값만 제거한다)
- pop()메소드는 index로 값을 제거한다 (default는 마지막 값)

	예)
```
>>> soccer = ["Henry","Ronaldo","Messi"]
>>> soccer.sort()
>>> print(soccer)
["Henry","Messi","Ronaldo"]
>>> print(soccer.count("Messi")) #Messi란 단어가 몇개 있는지 알려준다
1
```


<h2>no 13. 리스트 사용법 - 3 중첩 리스트</h2>
- 리스트 안에 새로운 리스트를 넣을 수가 있다
- slicing은 문자열과 비슷한 방법으로 된다.
- '+' 연산자는 리스트에서 리스트끼리 병합하게 한다
- '*' 연산자는 리스트를 숫자만큼 반복시킨다

	예)
```
>>> my_list = [1,2,3,['a','b','c']]
>>> print(my_list[3])
['a','b','c']
>>> print(my_list[-1][0]) #my_list의 마지막 값에서 첫번째 index인 값을 불러온다
'a'
```

