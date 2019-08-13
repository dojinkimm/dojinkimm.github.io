---
layout: post
title: "[Python] Part 4_파이썬의 기초_튜플"
date: 2018-08-02 16:00:00
author: Dojin Kim
categories: Python
tags: python
cover:  "/assets/imgs/python_cover.jpg"
---

<h2>no 14. 튜플 사용법 </h2>
- () 안에 숫자 혹은 문자열을 넣는다.
- 튜플은 다른 자료형들이 포함될 수 있다.
- 튜플은 추가하는 것은 가능하다
- 하지만 한번 설정된 값들을 변경 혹은 제거할 수는 없다.

	예)
```
>>> t1 = (1, 2, 'a', 'b')
```

<h2>no 15. 패킹 언패킹</h2>
- 패킹은 한꺼번에 여러 요소들을 묶는 것을 의미한다
- 언패킹은 묶여있는 값들을 푸는 것을 의미한다
- 두 변수의 값도 한번에 바꾸는 것이 가능하다

	예)
```
>>> my_tuple = (1, 2, 3)
>>> num1, num2, num3 = my_tuple
>>> print(num1)
1
>>> print(num2)
2
```
```
>>> num1 = 1
>>> num2 = 2
>>> num1, num2 = num2, num1
>>> print(num1)
2
>>> print(num2)
1
```

<h2>no 16. 딕셔너리</h2>
- 키와 값을 가지고 있는 자료형이다.
- 말 그대로 사전처럼 단어와 단어에 대한 의미가 있다고 이해하면 쉽다.
- value는 중복될 수 있지만 key값은 중복되면 안된다.
- keys() 함수는 딕셔너리의 key값들을 모아서 리스트로 리턴한다.
- values() 함수는 딕셔너리의 value값들을 모아서 리스트로 리턴한다.
- items() 함수는 key와 value의 쌍을 튜플로 묶은 값을 dict_items 객체로 돌려준다.
- clear() 함수는 다 지운다.
- get()함수의 () 안에 key 값을 두면 value를 리턴한다.
- 

	예)
```
>>> dic = {'name':'pey', 'phone':'0119993323', 'birth': '1118'}
>>> dic['major'] = 'computer' #딕셔너리 추가하는 법
>>> del dic['name'] #딕셔너리 삭제하는 법
```