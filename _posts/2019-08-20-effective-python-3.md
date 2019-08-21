---
layout: post
title: "[Effective Python] Functions - Closures, Positional, Keyword Arguments"
date: 2019-08-20 17:00:00
author: Dojin Kim
categories: Python
tags: python effective_python 
cover:  "/assets/imgs/python_cover.jpg"
---

> Effective Python - Brett Slatkin을 읽으면서 공부 및 정리를 하며 글을 쓰고 있습니다. Python을 잘쓰기 위한 총 59가지의 방법을 소개하고 있다. 다만, post에서는 많이 헷갈려하고, 알면 도움이 될만한 방법들 위주로 소개할 예정이다.

# Item 15. Closures

보통 closures를 사용하는 언어를 생각하면 javascript를 떠올린다. 하지만, Python에서 closure라는 개념이 사용되는지 잘 알지 못한다. Python도 closure를 지원한다, function에 nested됀 형태로 closure를 사용할 수 있다. 

```python
def sort_priority(values, group):
	def helper(x):
		if x in group:
			return (0, x)
		return (1, x)
	values.sort(key=helper)

numbers = [8, 3, 1, 2, 5, 4, 7, 6]
group = {2, 3, 5, 7}
sort_priority(numbers, group)
print(numbers)
# [2, 3, 5, 7, 1, 4, 6, 8]
```

이 코드는 리스트를 정렬하되 특정한 group에 더 우선순위를 둬서 정렬을 시키는 코드이다. `def helper` 는 closure이기 때문에 `def sort_priority` 밖에서는 접근을 할 수 없다. 외부에서 접근을 불가능하게 하고 한 함수 내에서만 사용하고 싶은 함수를 이처럼 nested 형태로 만들어서 사용할 수 있다.

다만, 여기서 주의해야할 점이 있다. 상위 레벨에 있는 변수와 같은 이름이 closure내에  정의되면, 이 변수는 상위 레벨 변수를 가린다. 밑에 예시 코드를 보면 sort_priority에서 found가 False로 초기화되었고 sort가 되면서 found=True가 됬음을 알 수 있다. 하지만, found를 print찍어보니 False가 결과로 나온다. 이렇게 된 이유는 closure인 helper 함수에서 found는 위 레벨의 found와 다른 변수이기 때문이다. 


```python
def sort_priority(values, group):
	found = False
	def helper(x):
		if x in group:
			found = True
			return (0, x)
		return (1, x)
	values.sort(key=helper)
	return found

found = sort_priority(numbers, group)
print(numbers)
print(found)
# [2, 3, 5, 7, 1, 4, 6, 8]
# False

```

Closure내에서 상위 레벨의 변수를 사용하고 싶으면 `nonlocal` 이라는 키워드를 추가해야 한다.

```python
def sort_priority(values, group):
	found = False
	def helper(x):
		nonlocal found # 추가된 부분
		if x in group:
			found = True
			return (0, x)
		return (1, x)
	values.sort(key=helper)
	return found
```

# Item 18. Positional Argument

함수에 인자를 전달할 때, 상황마다 어떤 인자는 전달이 필요할 때가 있고 어떤 인자는 없어도 될 때가 있다. 이 때 parameter에 `*` 을 추가하면 선택적으로 argument를 전달할 수 있다.

밑에 예시에서 첫 번째 인자 이후에 들어오는 인자들은 list 형식으로 ***values**에 들어가게 된다.  반대로 list를 전달하고 싶으면 `*` 을 전달하는 인자에 추가해야 한다. 하지만 argument에 `*` 을 추가하는 것은 input size가 작을 때만 잘 작동한다. 

```python
def log(message, *values): 
	if not values:
		print(message)
	else:
		values_str = ‘, ‘.join(str(x)for x in values)
		print(‘%s: %s’ % (message, values_str))

log('My numbers are', 1, 2)
log('Hi there') 

# list 전달
l = [1,2,3,4]
log('My numbers are', *l)
```

# Item 19. Dynamic Default Arguments에 None 사용하기

처음에 함수를 정의할 때 default arguments들은 한번만 execute 된다. 밑에 코드를 보면 함수를 실행시킬 때마다 시간이 바뀌기를 기대했을 테지만, 정의 됬을 때 한번만 execute됐기 때문에 바뀌지 않았다.

```python
def log(message, when=datetime.now()):
	print(‘%s: %s’ % (when, message))

log(‘Hi there!’)
sleep(0.1)
log(‘Hi again!’)

# 2014-11-15 21:10:10.371432: Hi there!
# 2014-11-15 21:10:10.371432: Hi again!
```

이러한 경우에는 datetime.now()를 default argument를 두기 보다 None으로 설정하고, 함수 내에서 datetime.now()를 실행시키는 것이 프로그램 본래 목적에 더 맞을 것이다.


```python
def log(message, when=None):
	when = datetime.now() if when is None else when
	print(‘%s: %s’ % (when, message))
```

# Item 20, 21. Keyword Argument

Python에는 keyword argument라는 것이 있는데, 다른 값이 들어오지 않을 때 default로 어떤 값을 미리 정의해놓는 것이다. 위에서 when=datetime.now() 가 keyword argument로 볼 수 있다. 

```python
def calculate(a, b, add=True, multiply=True):
	if add and multiply:
		return (a+b)*10
	elif add:
		return (a+b)
	else:
		return 0

calculate(10,30,False,False)
calculate(10,30,add=False,multiply=False)
```

위 코드에서 두 calculate() 모두 같은 값을 전달 받는다. 그리고 default keyword argument의 값도 변경할 수 있다. 하지만, keyword argument를 변경할 때 유저는 변경하는 이유가 명확해야 한다. 보통 keyword argument에 default값에는 왠만해서는 변경되지 않는 값을 둔다. 그렇기 때문에 해당 값ㅇ르 유저가 변경하려고 하면 유저가 clear하게 자신이 무엇을 바꾸고 있는지 알아야 한다. 

유저가 직접 add=False, multiply=False라고 명시를 해주면 목적이 있어서 변경하는 것이라고 파악할 수 있지만, 그 위의 코드처럼 False, False라고만 작성돼있으면 그 의도를 파악하기 힘들다. 그래서 Python에서는 강제로 keyword argument를 변경시에 명시하도록 만들 수 있다.

```python
def calculate(a, b, *, add=True, multiply=True): #변경된 부분
	...

calculate(10,30,False,False) # error
calculate(10,30,add=False,multiply=False) # no error
```

이와 같이 keyword argument와 positional arguments사이에 `*` 를 추가해주면 이 뒤로는 keyword argument이고 항상 명시되어야 한다는 것을 의미한다. 

⇒ 이렇게 하는 것은 programming을 조금 더 clear하게 작성하기 위함이다.