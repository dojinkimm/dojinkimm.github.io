---
layout: post
title: "[Effective Python] Pythonic thinking - enumerate, zip,  try-except-else-finally"
date: 2019-08-19 15:00:00
author: Dojin Kim
categories: Python
tags: python effective_python 
cover:  "/assets/imgs/python_cover.jpg"
---

> Effective Python - Brett Slatkin을 읽으면서 공부 및 정리를 하며 글을 쓰고 있습니다. Python을 잘쓰기 위한 총 59가지의 방법을 소개하고 있다. 다만, post에서는 많이 헷갈려하고, 알면 도움이 될만한 방법들 위주로 소개할 예정이다.

Pythonic thinking 이어서 작성하고 있습니다.

# Item 10. Enumerate

주로 Python으로 list를 iterate할 때 `for i in range(len(list_name))` 혹은 `for name in list_name` 형식을 많이 쓰는 경우가 있다. 

첫 번째 경우는 list의 크기만큼 iterate를 하고 index로 접근을 하고, 두 번째 경우는 각 iteration마다 item이 name 변수로 들어오게 된다. 

```python
fruit = ['banana', 'apple', 'orange', 'pineapple', 'melon']
for i in range(len(fruit)):
	print(fruit[i])

for f in fruit:
	print(f)
```

index로 접근하는 것보다 각 iteration마다 list의 item을 사용하는 것이 더 직관적이고 코드의 길이도 줄여준다. 하지만 프로그래밍을 하다보면 iteration을 할 때 index로 item에 접근할 필요가 있을 때가 있다. 예) 그 이전 item 혹은 그 이후 item에 접근할 때.

그 때 `enumerate` 를 사용하는 것이 가장 좋은 방법이고, 가능하면 range() 대신에 `enumerate` 을 사용하는 것이 좋다. `for i,f in enumerate(fruit)` 에서 앞에 `i` 는 index range에서처럼 index를 의미하고 `f` 는 fruit list의 iteration마다의 item을 의미한다.

```python
for i,f in enumerate(fruit):
	print(fruit[i])
	print(f)
	# 같은 것을 print함
```

# Item 11. List를 동시에 iterate하려면, zip

list가 여러개 있고 동시에 list를 iterate하고 싶은 경우가 있다. (default로 길이가 제일 짧은 list만큼만 iteration이 이뤄진다)

예) 유저 이름 list, 유저 나이 list가 따로 있을 때, 유저가 20세 이상일 때만 이름을 출력하고 싶을 때 

```python
name = ['Henry', 'Lisa', 'Dorosi', 'Gimmy']
age = [10, 22, 32, 17]

# 기존 방식
for i in range(len(name)):
	if age[i] > 20:
		print(name[i], age[i])


# zip
for n,a in zip(name,age):
	if a > 20:
		print(n,a)
```

이런 식으로 zip을 사용하게 되면 index으로 인해 복잡해지는 코드를 간결하게 줄 일 수 있다. zip은 여러 list를 하나로 묶어서 하나씩 iterate하는 역할을 한다.

# Item 13. try-except-else-finally

예외사항을 처리할 때 사용되는 명령어들이 있다: try, except, else, finally. Try는 주로 실행하려고 한는 구문에 에러가 있을 수 있을 때 사용을 한다. 

**except**는 어떤 error가 발생했는지 지정할 수 있다. 간단히 `Exception as e` 라고 할 수 있지만, exception의 종류도 많기 때문에 보통은 조금 상세하게 지정을 한다. 

**else**는 예상했던 exception이 발생하지 않았다면 다음 구문을 실행한다, 이 때 다른 exception이 발생할 수 있다. 

마지막으로 **finally**는 모든 구문이 실행되고 나서 맨 마지막에 항상 실행된다. Exception이 일어났을 때도 혹은 아무 exception이 일어나지 않았을 때도 finally는 실행이 된다.

```python
handle = open(‘/tmp/random_data.txt’):
try:
	data = handle.read()
	op = json.loads(data)
	value = (op[‘numerator’]/op[‘denominator’]) # 0이 분모로 오는 error가 발생할 수 있음 
except ZeroDivisionError as e:
	return e
else: # 0으로 나누는 error가 발생하지 않았다면 이 구문 실행
	op[‘result’] = value
	handle.write(result)  
finally:
	handle.close() # 다 실행이 됬다면 run함(error가 발생했던 안 발생했던간에)
```