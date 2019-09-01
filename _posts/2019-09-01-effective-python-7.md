---
layout: post
title: " [파이썬 코딩의 기술] Concurrency and Parallelism(병행성과 병렬성) - lock, coroutines, concurrent.futures"
date: 2019-09-01 17:00:00
author: Dojin Kim
categories: Python
tags: python effective_python 파이썬코딩의기술
cover:  "/assets/imgs/python_cover.jpg"
---

Effective Python - Brett Slatkin을 읽으면서 공부 및 정리를 하며 글을 쓰고 있습니다.

# Item 38: Thread에서 Lock을 사용해서 Data Races 막기

Item 37에서 GIL이 상호 배타적이라는 것을 봤다. 하지만, 모든 상황에서 lock처럼 작동하는 것이 아니다, multiple thread로 같은 object에 접근을 하면 interruption일 발생해 문제가 생길 수 있다. 

여러 숫자들을 parallel로 세는 프로그램을 한번 작성해보자.

```python
class Counter(object):
    def __init__(self):
        self.count = 0

    def increment(self, offset):
        self.count += offset

def worker(sensor_index, how_many, counter):
    for _ in range(how_many):
        # Read from the sensor
        # …
        counter.increment(1)

def run_threads(func, how_many, counter):
    threads = []
    for i in range(5):
        args = (i, how_many, counter)
        thread = Thread(target=func, args=args)
        threads.append(thread)
        thread.start()
    for thread in threads:
        thread.join()
```

해당 프로그램은 5개의 thread를 만들어서 숫자를 세는 프로그램이다. 10^5 를 5번 세기 때문에 500000가 나올 것으로 예상했지만, 결과는 완전 달랐다.

```python
how_many = 10**5
counter = Counter()
run_threads(worker, how_many, counter)
print(‘Counter should be %d, found %d’ %(5 * how_many, counter.count))
# Counter should be 500000, found 278328
```

Python 인터프리터가 작동하는 방법 때문에 이러한 결과가 나오게 된 것이다. Python 인터프러터는 한 thread를 실행하다가 다른 thread를 번갈아가면서 실행한다. 유저는 언제 어떤 thread가 실행되는지 알 수는 없다. 

```python
counter.count += offset 
```

Counter object의 increment method에 있는 코드는 내부적으로 밑과 같이 작동한다. 이 사이에서 다른 thread로 변경이 되면 우리가 원하는 방향과 다르게 프로그램이 실행이 되는 것이다. `Data race`가 발생하는 것이다.

```python
value = getattr(counter, ‘count’)
result = value + offset
setattr(counter, ‘count’, result)
```

이러한 `data race`를 방지하기 위해서는 `Lock` class를 사용해야 한다. 이 class로 현재 값이 여러 multiple thread에 의해 동시에 접근하는 것을 방지할 수 있다. 한 thread가 한 타임에 그 값에 접근할 수 있게 된다.

프로그램을 실행시키면 우리가 원했던 결과를 얻을 수 있게 된다.

```python
class LockingCounter(object):
    def __init__(self):
        self.lock = Lock()
        self.count = 0

    def increment(self, offset):
        with self.lock:
            self.count += offset

counter = LockingCounter()
run_threads(worker, how_many, counter)
print(‘Counter should be %d, found %d’ %(5 * how_many, counter.count))
# Counter should be 500000, found 500000
```

# Item 40: 많은 함수를 동시에 실행할 때 Coroutines 고려하기

Thread는 여러 함수들을 동시에 실행 가능하게 하지만 큰 문제점들이 있다:

- 안전하게 작동되기 위해 추가적인 tool들이 필요하다 (lock, queue 등). 이러한 코드는 single-thread를 사용하는 것보다 확장 및 유지하기 어렵게 만든다
- Thread는 많은 메모리를 요구로 한다, 각 실행중이 thread마다 약 8MB. 프로그램이 작으면 현대 컴퓨터에서 이 정도는 무리가 없지만, 몇 천개의 함수들을 동시에 실행시키려면 문제가 발생한다.
- Thread의 overhead가 크다.

그래서 이러한 점을 극복하기 위해 Python에서는 `coroutines`를 사용할 수 있다. 이는 generator에서 확장되서 구현되었다.  `Coroutines`는 비활성화되기 전까지 약 1KB의 적은 메모리를 사용한다.

`Coroutines`는 `yield` expression 이후에 `send`로 value를 generator에 보낸다.

```python
def my_coroutine():
    while True:
        received = yield
        print(‘Received:’, received)

it = my_coroutine()
next(it) # Prime the coroutine
it.send(‘First’)
it.send(‘Second’)

# Received: First
# Received: Second

def minimize():
    current = yield
    while True:
        value = yield current
        current = min(value, current)

it = minimize()
next(it) # Prime the coroutine
print(it.send(10))
print(it.send(4))
print(it.send(22))
print(it.send(-1))

# 10
# 4
# 4
# -1
```

