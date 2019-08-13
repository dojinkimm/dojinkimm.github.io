---
layout: post
title: "[Python] Part 1_파이썬의 기초_print_input_comment"
date: 2018-08-01 16:00:00
author: Dojin Kim
categories: Python
tags: python
cover:  "/assets/imgs/python_cover.jpg"
---


<h2>no 1. 출력하는 법 - print()</h2>
- print()는 ()안에 들어가 있는 단어나 숫자 등을 출력해주는 함수이다.

	예)
```
>>> print(10)
10
>>> print('Hello world!')
Hello world!
>>> print(3.14)
>>> 3.14
```


<h2>no 2. 입력하는 법 - input()</h2>
- run하는 동안 유저의 키보드 입력을 받는 함수이다.
- 숫자를 적던 문자를 적던 항상 문자열의 형태로 받아진다.
- 입력한 값을 변수에 저장할 수 있다.

	예)
```
>>> how_old = input("How old are you? ")
How old are you? 23 	#23 은 사용자가 작성한 부분이고 23이 how_old라는 변수에 저장된다
```


<h2>no 3. 주석처리하는 법</h2>
- 코드를 자세히 설명하던가, 무언가를 노트하기 위해 사용된다.
- 주석은 코드의 일부분으로 생각되지 않고 무시된다.

	예)
```
>>> print("hello world") #hello world를 출력한다
hello world
```


