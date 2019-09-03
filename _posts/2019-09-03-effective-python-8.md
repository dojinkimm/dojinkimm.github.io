---
layout: post
title: "[파이썬 코딩의 기술] Built-in 모듈 - wraps, with, copyreg"
date: 2019-09-03 17:00:00
author: Dojin Kim
categories: Python
tags: python effective_python 파이썬코딩의기술
cover:  "/assets/imgs/python_cover.jpg"
---
> Effective Python - Brett Slatkin을 읽으면서 공부 및 정리를 하며 글을 쓰고 있습니다.

# Item 42: functools.wraps로 함수 decorator 정의하기

Python은 감싸고 있는 함수 이전 혹은 그 이후에 특정한 코드를 실행시킬 수 있는 `decorator`라는 특별한 문법을 가지고 있다. 이 문법으로 입력 argument나 리턴 값을 수정할 수 있다, 디버깅할 때 유용하게 사용될 수 있다. 예를 들어, argument와 리턴 값을 프린트해서 진행상황을 보고 싶을 때 사용하기에 적절하다. 특히, 재귀함수가 어떻게 stack으로 쌓여가는지 볼 수 있게 된다.

`@` 를 추가해서 현재 함수에 `decorator`를 호출할 수 있게 된다. fibonacci 함수가 실행되기 전과 후에 wrapper를 실행시킨다.

```python
def trace(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        result = func(*args, **kwargs)
        print(‘%s(%r, %r) -> %r’ %(func.__name__, args, kwargs, result))
        return result
    return wrapper

@trace
def fibonacci(n):
    “““Return the n-th Fibonacci number”””
    if n in (0, 1):
        return n
    return (fibonacci(n - 2) + fibonacci(n - 1))

fibonacci = trace(fibonacci)
fibonacci(3)

# fibonacci((1,), {}) -> 1
# fibonacci((0,), {}) -> 0
# fibonacci((1,), {}) -> 1
# fibonacci((2,), {}) -> 1
# fibonacci((3,), {}) -> 2
```

# Item 43: 재사용 가능한 try/finally 동작을 만들기 위해 contextlib 과 with 문 고려하기

`with` 문은 Python에서 코드가 특별한 context안에서 실행된다는 것을 의미한다. Item38에서 lock을 사용할 때 `with`문을 사용했었는데, 이 때 해당 문법 밑에 코드는 lock가 있을 때만 실행됨을 의미한다.

```python
lock = Lock()
with lock:
    print('Lock is held')
```

`with`에 해당하는 문법을 `try/finally` 로 똑같이 변환할 수 있다.

```python
lock.acquire()
try:
    print('Lock is held')
finally:
    lock.release()
```

하지만, `with`문을 사용한는 것이 더 간결하다. 

 

`with`문을 더 잘 사용하기 위해서는 `contextlib` built-in 모듈을 사용할 수 있다. 이 모듈은 `contextmanager`라는 decorator를 지니고 있어서 간단한 함수가 `with`문에 사용될 수 있게 한다. 

debug logging을 하는 코드가 있다고 가정해보자. 프로그램의 기본 log level은 WARNING이다, 그래서 함수를 실행했을 때 error 메세지만 프린트 된다.

```python
def my_function():
    logging.debug('Some debug data')
    logging.error('Error log here')
    logging.debug('More debug data')

my_function()
# Error log here
```

context manager를 사용해서 일시적으로 log level을 변경할 수 있다. 이 helper함수는 `with` 블록에서 사용될 때와 아닐 때 다른 logging severity level을 다르게 한다.

```python
def debug_logging(level):
    logger = logging.getLogger()
    old_level = logger.getEffectiveLevel()
    logger.setLevel(level)
    try:
        yield
    finally:
        logger.setLevel(old_level)
```

`yield`는 `with`블록이 실행될 포인트이다. 밑에 코드를 실행시켰을 때 `with` 블록 안에서 함수를 호출했을 때는 모든 debug정보들이 프린트 됌을 알 수 있다. 이처럼 `with`안에 있을 때는 특정한 context로 실행이 되도록것이다.

```python
with debug_logging(logging.DEBUG):
    print(‘Inside:’)
    my_function()
print(‘After:’)
my_function()

# Inside:
# Some debug data
# Error log here
# More debug data
# After:
# Error log here
```

### `with` Target  사용하기

`with`문에 전달된 context manager는 object를 리턴할 때도 있다. 이 object를 `as`로 특정한 로컬 변수에 지정할 수 있다.

`open`의 리턴 object가 `with`문 내에서만 사용할 수 있는 로컬변수 handle에 저장이 되고 사용할 수 있다.

```python
with open(‘/tmp/my_output.txt’, ‘w’) as handle:
    handle.write(‘This is some data!’)
```

# Item 44: copyreg로 pickle을 신뢰할 수 있게 만들기

### Note

Python의 `pickle`모듈은 사실 디자인적으로 안전하지 않다. Serialized data는 다시 Python object로 어떻게 만들지에 대한 정보를 가지고 있다. 그래서 잘못된 정보를 포함시키게 만들면 deserialize해서 Python object를 만들 때 잘못된 Python object가 만들어질 수 있다. 반면에, `json`모듈의 디자인은 안전하다. 

`pickle`은 Python object를 bytes stream으로 serialize(직렬화)하고 다시 butes를 object로 deserialize하게 해주는 built-in 모듈이다. 