맨 처음에 `next` 라는 call이 필요하고 이는 generator로 하여금 첫 `send`를 받을 수 있게 준비시킨다. Generator 함수는 `send` 로 call되면 무한대로 실행될 것이다. 이처럼 `Coroutines` 도 thread처럼 독립적인 함수이다. 한가지 차이는 `coroutine`은 `yield` expression에서 멈추고 `send`가 외부에서 call 될때만 재게된다.

# Item 41: concurrent.futures를 사용해서 parallelism 구현하기

이전에도 언급했듯이 GIL때문에 Python의 thread로는 parallelism을 실현시킬 수가 없다. 

하지만, `concurrent.futures` built-in 모듈을 사용하면 여러 CPU core를 parallel로 사용할 수 있다. 이 모듈은 추가적인 인터프리터들을 child process처럼 CPU core에 실행시킴으로 parallel하게 프로그램을 실행한다. 이 child process들은 메인 인터프리터에서 분리되었기 때문에 각 process마다 자신의 GIL을 가지고 있고, 그렇기에 각 child는 하나의 CPU core를 온전히 사용할 수 있다. Child들은 메인 process에 연결 돼있어서 instruction을 받고 연산을 하고 결과값을 리턴한다.

예로, 최대공배수를 구하는 프로그램을 작성해보자. 밑은 아직 parallel하지 않게 구현한 프로그램이다. 큰 숫자들의 최대공배수를 순차적으로 구하기 때문에 꽤나 많은 시간이 걸렸다.

```python
def gcd(pair):
    a, b = pair
    low = min(a, b)
    for i in range(low, 0, -1):
        if a % i == 0 and b % i == 0:
            return i


numbers = [(1963309, 2265973), (2030677, 3814172),(1551645, 2229620), (2039045, 2020802)]
start = time()
results = list(map(gcd, numbers))
end = time()
print(‘Took %.3f seconds’ % (end - start))

# Took 1.170 seconds
```

GIL이 CPU core를 parallel하게 사용하지 못하게 하기 때문에 Python multi-thread를 사용하더라도 속도가 향상되지는 않을 것이다. 이제 `concurrent.futures` 모듈의 `ThreadPoolExecutor` class와 두 개의 worker thread를 사용해서 parallel한 프로그램을 구현해보자.

```python
start = time()
pool = ThreadPoolExecutor(max_workers=2)
results = list(pool.map(gcd, numbers))
end = time()
print(‘Took %.3f seconds’ % (end - start))

# Took 1.199 seconds
```

parallel하게 바꿨음에도 속도가 향상되지 않았는데, 이는 처음에 시작하고 thread의 pool들간 communication 오버헤드가 발생하기 때문이다. 속도를 향상시키기 위해 `ThreadPoolExecutor`를  `ProcessPoolExecutor` 변경해보자

```python
start = time()
pool = ProcessPoolExecutor(max_workers=2)
results = list(pool.map(gcd, numbers))
end = time()
print(‘Took %.3f seconds’ % (end - start))

# Took 0.663 seconds
```

Dual-core 머신에서 코드를 실행시켰을 때 속도가 상당히 향상됐음을 볼 수 있다. 한번 `ProcessPoolExecutor` class가 무엇을 하기에 이러한 현상이 일어나닌즈 보려고 한다.

1. 처음에 `numbers` 의 item들을 `map`으로 가져간다.
2. `pickle` 모듈을 사용해서 binary data로 serialize(직렬화)한다. 
3. 메인 인터프리터에서 serialized data를 복사해서 child 인터프리터 process에 로컬 소켓을 이용해서 전달한다.
4. child process내에서 `pickle` 모듈을 다시 사용해서 Python object로 deserialize를 한다.
5. gcd 함수를 가진 Python 모듈을 import 한다.
6. child process에서 parallel하게 해당 함수를 실행시킨다
7. 결과값을 bytes로 serialize한다.
8. bytes들을 복사하고 소켓을 통해서 리턴한다.
9. parent process에서 bytes들을 Python object로 deserialize한다.
10. 마지막으로, 결과값들을 합쳐서 하나의 list를 리턴한다.

parent process와 child process에 data를 전달할 때 serialize와 deserialize 작업을 반복하기 때문에 어느정도의 오버헤드가 발생한다. 

이 scheme은 서로 독립된 작업들을 parallel하게 실행시키고 싶을 때, 그리고 적은 data를 전달해서 많은 양의 연산이 필요한 상황에서 유용하다. 만약 프로그램이 이러한 특징들을 지니지 않는다면 `multiprocessing`의 오버헤드가 더 커서 parallel하게 프로그램을 작성하는 것이 속도를 크게 향상시켜주지 못할 수 있다.