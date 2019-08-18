---
layout: post
title: "[Effective Python] Pythonic thinking - style guide, bytes str, list comprehension"
date: 2019-08-18 17:00:00
author: Dojin Kim
categories: Python
tags: python effective_python 
cover:  "/assets/instacode.png"
---

> Effective Python - Brett Slatkin을 읽으면서 공부 및 정리를 하며 글을 쓰고 있습니다.

# 1. Style Guide

Python Enhancement Proposal #8, 짧게 줄여서 [PEP 8](https://www.python.org/dev/peps/pep-0008/), 라는 것이 있는데 이는 Python 코드를 작성하는 스타일 가이드를 말한다.

문법만 맞으면 되는거 아닌가? 라는 생각을 할 수 있다. 하지만, 여려 사람들이랑 협업해서 하는 큰 프로젝트에서는 나만 알아보는 코드를 작성하기보다 모두가 알아들을 수 있는 코드를 작성하는 것이 필요하다. 이러한 경우에 하나의 통일된 스타일을 가지고 코딩을 하면 모두가 이해하기 쉬울 것이다. 그렇기 때문에, 스타일 가이드에 맞춰서 코딩을 하는 것이 중요하다.

스타일 가이드는 whitespace 몇개, tab 몇번 등 엄청 세부적으로 가이드를 제공한다. 하지만 그 중에서 제일 헷갈려하는 걸 짚어보려고 한다.

### Naming

- 함수, 변수, 속성의 이름은 lowercase_underscore 형식이어야 한다.

```python
def multiply_ten(a):
    return a*10

school_name = "H"
print(multiply_ten(10))
```


- Protected 인스턴스 속성은 _leading_underscore 형식이어야 한다.
- Private 인스턴스 속성은 __double_leading_underscore 형식이어야 한다.

```python
class Calculator:
    _protected = 10
    __pi = 3.14
...
```

- Class나 Exceptions는 CapitalizedWord 형식이어야 한다.

```python
try:
    ...
except ValueError:
    ...
```

- Module-level의 constant들은 ALL_CAPS 형식이어야 한다.

```python
# opencv
import cv2
cv2.CV_CAP_PROP_FPS 
```

### Expressions & Statements

- 비어있는 value 확인하기 위해서는 `if len(a)==0` 이런 식이 아니라 ⇒ `if not a` 라는 식으로 작성한다.
- 비어있지 않는 value 확인할 때도 비슷하다.
- `import ...` 는 항상 파일 맨 위에 위치한다.

# 2. bytes, str 차이

Python3 에서 

- str는 내부적으로 `unicode` character를 갖고 있다.
- bytes는 `raw 8-bit` 값을 갖고 있다

unicode를 binary로 바꾸려면 `encode` method를 사용해야 하고, binary를 unicode로 바꾸려면 `decode` method를 사용해야 한다.

```python
# binary를 unicode로 변환하는 method
def to_str(bytes_or_str):
    if isinstance(bytes_or_str, bytes):
        value = bytes_or_str.decode(‘utf-8’)
    else:
        value = bytes_or_str
    return value # Instance of str


# unicode를 binary로 변환하는 method
def to_bytes(bytes_or_str):
    if isinstance(bytes_or_str, str):
        value = bytes_or_str.encode(‘utf-8’)
    else:
        value = bytes_or_str
    return value # Instance of bytes
```

 

Python3에서는 파일을 읽고 쓸 때 default로 utf-8 encoding을 사용한다. 그래서 binary파일을 바로 읽을 수 없다. 만약 binary 파일을 읽고 싶으면 `rb`, 쓰고 싶으면 `wb` 로 명시를 해줘야 한다.

```python
with open(‘/tmp/random.bin’, ‘w’) as f:
    f.write(os.urandom(10))

with open(‘/tmp/random.bin’, ‘wb’) as f:
    f.write(os.urandom(10))
```

# 3. List Comprehension

map, filter를 사용하는 것보다 list comprhension을 사용하는 것을 추천한다. List comprehension은 한 눈에 보기가 쉽기 때문이다.

```python
even_squares = [x**2 for x in a if x % 2 == 0]
print(even_squares)
```

하지만, 중첩으로 여러개의 list comprehension을 사용하는 것은 추천되지 않는다. List comprehension은 긴 코드를 줄여주는 효과를 내지만, 중첩이 길어지면 기존의 for loop을 중첩하는 것과 크게 다르지 않기 때문이다.

```python
# list comprehension
flat =[x for sublist1 in my_lists
    for sublist2 in sublist1
    for x in sublist2]

# for 중첩
flat = []
for sublist1 in my_lists:
    for sublist2 in sublist1:
        flat.extend(sublist2)
```

또한, list comprehension이 매우 크다면 `Generator` 사용을 고려해야 한다. Input이 매우 크면 시간도 매우 오래 걸릴뿐더러 메모리도 많이 잡아먹기 때문이다. Generator는 전체 output을 한번에 연산을 하는 것이 아니라 iterator로 해당하는 expression에 도달하면 item을 하나씩 yield를 한다.

Generator는 list comprehension처럼 생긴 문법을 [] 가 아닌 ()에 두면 된다. 다만 generator를 바로 print하면 generator의 object이 반환될 것이다. 그래서 generator를 iterate하는 것이 중요하다. Iterate하면 iterate 할 때마다 연산을 하게 된다.

```python
it = (len(x) for x in open(‘/tmp/my_file.txt’))
print(it) 
# <generator object <genexpr> at 0x101b81480>

print(next(it))
print(next(it))
```