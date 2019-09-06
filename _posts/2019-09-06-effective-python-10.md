---
layout: post
title: "[파이썬 코딩의 기술] Collaboration - docstring, package, root exception, circular dependency"
date: 2019-09-06 17:00:00
author: Dojin Kim
categories: Python
tags: python effective_python 파이썬코딩의기술
cover:  "/assets/imgs/python_cover.jpg"
---

> Effective Python - Brett Slatkin을 읽으면서 공부 및 정리를 하며 글을 쓰고 있습니다.

# Item 49: 모든 함수, 클래스, 모듈에 Docstring 작성하기

Python이 다이나믹하기 때문에 documentation하는 것이 매우 중요하다. Python에서는 프로그램 소스 코드의 documentation을 프로그램 실행시키면서 접근할 수 있다는 것에서 다른 언어와 다르다.

Python은 `docstring`이라는 것을 제공해서 `__doc__`로 접근할 수 있게 한다.

```python
def palindrome(word):
    """Return True if the given word is a palindrome."""
    return word == word[::-1]

print(repr(palindrome.__doc__))

# 'Return True if the given word is palindrome.'
```

이러한 `__doc__` 덕분에:

1. documentation에 접근할 수 있게 되어서 개발이 쉬워진다. 프로그래머는 사용하고 있는 함수, 클래스, 모듈 등의 목적 및 기능을 알 수 있기 때문이다. 
2. HTML처럼 document화하기 좋은 텍스트 형식으로 `Sphinx`나 `Read the Docs`로 쉽게 변환할 수 있다.

### Module document화하기

Module의 documentation은 소스 파일의 가장 위에 위치하고 """ ... """ 이 안에 글을 작성한다. documentation의 목적은 module과 그 내용을 소개하는 것이다.

제일 첫 문장에는 module의 목적을 나타낸다. 그 밑에는 유저가 module을 사용하기 위해 알아야할 디테일들을 작성한다.

```python
"""Library for testing words for various linguistic patterns.

Testing how words relate to each other can be tricky sometimes!This module provides easy ways to determine when words you’vefound have special properties.

Available functions:
    - palindrome: Determine if a word is a palindrome.
    - check_anagram: Determine if two words are anagrams.
    … 
"""
```

**Class와 function도 module의 docstring방법을 따른다.**

# Item 50: 모듈을 구성하고 안정적인 API제공하기 위해 패키지 사용하기

프로그램의 코드가 커질 수록 구조를 재정리할 필요가 항상 생긴다. module이 너무 많아져서 정리가 안될때 Python의 `packages`를 사용하면 된다. `Packages`는 module들을 담은 module이라고 보면 된다.

보통, 디렉토리에 \__init__.py라는 비어있는 파일이 있으면 `packages`로 간주 된다. \__init__.py가 있으면 그 디렉토리 안에 있는 파일들은 디렉토리에 상대적 경로로 사용 가능하다. 예를들어, 밑과 같은 디렉토리 구조를 가진 `packages`가 있다고 생각해보자

    main.py
    mypackage/__init__.py
    mypackage/models.py
    mypackage/utils.py

이 `packages`에서 `utils`라는 module을 import하고 싶으면 먼저 패키지 명을 작성하고 그 안에서 module을 import하면 된다.

```python
from mainpackage import utils
```

### Namespaces

다른 패키지이지만 같은 module명이 있더라도 그 안에 있는 함수나 다른 module의 이름이 다르다면 절대경로를 보여줘서 conflict없이 module을 import할 수 있다.

```python
from analysis.utils import log_base2_bucket
from frontend.utils import stringify

bucket = stringify(log_base2_bucket(33))
```

다만, 제일 마지막에 import하는 module 혹은 function의 이름이 같다면 더 밑에 import된 module이나 function이 사용된다

```python
from analysis.utils import inspect
from frontend.utils import inspect # Overwrites!
```

이러한 문제점을 해결하기 위해서는 `as`라는 명령어를 작성해서 이름을 다시 만들어서 지정해주면 된다.

```python
from analysis.utils import inspect as analysis_inspect
from frontend.utils import inspect as frontend_inspect
```

# Item 51: Root Exception 정의해서 API로부터 Callers(호출자) 보호하기

Python module API를 정의 할 때 root `Exception`을 정의하는 것이 더 강력하다. Module에서 일어나는 모든 다른 exception들은 이 root exception을 상속받게 된다.

root exception이 있으면 API를 사용하는 유저가 어떤 에러가 어떤 상황에 일어났는지 볼 수 있고 일부로 상황을 만들어서 특정한 에러가 나오도록 유도할 수도 있다. 

```python
class Error(Exception):
    """Base-class for all exceptions raised by this module."""

class InvalidDensityError(Error):
    """There was a problem with a provided density value."""
```
    

밑의 예시처럼 root exception을 사용하면 유저가 언제 어떤 API를 사용했을 때 문제가 발생하는지 더 자세하게 알 수 있다. 

또한, API module 코드의 버그를 찾는데 도움을 준다. 만약 코드가 module의 hierarchy대로 예외사항들을 일으킬 때, 그 외에 일어나는 모든 예외사항들은 프로그래머가 의도하지 않는 예외사항들이 되는 것이다. 그리고 이 예외사항들은 API코드의 버그가 있음을 의미한다.

