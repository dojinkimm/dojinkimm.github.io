---
layout: post
title: "[파이썬 코딩의 기술] 클래스와 상속 - __call__, @classmethod, super, mixin"
date: 2019-08-22 17:00:00
author: Dojin Kim
categories: Python
tags: python effective_python 파이썬코딩의기술
cover:  "/assets/imgs/python_cover.jpg"
---

> Effective Python - Brett Slatkin을 읽으면서 공부 및 정리를 하며 글을 쓰고 있습니다.

# Item 23.  \__call__

`__call__` 은 helper class로 함수가 호출될 때 인자로 들어가지면 사용되는 함수이다. 이 함수가 왜 필요한지 알아보기 위해서 이 함수를 사용하지 않을 때의 경우를 보려고 한다.

```python
def log_missing():
    print(‘Key added’)
    return 0

current = {‘green’: 12, ‘blue’: 3}
increments = [(‘red’, 5),(‘blue’, 17),(‘orange’, 9),] 
result = defaultdict(log_missing, current)
print(‘Before:’, dict(result))
for key, amount in increments:
    result[key] += amount
print(‘After: ‘, dict(result))

# Before: {‘green’: 12, ‘blue’: 3}
# Key added
# Key added
# After: {‘orange’: 9, ‘green’: 12, ‘blue’: 20, ‘red’: 5}
```


dictionary를 만들 때 current라는 dictionary만 넣어준 것이 아니라 함수 `log_missing`도 함께 인자로 넣어줬다. 그리고 dictionary에 새로운 key 값이 들어갈 때마다 `log_missing` 함수가 작동을 한다. 어떤 작업이 이뤄졌다는 helper function을 구현해봤다.

하지만, class의 형태로 만드는 것이 언제나 더 reusable하기 때문에 class로 만드는 것이 더 좋다. helper function이기 때문에 method의 이름을 어떻게 정해야하는지가 애매하다. 이러한 상황에 따로 method명을 주지 않고 `__call__` 을 사용하면 된다.

밑에 코드를 설명하자면, 일단 `counter=CountMissing()`으로 instance화를 했다. `defaultdict`를  생성할 때 이 class를 전달하는 것처럼 보이지만, 실제로는 `__call__` 함수가 전달된 것이다. 그래서 defaultdict에 새로운 key가 추가될 때 마다 `self.added+=1` 계산이 이뤄지게 되는 것이다. 

`__call__`은 인스턴스 자체가 아닌 함수를 전달해야 하는 부분에서 따로 counter.__call__ 같은 명령어 없이 `__call__`함수를 전달하는 역할을 한다.

```python
class CountMissing(object):
    def __init__(self):
        self.added = 0

    def __call__(self):
        self.added += 1
        return 0

counter = CountMissing()
result = defaultdict(counter, current) 
for key, amount in increments:
    result[key] += amount
assert counter.added == 2
```


# Item 24. @classmethod로 generic하게 object construct 하기

다른 언어에서는 constructor polymorphosm을 지원해서 subclass가 generic하게 사용될 수 있게 한다. Python에서 문제점은 **init**이라는 single constructor method만 지원한다는 것이다. 그럼에도 generic한 method를 만드는 것은 프로그램의 reusability(재활용성)을 위해 꼭 필요한 부분이다. 이러한 문제점을 해결하는 것이 바로 Python의 **`@classmethod`**이다.

일단 **`@classmethod`**가 있는 함수는 `cls` 를 첫 인자로 받는다 (이름은 달라도 된다). `cls` 는 class가 인자로 전달된다는 의미이다.

```python
from datetime import date 
    
class Person: 
    def __init__(self, name, age): 
        self.name = name 
        self.age = age 
        
    # a class method to create a Person object by birth year. 
    @classmethod
    def fromBirthYear(cls, name, year): 
        return cls(name, date.today().year - year) 
        
person1 = Person('mayank', 21) 
person2 = Person.fromBirthYear('mayank', 1996) 
    
print (person1.age) # 21
print (person2.age) # 21
```


