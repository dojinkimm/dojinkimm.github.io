---
layout: post
title: " [파이썬 코딩의 기술] Concurrency and Parallelism(병행성과 병렬성) - subprocess"
date: 2019-08-30 17:00:00
author: Dojin Kim
categories: Python
tags: python effective_python 파이썬코딩의기술
cover:  "/assets/imgs/python_cover.jpg"
---

> Effective Python - Brett Slatkin을 읽으면서 공부 및 정리를 하며 글을 쓰고 있습니다.

Concurrency와 parallelism의 차이가 무엇인지 살펴보려고 한다. 해당 내용은 [stackoverflow-what-is-difference-between-concurrency-and-parallelism](https://stackoverflow.com/questions/1050222/what-is-the-difference-between-concurrency-and-parallelism) 에서 가지고 왔다.

Concurrency는 2개 혹은 그 이상의 작업들이 시작, 실행, 끝나기까지 서로 겹치는 시간대에 이뤄질 수 있음을 의미한다. 꼭, 여러 작업이 동시에 시작하는 것을 의미한다. 예를 들어, 하나의 CPU core를 가진 컴퓨터에서 os는 싱글 프로세서에서 실행되는 프로그램들을 왔다 갔다 한다. 그래서 마치 모든 작업들이 동시에 이뤄지는 것처럽 보이는 것이다. 

Parallelism은 말 그래도 작업들이 동시에 진행되는 것이다. 예를 들어, multiple CPU core를 가진 컴퓨터에서 multiple 프로그램들이 동시에 실행이 된다. 각 CPU core는 다른 프로그램들이 동시에 진행될 수 있도록 해준다.  

<img src="/assets/imgs/python/effective_python_5.png"/>


둘의 가장 큰 차이점은 **속도**이다. 프로그램이 parallel하게 진행이 되면 전체 실행시간에서 반을 줄 일 수 있다. 반면에, concurrent 프로그램은 여러 프로그램들이 돌아가는 것처럼 보이지만 전체 실행 시간은 줄어들지 않는다.

Python에서 concurrent 프로그램 매우 쉽게 작성할 수 있다. Parallel 프로그램도 system calls, subprocesses, C-extenssions를 사용해서 작성할 수 있지만, 실제로는 concurrent Python 코드를 parallel하게 만드는 것은 매우 어렵다.   그럼에도, 각 상황마다 Python을 최대한 활용하기 위한 방법들을 알아볼 예정이다. 

# Item 36: Child Processes 관리하기 위해 subprocess 사용하기

Python에 의해 시작된 child process들은 parallel로 실행될 수 있다. 그래서 python으로 하여금 모든 CPU의 core를 사용해서 프로그램의 throughpu을 최대화할 수 있게 해준다. 

지금까지 Python으로 subprocess 실행시키는 방법으로는 *popen, open2, os.exec** 등이 있었다. 하지만, 최근 Python에서 child process를 관리하는 최적의 방법은 `subprocess`라는 built-in 모듈을 사용하는 것이다. 이 `subprocess`를 사용하는 것은 매우 간단하다.

```python
proc = subprocess.Popen(
			[‘echo’, ‘Hello from the child!’],
			stdout=subprocess.PIPE)
out, err = proc.communicate()
print(out.decode(‘utf-8’))

# Hello from the child!
```

`Popen` constructor가 process를 시작한다. `communicate` method는 child process의 결과를 읽고 종료까지 기다린다. Child process는 parent process 인 Python 인터프리터로부터 독립적으로 실행된다. 

```python
# child method만들어서 run
def run_sleep(period):
	proc = subprocess.Popen([‘sleep’, str(period)])
	return procstart = time()

procs = []
for _ in range(10):
	proc = run_sleep(0.1)
	procs.append(proc)

# communicate method
for proc in procs:
	proc.communicate()
end = time()
print(‘Finished in %.3f seconds’ % (end - start))
```

`subprocess`를 사용하면 parallel(병렬)하게 프로그램을 사용할 수 있다. Openssl command-line tool을 사용해서 어떠한 데이터를 encrypt(암호화)한다고 가정해보자. Child process를 cmd-line arguments로 시작하고 I/O를 pip하는 것은 쉬운 일이다. 

```python
def run_openssl(data):
	env = os.environ.copy()
	env[‘password’] = b’\xe24U\n\xd0Ql3S\x11’
	proc = subprocess.Popen(
			[‘openssl’, ‘enc’, ‘-des3’, ‘-pass’, ‘env:password’],
			env=env,
			stdin=subprocess.PIPE,
			stdout=subprocess.PIPE)
	proc.stdin.write(data)
	proc.stdin.flush() # Ensure the child gets input
	return proc
```

여기서 랜덤한 byte들을 암호화하는 함수에 pipe를 한다. (실제 상황에서라면 유저의 input, network socket등이 될 것이다) 

child process가 parallel하게 run 하고 input을 받게 된다. `communicate` method로 모두가 끝날 때 까지 기다리고 최종 결과를 받는다 

```python
procs = []
for _ in range(3):
	data = os.urandom(10)
	proc = run_openssl(data)
	procs.append(proc)

for proc in procs:
	out, err = proc.communicate()
	print(out[-10:])

# b’o4,G\x91\x95\xfe\xa0\xaa\xb7’
# b’\x0b\x01\\xb1\xb7\xfb\xb2C\xe1b’
# b’ds\xc5\xf4;j\x1f\xd0c-‘
```

만약 child process가 끝나지 않는 것이 걱정되거나 input 혹은 output pipe가 어쩌다가 막히는 것이 걱정되면 `communicate` method에 `timeout` 인자를 전달하는 것도 방법이다. 인자로 전달된 시간 안에 child process가 반응을 안하면 exception을 발생시키고 종료를 한다. 

```python
proc = run_sleep(10)
try:
	proc.communicate(timeout=0.1)
except subprocess.TimeoutExpired:
	proc.terminate()
	proc.wait()
print(‘Exit status’, proc.poll())
```

이 `timeout`인자는 Python 3.3 혹은 그 이후 버전에서만 사용이 가능하다.