```python
try:
    weight = my_module.determine_weight(1, -1)
except my_module.Error as e:
    logging.error(‘Unexpected error: %s’, e)
```

# Item 52: Circular Dependencies 없애는 방법 알기

협업을 하다보면 상호의존적인 module들이 생기는 경우가 발생한다. 예를 들어, 어디에 document를 저장하는지 보여주는 dialog 박스 GUI 어플리케이션이 있다고 생각해보자. Dialog로 보여지는 data는 argument로 구체화할 수 있고 글로벌 state를 알아야 할 필요도 있다. 

밑 코드는 default document 저장 장소를 글로벌로 보여주는 dialog 코드이다.

```python
# dialog.py
import app

class Dialog(object):
    def __init__(self, save_dir):
        self.save_dir = save_dir
    # …

save_dialog = Dialog(app.prefs.get(‘save_dir’))

def show():
    # …
```

여기서의 문제는 `app` module이 `prefs` object를 가지고 있고 똑같이 `dialog` classs를 import한다는 것이다. 

이러한 상황은 circular(순환적) dependency가 있는 상황이다. `app` module을 메인 프로그램에서 사용하려고 할 때 exception이 생기게 된다.


```python
# app.py
import dialog

class Prefs(object):
    # …
    def get(self, name):
        # …

prefs = Prefs()
dialog.show()
```


문제를 더 잘 이해하기 위해서 Python의 import에 대해서 더 살펴보도록 하자. Module이 import 되면 Python은 다음과 같은 순서로 진행이 된다:

1. `sys.path`에서 module을 찾는다
2. module에서 코드를 불러오고 컴파일 돼있음을 확실히 한다
3. 이에 대응되는 비어있는 module object를 생성한다
4. module을 `sys.modules`에 삽입한다
5. module object에 코드를 실행시키고 내용을 정의 한다.

circular dependency의 문제점은 module의 attribute들은 실행이 되기 전 까지는 정의가 되지 않는 다는 것이다 (step 5 이후). 하지만, module은 `import`문으로 바로 `sys.modules`에 삽입된다 (step 4 이후). 

이러한 문제에 대한 해결책은 코드를 리팩토링해서 `prefs` 데이터 구조가 dependency tree에 맨 밑에 오게 하는 것이다. 그렇다면, `app`과 `dialog` 둘다 같은 유틸리티 module을 import할 수 있게 되고 circular dependency를 피할 수 있게 되는 것이다. 

Circular dependecy를 깨트리는 방법은 총 3가지가 있다.

### 1. Reordering Imports

import의 순서를 변경하는 것이다. 예를 들어, `dialog` module import를 `app` module 밑에 둬서, 내용들이 먼저 실행이 되게 하면 에러는 사라지게 된다.


```python
# app.py
class Prefs(object):
    # …

prefs = Prefs()

import dialog # Moved
dialog.show() 
```


이 방법이 잘 작동되는 이윤는 `dialog` module이 늦게 불러와지기 때문에 prefs는 이미 `app.prefs`로 간주되기 때문이다. 에러는 발생하지 않지만 이 방법은 PEP 8 스타일 가이드에 어긋난다. 가이드대로 하면 import문은 항상 소스 코드 맨 위에 있어야하기 때문ㄴ이다.

### 2. Import, Configure, Run

`configure`를 사용해서 모든 module들이 다 import 된 다음에 코드가 실행이 되게 하는 것이다. `configure`로 다 import 됐다는 것은 모든 attribute들이 정의됐다는 의미이기도 하다.

그래서 이 방법대로 코드를 리팩토링하면 다음과 같다.

```python
# dialog.py
import app

class Dialog(object):
    # …

save_dialog = Dialog()

def show():
    # …

def configure():
    save_dialog.save_dir = app.prefs.get(‘save_dir’)
```



```python
# app.py
import dialog

class Prefs(object):
    # …

prefs = Prefs()

def configure():
    # …
```

마지막으로, main module에서 다 import를 먼저 하고 configure 다하고 나서 실행을 시킨다.

```python
# main.py
import app
import dialog

app.configure()
dialog.configure()

dialog.show()
```

이 방법도 circular dependency 문제를 해결하지만, 코드의 구조를 바꾸고 `configure` 스텝을 넣는 것이 쉽지 않다.

### 3. Dynamic Import

마지막, 그리고 가장 간단한 해결책은 import문을 함수나 method내에 사용하는 것이다. 이러한 방법을 `dynamic import`라고 부르는데, module import가 프로그램 첫 시작이 아니라 실행 도중 일어나기 때문이다. 

`dialog` module과 `app` module을 재정의하면 다음과 같다.

```python
# dialog.py
import app

class Dialog(object):
    # …

save_dialog = Dialog()

def show():
    import app # Dynamic import
    save_dialog.save_dir = app.prefs.get(‘save_dir’)
    # …
```


```python
# app.py
import dialog

class Prefs(object):
    # …

prefs = Prefs()
dialog.show()
```

s
이 방법은 구조적으로 변화가 많이 필요 없기 때문에 매우 간단하기 때문에 circular dependency가 발생하면 이 방법을 사용하는 것이 추천된다. 다만, 처음부터 이러한 코드를 작성하지 않는 것이 낫다.