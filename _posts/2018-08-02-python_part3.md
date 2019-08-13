---
layout: post
title: "[Python] Part 2_파이썬의 기초_문자열"
date: 2018-08-02 14:00:00
author: Dojin Kim
categories: Python
tags: python
cover:  "/assets/imgs/python_cover.jpg"
---

<h2>no 4. 문자열 사용법</h2>
- print()안에 "" 혹은 ''로 감싸고 아무 글자나 숫자를 쓰면 문자열이 된다.
- """ """를 쓰거나 ''' '''를 쓰면 여러줄로 문자열을 표현할 수 있다.

	예)

```
>>> string = 'Hello World'
>>> print(string)
Hello World
>>> string2 = """hamburger
pizza
lobster
cheese
"""

#위와 같은 형태는 string2="hamburger\npizza\nlobster\ncheese" 가 저장된 것과 같다
```


<h2>no 5. 문자열 format-1</h2>
- 가변적으로 바뀔 수 있는 부분은 % 연산자를 통해서 기존 문자열에 숫자나 다른 문자열을 대입한다.
- %s는 문자열, %d는 정수형, %f는 실수형을 의미한다 (이 부분은 C언어와 똑같은 부분이다)

	예)
```
>>> print('Hello %s' % 'World') #%s 자리에 World가 들어가게 된다.
Hello World
>>> string = "Jane"
>>> print("Her name is $s" % string)
Her name is Jane
```
```
>>> print('%d %d %d' % (3,6,9))
3 6 9
>>> print('%f' %4.5)
4.5
```


<h2>no 6. 문자열 format-2</h2>
- formatting을 해주는 문자열 메소드이다
- %s%d%f 대신에 {}기호를 사용하고, 그 뒤에 붙는 %연산자 대신에 .format을 사용한다
- 정수,실수,문자열을 내가 구분하지 않고 넣는대로 알아서 처리하기 때문에 편하다

	예)
```
>>> print('Hello %s' % 'World') #기존의 코드
Hello World
>>> print('Hello {}'.format('World')) #변경된 코드
Hello World
>>> print('My friends are {}, {}, and {}'.format('Jane','Henry','Lisa')) #여러개는 이렇게 표현이 가능하다
My friends are Jane, Henry, and Lisa
>>> print('My friends are {2}, {1}, and {1}'.format('A','B','C')) #숫자를 넣어주면 index를 뜻한다
My friends are C, B, and A
```

<h2>no 7. 문자열 indexing</h2>
- 문자열을 각 문자의 모음이라고 생각하면 된다.
- 문자열의 0번째 index는 문자열의 첫 글자를 의미 한다.

	예)
```
>>> string = "맛있는 사과"
>>> print(string[0])
맛
>>> print(string[4])
사
>>> print(string[-1]) #마이너스 '-' 가 붙으면 뒤에서부터 세기 시작한다
과
```

<h2>no 8. 문자열 slicing</h2>
- 문자열의 일부분만 잘라서 볼 수 있게 해준다

	예)
```
>>> string = "맛있는 사과"
>>> print(string[0:1]) #0번째 index부터 1번째 전까지를 리턴하라는 뜻이다
맛
>>> print(string[4:]) #4번쨰 index부터 끝까지를 리턴하라는 뜻이다.
사과
>>> print(string[:3]) #처음부터 3번쨰 index전까지 리턴하라는 뜻이다
맛있는
```

<h2>no 9. 문자열 split 메소드</h2>
- 문자열을 특정한 단위로 잘라서 리스트 형태로 만들어주는 메소드이다
- .split() 아무것도 없으면 공백단위로 문자열을 자른다는 의미이다.

	예)
```
>>> string = "맛있는 사과와 바나나"
>>> new_string = string.split()
>>> print(new_string)
['맛있는', '사과와', '바나나']
>>> scores = "A:B:C:D"
>>> new_scores = scores.split(":") # : 단위로 문자열을 나눈다.
```

<h2>no 10. end 사용법</h2>
- print()함수와 같이 쓰이고 출력 마지막에 무엇을 넣을지 지정해준다.
- default는 띄어쓰기인 \n이다


	예)
```
>>> print('모세의기적', end='/')
모세의기적/
>>> print('모세의', end='지팡이')
모세의지팡이
```