게임에서 플레이어 진행상황을 알려주는 코드를 작성해보자. 게임을 진행하면서 상태가 바뀌고 게임을 종료할 때 현재 상태를 파일에 `pickle`모듈로 저장한다. `pickle`모듈의 `dupm`로 GameState object를 파일에 바로 저장할 수 있게 된다. 그리고 `load`로 저장된 파일을 불러올 수 있다.

```python
class GameState(object):
    def __init__(self):
        self.level = 0
        self.lives = 4

state = GameState()
state.level += 1 
state.lives -= 1 

# 파일 저장
state_path = '/tmp/game_state.bin'
with open(state_path, ‘wb’) as f:
    pickle.dump(state, f) 

# 파일 불러오기
with open(state_path, ‘rb’) as f:
    state_after = pickle.load(f)
print(state_after.__dict__)

# {‘lives’: 3, ‘level’: 1}
```

이 게임에서 플레이어는 고득점을 목표로 한다고 가정해보자. 그러기 위해서는 플레이어들의 점수를 알아야 하기에 GameState class에 새로운 field를 추가해준다. 새로운 class에다 `dump`를 하고 `load`를 다시하면 이전에 있던 정보가 없어진 것을 볼 수 있다.

그래서 이전 object의 내용을 path로 불러오면 제대로 불러와진 것을 볼 수 있다, 하지만, 새로 추가된 points라는 attribute는 사라졌다. 

```python
class GameState(object):
    def __init__(self):
        self.level = 0
        self.lives = 4
        self.points = 0 # 추가된 부분

state = GameState()
serialized = pickle.dumps(state)
state_after = pickle.loads(serialized)
print(state_after.__dict__)

# {‘lives’: 4, ‘level’: 0, ‘points’: 0}

with open(state_path, ‘rb’) as f:
    state_after = pickle.load(f)
print(state_after.__dict__)

# {‘lives’: 3, ‘level’: 1}
```

이러한 문제를 해결하기 위해서는 `copyreg` built-in 모듈을 사용해야 한다.

처음에 default value들을 constructor에 추가해준다. `unpickle_game_state`함수는 serialized data와 parameter를 `pickle_game_state`에서 받고 이에 해당하는 GameState object를 리턴한다. constructor를 감싸는 wrapper정도라고 생각하면 된다.

```python
class GameState(object):
    def __init__(self, level=0, lives=4, points=0):
        self.level = level
        self.lives = lives
        self.points = points

    def pickle_game_state(game_state):
        kwargs = game_state.__dict__
        return unpickle_game_state, (kwargs,)

    def unpickle_game_state(kwargs):
        return GameState(**kwargs)
```

그 다음에 `copyreg`모듈을 register한다. 그러고 이전에 수행했던 작업을 해보면 원하는 만큼 points의 값이 올라간 걸 알 수 있다. 이제 GameState에 새로운 field를 추가해본다.

```python
copyreg.pickle(GameState, pickle_game_state)

state = GameState()
state.points += 1000
serialized = pickle.dumps(state)
state_after = pickle.loads(serialized)
print(state_after.__dict__)

# {‘lives’: 4, ‘level’: 0, ‘points’: 1000}
```

magic이라는 값을 추가해본다. 추가하고 나서 `pickle`로 load를 하면 magic attribute가 프린트됨을 볼 수 있다. 옛 GameState를 deserialize해도 바뀐 attribute가 있음을 알 수 있다. 이렇게 되는 이유는 `unpickle_game_state`가 GameState constructor를 바로 호출하기 때문이다. parameter가 비어있으면 defalut value를 바로 가져온다, 그래서 이전 game state 파일에 magic이 없었음에도 deserialize했을 때 magic의 default 값을 가져온 것이다. 

```python
class GameState(object):
    def __init__(self, level=0, lives=4, points=0, magic=5):
            # …


state_after = pickle.loads(serialized)
print(state_after.__dict__)

# {‘level’: 0, ‘points’: 1000, ‘magic’: 5, ‘lives’: 4}
```

### Versoning Classes


위 예시와 반대로 field를 제거해야될 때도 있다. GameState에서 lives field가 제거되었다고 가정해보고 어떤 결과가 나오는지 살펴보자

```python
class GameState(object):
    def __init__(self, level=0, points=0, magic=5):
            # …

pickle.loads(serialized)

# TypeError: __init__() got an unexpected keyword argument ‘lives’
```

field를 제거하면 TypeError가 발생한다. 이 에러를 방지하기 위해서는 `copyreg`에 버전 parameter를 추가하는 것이다. 이전 버전에는 version argument가 없었기 때문에 GameState가 constructor로 전달된다. 그러고 나면 이전 object를 deserialize하는 작업은 작동이 되게 된다.

```python
...
    def pickle_game_state(game_state):
        kwargs = game_state.__dict__
        kwargs['version'] = 2
        return unpickle_game_state, (kwargs,)

    def unpickle_game_state(kwargs):
        version = kwargs.pop('version', 1)
        if version == 1:
            kwargs.pop('lives')
        return GameState(**kwargs)



copyreg.pickle(GameState, pickle_game_state)
state_after = pickle.loads(serialized)
print(state_after.__dict__)

# {‘magic’: 5, ‘level’: 0, ‘points’: 1000}
```