`@classmethod` 의 특징을 얘기해보면:

- class에 bound되고 class의 object에 bound 되지는 않는다
- class 자체를 인자로 받기 때문에 class에 접근할 수 있다, object instance 아님.
- class instance에 영향을 줄 수 있는 class의 state을 수정할 수 있다. 예) class 변수를 수정하면 모든 인instance에서도 변경이 된다.

# Item 25. super로 부모 클래스를 초기화하기

예전에 child class에서 parent class 초기화하려면 바로 parent class의 `__init__` method를 호출했다. 이 방법은 간단한 hierarchy에서만 잘 작동한다.

```python
class MyBaseClass(object):
    def __init__(self, value):
        self.value = value

class MyChildClass(MyBaseClass):
    def __init__(self):
        MyBaseClass.__init__(self, 5)
```

만약에 diamond inheritance가 발생하면 원하지 않는 결과가 나올 수 있다. Diamond inheritance는 subclass(ThisWay)가 2개의 다른 class(TimessFive, PlusTwo)를 inherit 하고, 다시 이 2개의 class는 하나의 class(MyBaseClass)에서 inherit을 할 때 발생한다.  

<img src="/assets/imgs/python/effective_python_4.png"/>


```python
class TimesFive(MyBaseClass):
    def __init__(self, value):
        MyBaseClass.__init__(self, value)
        self.value *= 5

class PlusTwo(MyBaseClass):
    def __init__(self, value):
            MyBaseClass.__init__(self, value)
            self.value +=2

class ThisWay(TimesFive, PlusTwo):
    def __init__(self, value):
        TimesFive.__init__(self, value)
        PlusTwo.__init__(self, value)

foo = ThisWay(5)
print(‘Should be (5 * 5) + 2 = 27 but is’, foo.value) # 7
```


value가 7이 되는 이유는, PlustTwo.__init__에 의해서 self.value가 다시 5로 초기화됐기 때문이다. 

### Python 2

이러한 문제를 해결하기 위해서 Python 2.2에서는 `super` 라는 built-in 함수를 추가했고 `MRO`(method resolution order)를 정의했다. MRO는 어떤 superclass가 먼저 initialize되어야 하는지에 대한 기준을 정해주고, diamond 형태의 hierarchy가 있으면 superclass가 한번만 run하도록 한다.

```python
class TimesFive(MyBaseClass):
    def __init__(self, value):
        super(TimesFive, self).__init__(value)
        self.value *= 5

class PlusTwo(MyBaseClass):
    def __init__(self, value):
            super(PlussTwo, self).__init__(value)
            self.value +=2

class SuperWay(TimesFive, PlusTwo):
    def __init__(self, value):
        super(SuperWay, self).__init__(value)

foo = SuperWay(5)
print(‘Should be (5 * 5) + 2 = 27 but is’, foo.value) # 35

```

TimesFive가 먼저 inherit되는 것 처럼 보이고 그 다음에 PlusTwo가 오는 것 처럼 보여서 27이 print될 것으로 예상되지만 35가 나온다. `MRO` 가 정의된 순서가 영향을 준다.

SuperWay(5)를 호출하면 TimesFive.__init__가 호출되고, 이는 PlusTwo.__init__를 호출하고, 이는 MyBaseClasss.__init__을 호출한다. 가장 위에 도달하면 그 때부터 initialization method가 실행이 된다.

1. MyBaseClass.__init__을 통해 value에 5값이 들어온다
2. PlusTwo.__init__을 통해 value += 2가 되서 value는 7이 된다
3. TimesFive.__init__을 통해 value *= 5가 되서 value는 35가 된다.

