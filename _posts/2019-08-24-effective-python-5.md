---
layout: post
title: "[파이썬 코딩의 기술] 메타클래스와 attributes - @property, descriptor, getattr"
date: 2019-08-24 17:00:00
author: Dojin Kim
categories: Python
tags: python effective_python 파이썬코딩의기술
cover:  "/assets/imgs/python_cover.jpg"
---

> Effective Python - Brett Slatkin을 읽으면서 공부 및 정리를 하며 글을 쓰고 있습니다.

# Item 29. Getter와 Setter대신 일반속성 사용하기

다른 프로그래밍 언어를 사용하다가 Python으로 넘어온 사람들은 자연스럽게 getter와 setter을 class내 method로 만드려고 한다. 하지만, 이 방법은 Pythonic하지가 않다, 특히 기존 값에 추가적 연산을 할 때 불필요한 코드를 만들어낸다. 

```python
class OtherLanguage(object):
	def __init__(self, value):
		self.value = value

	def get_value(self):
		return self.value

	def set_value(self, value):
		self.value = value
    
a = OtherLanguage(1000)
# 매우 불필요한 코드
a.set_value(a.get_value()+500)
    
class Pythonic(object):
	def __init__(self, value):
		self.value = value

a = Pythonic(1000)
a.value += 500
```

이미 instance를 생성할 때 attribute가 초기화 된 이후에 set 혹은 get을 explicit하게 할 수 있는데,  `@property` 와 `setter`를 사용하면 된다. 이 둘의 특성은 값을 새로 설정 혹은 변경할 수 있을 뿐만 아니라, 조건을 둘 수 있다는 것이다.

```python
class Pythonic(object):
	def __init__(self, value):
		self.value = value

	@property
	def value(self):
		return self.value
	
	@value.setter
	def value(self, value):
		if value <= 0:
			raise ValueError(‘%f value must be > 0’ % value)
		self.value = value
```

해당 코드는 0보다 작은 값이 value에 set이 되면 에러를 일으킨다. `@property` 를 사용하는데 유의해야할 점은 getter이기 때문에 다른 연산 같은 것을 넣으면 안된다는 것이다. 에러는 일어나지 않지만, 본래 getter로서의 목적만 달성하도록 해야 한다.

# Item 31: 재사용 가능한 @property method에는 descriptor 사용하기

`@property` 의 가장 큰 문제점은 reuse(재활용)할 수 없다는 것이다. 같은 class내에 여러 attribute에 대해서, 그리고 다른 class간 reuse가 불가능하다. 

밑에 코드는 writing과 math 점수가 비슷한 logic을 거쳐감에도 다른 attribute이기 때문에 비슷한 코드를 반복해서 작성했다.

```python
class Exam(object):
	def __init__(self):
		self._writing_grade = 0
		self._math_grade = 0

	@staticmethod
	def _check_grade(value):
		if not (0 <= value <= 100):
			raise ValueError(‘Grade must be between 0 and 100’)

	@property
	def writing_grade(self):
		return self._writing_grade

	@writing_grade.setter
	def writing_grade(self, value):
		self._check_grade(value)
		self._writing_grade = value

	@property
	def math_grade(self):
		return self._math_grade

	@math_grade.setter
	def math_grade(self, value):
		self._check_grade(value)
		self._math_grade = value
```

이러한 문제점을 해결하기 위해서 `descriptor`를 사용하면 된다. `descriptor`는 \__get__ 과 \__set__ method를 제공해서 reuse를 할 수 있게 해준다. 다른 attribute들이 같은 logic에 대해서 같은 코드를 사용할 수 있게 된다.

```python
class Grade(object):
	def __init__(self):
		self._value = 0

	def __get__(self, instance, instance_type):
		return self._value

	def __set__(self, instance, value):
		if not (0 <= value <= 100):
			raise ValueError(‘Grade must be between 0 and 100’)
		self._value = value

class Exam(object):
	# Class attributes
	math_grade = Grade()
	writing_grade = Grade()
	science_grade = Grade()


exam = Exam()
exam.writing_grade = 40
```

