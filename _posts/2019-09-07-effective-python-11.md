---
layout: post
title: "[파이썬 코딩의 기술] Production - module-scoped, repr, unittest"
date: 2019-09-07 17:00:00
author: Dojin Kim
categories: Python
tags: python effective_python 파이썬코딩의기술
cover:  "/assets/imgs/python_cover.jpg"
---

> Effective Python - Brett Slatkin을 읽으면서 공부 및 정리를 하며 글을 쓰고 있습니다.

# Item 54: 배포 환경 구성하는 데 Module-Scoped 코드 고려하기

배포 환경이란, 프로그램이 실행되는 configuration(구성)을 의미한다. 모든 프로그램이 적어도 하나의 배포 환경을 가지고 있다 - product environment. 다만, product environment(환경)의 configuration은 내가 개발하고 있는 개발 환경의 것과 다를 수 있다는 것을 알아야 한다. 

`pyvenv` 같은 툴은 모든 환경들에 같은 Python 패키지가 설치 될 수 있게 한다. 문제가 하나 있다면 그건 바로 product 환경에는 개발 환경에서는 없는 외부적인 요인들이 필요할 때가 있다는 것이다. 예로, 내 프로그램을 웹 서버 컨테이너에 실행을 시키고 DB에 접근이 가능하게 만들고 싶다고 생각해보자. 이는 프로그램 코드를 변경할 때 마다, 서버 컨테이너를 실행해야 하고, DB가 올바르게 작동하고, 접근을 위한 패스워드가 필요하다는 것을 의미한다. 코드에서 줄 하나만 바꾸는데 이 모든 것이 이뤄져야 한다면 비용이 너무 크다.

그래서 가장 좋은 방법은 개발 환경에 따라서 다른 기능들을 제공하는 것이다. 예를 들어, 하나는 production용 하나는 개발용으로 두 개의 \__main__파일을 가질 수 있다.

```python
# dev_main.py
TESTING = True
import db_connection
db = db_connection.Database()

# prod_main.py
TESTING = False
import db_connection
db = db_connection.Database()
```

이 두 파일의 차이점은 `TESTING`이라는 상수의 값이다. 다른 module들이 \__main__파일을 import해서 `TESTING`의 값에 따라서 다르게 작동하게 할 수 있다.

```python
# db_connection.py
import __main__

class TestingDatabase(object):
    # …

class RealDatabase(object):
    # …

if __main__.TESTING:
    Database = TestingDatabase
else:
    Database = RealDatabase
```


여기서 주목해야 할 것은 바로 코드가 module scope(범위)에서 실행되다는 것이다.  이렇게 하면 db 구성과 같은 비용이 드는 과정들을 개발 환경에서 매번 거쳐야할 필요가 없는 것이다.

---

하지만, 개발 환경이 복잡해질수록 `TESTING`과 같은 상수들을 제거하고 `configparser`와 같은 built-in module을 사용하는 것이 낫다. 

---

# Item 55: Output 디버깅 하는 데 repr 문자열 사용하기

주로 Python에서 디버깅을 할 때 `print` 함수를 사용하는 경우가 많다. `print`함수는 사람이 읽기 편하게 모든 객체들이나 문자열들의 값들을 보여줘서 디버깅을 할 때 편하다. 하지만, 이 함수이 문제는 값의 자료형을 정확히 알기 어렵다는 것이다.

밑에 코드처럼, 숫자나 문자 모두 같은 값을 출력한다. 

```python
print(5)
print('5')
# 5
# 5
```

디버깅을 할 때는 값들의 자료형을 구분하는 것도 매우 중요한 일이다. 그렇기 때문에 디버깅을 할 때는 object의 `repr` 버전을 봐야 한다. `repr`는 object를 printable representation으로 리턴하는 buint-in 함수이다. 

```python
a = '\x07'
print(repr(a))

# ‘\x07’
```

`print`로 디버깅할 때 `repr`를 사용해서 자료형을 확실하게 구분할 수 있다. 자료형의 구분을 확실히 해야할 때는 디버깅할 때 `repr` 사용을 고려해야 한다.

```python
print(repr(5))
print(repr('5'))

# 5 
# ‘5’
```

# Item 56: unittest로 모든 것 테스트 하기

Python은 정적 타입 체크를 하지 않는다. 컴파일러는 프로그램을 실행시켰을 때 프로그램이 정상적으로 작동할 것이라는 보장을 해주지 않는다. Python에서는 프로그램이 호출하는 함수가 런타임때 정의될 것인지 알 수가 없다. 이러한 다이나믹함은 좋은 점도 있지만 단점도 있다.

이러한 다이나믹의 장점을 다 사용하고 production으로 배포했을 때 문제를 줄이기 위해서는 **`test`**를 하는 것이 **매우 중요하다.** Test는 코드의 보험과도 같은 존재이다. 리팩토링을 하거나 코드를 확장할 때 test는 어떤 변화가 있었는지 알기 쉽게 만들어준다. 

Test를 가장 쉽게 할 수 있는 방법은 `unittest`라는 built-in module을 사용하는 것이다.

이 코드는 test를 정의하고 있다. 

```python
# utils.py
def to_str(data):
    if isinstance(data, str):
        return data
    elif isinstance(data, bytes):
        return data.decode('utf-8')
    else:
        raise TypeError('Must supply str or bytes, ''found: %r' % data)
```

이제 이 test 코드를 사용하기 위해 다른 코드를 작성해준다. 

```python
# utils_test.py
from unittest import TestCase, main
from utils import to_str

class UtilsTestCase(TestCase):
    def test_to_str_bytes(self):
        self.assertEqual('hello', to_str(b'hello'))

    def test_to_str_str(self):
        self.assertEqual('hello', to_str('hello'))

    def test_to_str_bad(self):
        self.assertRaises(TypeError, to_str, object())

if __name__ == ‘__main__’:
    main()
```

`TestCase` class는 test가 제대로 이뤄졌는지 확인을 하는 helper method들을 제공한다: `asssertEqual`은 결과 값이 같은지 확인을 하고, `assertTrue`는 Boolean 문법을 확인하고, `assertRaises`는 예외사항이 발생했는지 확인을 한다. `TestCase` class에 새로운 helper method를 정의할 수 있다, 다만, method이름이 `test`라는 단어로 시작하지 않으면 된다.