```python
from pprint import pprint
pprint(SuperWay.mro())
"""
[<class ‘__main__.SuperWay’>,
<class ‘__main__.TimesFive’>,
<class ‘__main__.PlusTwo’>,
<class ‘__main__.MyBaseClass’>,<class ‘object’>]
"""
```

### Python3

Python3에서는 위와 같은 헷갈리는 구문들을 조금 더 확실하게 수정했다. `__class__` 변수를 사용해서 현재 class를 reference할 수 있게 했다. 그리고 self의 값을 더 자세하게 작성하도록 했다. 밑에 코드는 Python2에서는 돌아가지 않는다, 왜냐하면 `super`가 다르게 구현됐기 때문이다.


```python
class Explicit(MyBaseClass):
    def __init__(self, value):
        super(__class__, self).__init__(value * 2)

class Implicit(MyBaseClass):
    def __init__(self, value):
        super().__init__(value * 2)
```
   

# Item 26. Multiple Inheritance는 Mix-in Utility Classes에만 사용하기

Multiple inheritance는 코드를 복잡하게 만들기 때문에 피하는 것이 좋다. 그럼에도 multiple inheritance를 사용해야 한다면 `mix-in`을 사용하는 것을 고려해야 한다. `mix-in`은 작은 class여서 class가 제공해야 하는 method 몇개만 제공한다, 그리고 `__init__`을 정의하지 않아도 된다. 

```python
# binary tree를 dictionary로 표현하게 하는 class이다.
class ToDictMixin(object):
    def to_dict(self):
        return self._traverse_dict(self.__dict__)

    def _traverse_dict(self, instance_dict):
        output = {}
        for key, value in instance_dict.items():
            output[key] = self._traverse(key, value)
        return output
    
    def _traverse(self, key, value):
        if isinstance(value, ToDictMixin):
            return value.to_dict()
        elif isinstance(value, dict):
            return self._traverse_dict(value)
        elif isinstance(value, list):
            return [self._traverse(key, i) for i in value]
        elif hasattr(value, ‘__dict__’):
            return self._traverse_dict(value.__dict__)
        else:
            return value
```

 

# Item 27. Private 보다 Public Attributes 사용하기

Python에서는 2가지의 visibility가 있다: public, private. Private만 변수 이름 앞에 `__ (double underscore)`을 가지고 있다.

```python
class MyObject(object):
    def __init__(self):
        self.public_field = 5
        self.__private_field = 10

    def get_private_field(self):
        return self.__private_field

    @classmethod
    def get_private_field_of_instance(cls, instance):
        return instance.__private_field
```

public은 object에 dot operator를 사용하면 접근이 가능하다.

```python
foo = MyObject()
assert foo.public_field == 5 

foo.__private_field 
# AttributeError: ‘MyObject’ object has no attribute ‘__private_field’

```

반면에, private field들은 외부에서 접근을 할 수 없고 class내에 method에서만 접근이 가능하다. @classmethod도 private attribute에 접근 가능하다.

Inherit한 child에서 parent의 private field에 접근할 수 없다. 컴파일러가MyChildObject.get_private_field method를 보면 __private_field가 _MyChildObject**.**__private_field를 접근하게 한다. 하지만 __private_field는 그 parent class에서 정의돼있기 때문에 에러가 발생한다.

```python
class MyChildObject(MyObject):
        def get_private_field(self):
            return self.__private_field

baz = MyChildObject()
baz.get_private_field()
```

그래서 parent class에 있는 private field에 접근하기 위해서는 다른 방법으로 호출해야 한다.

```python
assert baz._MyParentObject__private_field == 71
```

여기서 한가지 의문점이 들 수 있다: 왜 private attribute 접근하는 것에 대해 rule이 그렇게 strict하지 않는가? 이에 대한 답은 Python에 모토에서 찾아볼 수 있다 "We are all consenting adults here." Python은 이렇게 public을 선언해서 접근성을 열어두는 것이 접근성을 낮추는 것보다 장점이 더 크다고 생각한다.