위 코드를 설명하면, Exam class에서 Grade의 instance를 만들었다. 코드의 맨 마지막줄에는 exam.writing_grade가 있는데 이는 보통 class의 method를 호출할 때 사용한다. 하지만, 현 Exam에는 writing_grade라는 method가 없다, 그럴때 Python에서는 Exam의 class attribute에서 writing_grade가 있는지 찾는다. 그리고 그 class attribute가 object인지 확인하고 \__get__ \__set__ method를 가지고 있으면 `descriptor` protocol을 따르려는 것으로 간주한다.

여기서도 하나의 문제점이 있는데, 그건 바로 Grade()라는 instance가 share되고 있다는 것이다. 즉, Exam()의 instance를 여러개 만들어도 그 안에 있는 writing_grade와 같은 class attribute는 share되기 때문에 원하는 결과값이 안나온다는 것이다.


```python
first_exam = Exam()
first_exam.writing_grade = 82
second_exam = Exam()
second_exam.writing_grade = 75
print(‘Second’, second_exam.writing_grade) # Second 75
print(‘First ‘, first_exam.writing_grade) # First 72
```


share되는 것을 방지하기 위해서는 Grade class를 조금 수정해야 한다.

```python
class Grade(object):
	def __init__(self):
		self._values = WeakKeyDictionary() # 수정됨

	def __get__(self, instance, instance_type):
		if instance is None: # 수정됨
			return self
		return self._values.get(instance, 0)

	def __set__(self, instance, value):
		if not (0 <= value <= 100):
			raise ValueError(‘Grade must be between 0 and 100’)
		self._values[instance] = value # 수정됨
```

이제 이전처럼 여러개의 Exam instance를 만들면 각각 dictonary에 저장이 되기 때문에 class attribute에 접근했을 때 share되지 않는다. 여기서 단순하게 `dict()` 를 사용하지 않고 `WeakKeyDictionary()` 를 사용한 이유는 메모리 관리를 하기 위함이다.

WeakKeyDictionary는 weakref built-in 모듈에서 제공하는 class이다. 런타임 도중에 Exam instance가 더 이상 reference되지 않는 것을 감지하면 이 class는 Exam instance를 제거해서 메모리 관리를 한다. 만약 Exam instance들이 더 이상 사용되지 않으면 _values dict가 비어있을 것을 보장한다.

# Item 32: Lazy 속성에 \__**getattr__**, \__**getattribute__**, and \__**setattr__** 사용하기

Python에서 동적으로 instance attribute을 사용해야할 때가 있다. @property method나, descriptor는 미리 정의되있어야 하기 때문에 동적으로 사용하기 힘들다. 그 때 `__getattr__` 와 같은 특별한 method를 사용해야 한다. 이 method를 정의 하면 attribute가 object instance를 찾지 못할 때 해당 method를 호출한다. 

### \__getattr__

밑에 코드에서 data.foo가 없기에 자동으로 `__getattr__` method를 호출한다. `super().__getattr__` 를 사용하는 이유는 무한대의 recursion을 방지하기 위함이다. 또한, data.foo에서 foo가 `__getattr__` 의 인자로 전달된 것을 볼 수 있다. 그리고 자동으로 `setattr` 가 실행이 되서 foo를 instance dictionary에 저장한다. 그래서 두 번째로 data.foo를 호출했을 때 `__getattr__` 가 다시 실행되지 않은 것이다. 이러한 behavior는 schemaless 데이터에 접근할 때 매우 유용하다. 

```python
class LazyDB(object):
	def __init__(self):
		self.exists = 5

	def __getattr__(self, name):
		value = ‘Value for %s’ % name
		setattr(self, name, value)
		return value
    
class LoggingLazyDB(LazyDB):
	def __getattr__(self, name):
		print(‘Called __getattr__(%s)’ % name)
		return super().__getattr__(name)

data = LoggingLazyDB()
print(‘exists:’, data.exists)
print(‘foo: ’, data.foo)
print(‘foo: ’, data.foo)

# exists: 5
# Called __getattr__(foo)
# foo: Value for foo
# foo: Value for foo
```

### \__getattribute__

이제 다른 경우를 살펴보려고 한다. 유저가 db에서 transaction을 보려고 할 때 `__getattr__`는 적합하지 않다. 이때 우리가 사용해야하는 hook는 `__getattribute__`이다, 이 method는 object에서 해당 attribute로 접근할 때 마다 호출이 된다, 심지어 attribute dictionary 없을 때에도 호출이 된다. 쉽게 이야기하자면 위 코드에서 해당 attribute가 없을 때 `__getattr__`는 한번 호출되고 난 이후에는 다시 호출 되지 않았다. 하지만, `__getattribute__`에서는 계속 호출을 할 수 있는 것이다. 

```python
class ValidatingDB(object):
	def __init__(self):
		self.exists = 5

	def __getattribute__(self, name):
		print(‘Called __getattribute__(%s)’ % name)
		try:
			return super().__getattribute__(name)
		except AttributeError:
			value = ‘Value for %s’ % name
			setattr(self, name, value)
			return value

data = ValidatingDB()
print(‘exists:’, data.exists)
print(‘foo: ’, data.foo)
print(‘foo: ’, data.foo)

# Called __getattribute__(exists)
# exists: 5
# Called __getattribute__(foo)
# foo: Value for foo
# Called __getattribute__(foo)
# foo: Value for foo
```

### \__setattr__

`__setattr__`는 어떤 value가 Python object에 assign됐을 때 db에 넣으려고 할 때 사용된다. 이 method는 instance에 어떤 value가 assign될 때마다 항상 호출된다.


```python
class SavingDB(object):
	def __setattr__(self, name, value):
		# Save some data to the DB log
		# …
		super().__setattr__(name, value)

class LoggingSavingDB(SavingDB):
	def __setattr__(self, name, value):
		print(‘Called __setattr__(%s, %r)’ % (name, value))
		super().__setattr__(name, value)

data = LoggingSavingDB()
print(‘Before: ‘, data.__dict__)
data.foo = 5
print(‘After: ’, data.__dict__)
data.foo = 7
print(‘Finally:’, data.__dict__)

# Before: {}
# Called __setattr__(foo, 5)
# After: {‘foo’: 5}
# Called __setattr__(foo, 7)
# Finally: {‘foo’: 7}
```

# Item 33: Metaclass로 subclass 검증하기

Metaclass로 class가 올바르게 정의됐는지 검증할 수 있다. 복잡한 hierarchy를 가진 class를 구현할 때 특정한 style을 따르게 하던지, method override를 요구하던지, class attribute간 엄격한 rule을 설정하기를 원할 수 있다. 

Metaclass를 검증하는 코드를 제공해서 새로운 subclass가 만들어질 때마다 검증과정을 거치게 할 수 있다. Metaclass는 `type`을 inherit해서 정의된다. Default 경우에, metaclass는 `__new__` method에서 class와 연관된 내용들을 전달받는다. 

```python
class ValidatePolygon(type):
	def __new__(meta, name, bases, class_dict):
		# Don’t validate the abstract Polygon class
		if bases != (object,):
			if class_dict[‘sides’] < 3:
				raise ValueError(‘Polygons need 3+ sides’)
		return type.__new__(meta, name, bases, class_dict)

class Polygon(object, metaclass=ValidatePolygon):
	sides = None 
	# Specified by subclasses
	@classmethod
	def interior_angles(cls):
		return (cls.sides - 2) * 180

class Triangle(Polygon):
	sides = 3
```

만약 여러 edge를 가진 다각형을 만드려고 할 때 validating metaclass를 사용해서 새로 만들어진 subclass가 특정 조건을 지키는지 검증할 수 있다. 해당 코드 같은 경우에 다각형의 sides가 3 미만인 경우에는 error를 발생시킨다.