---
layout: post
title: "[Django Tutorial 1] 설치, 프로젝트 생성 및 프로젝트 구조 살펴보기"
date: 2019-07-28 17:00:00
author: Dojin Kim
categories: Django
tags: django python
cover:  "/assets/imgs/django_cover.jpg"
---


[장고튜토리얼즈](https://tutorial.djangogirls.org/ko/)와 공식 [장고도큐먼트](https://docs.djangoproject.com/en/2.2/)를 보면서 공부 및 내용 정리를 하는 글입니다.

### Tutorial 1 목표:

- Django 설치하기 및 프로젝트 생성하기
- Project 구조 살펴보기
- Local에서 서버 실행하기

### 요구사항

- Python


## 1. Django 설치하기

    pip install django~=2.0.0

## 2. Django project 생성하기

Django project를 생성하는 방법은 두가지가 있다. Windows cmd(Mac terminal)에서 명령어를 입력해서 생성하는 것과 IDE에서 바로 생성하는 것이다..

### 2.1 cmd(terminal)에서 project 생성하기

    $ django-admin startproject mysite

만약 현재 위치하고 있는 directory에 바로 생성하고 싶으면 한칸 뛰고 . 을 입력하면 된다.

    $ django-admin startproject mysite .

### 2.2. Pycharm IDE에서 프로젝트 생성하기

Django project를 선택하고 설치할 위치를 지정하고 프로젝트명을 작성하고 `Create` 버튼을 클릭한다.
<img src="/assets/imgs/django/django1-create-project.png"/>


## 3. Project 구조 살펴보기

새롭게 생성된 프로젝트는 다음과 같은 구조를 갖게 된다. 

    project_name/
    	manage.py
    	project_name/
                __init__.py
                settings.py
                urls.py
                wsgi.py


간단하게 각 파일들이 하는 역할들을 살펴보면 다음과 같다.

- `manage.py`

: command-line utility로 Django project와 interact를 할 수 있게 해준다. 

- `2번째 project_name`

: 실제 Python package가 있는 directory이다.

- `\__init\__.py`

: 비어있는 파일이지만 삭제하면 안된다! 해당 directory가 Python package로 고려 되어야한다는 의미한다.

- `settings.py`

: settings & configuration 어떻게 작동하는지 알려준다

- `urls.py`

: 장고의 url들이 선언되있는 파일이다 (url의 규칙들을 의미한다)

- `wsgi.py`

: WSGI-compatible web server의 entry point


## 4. Server 작동시키는 방법

default는 8080 port이다. 직접 포트번호를 작성해서 바꿔서 실행시킬 수 있다. `runserver` 명령 뒤에 실행시키고 싶은 포트번호를 작성해주면 된다. (이때 0은 0.0.0.0의 줄임이다)

    $ python manage.py runserver
    
    $ python manage.py runserver 0:8000

서버를 작동시키고 브라우저를 통해 접속을 하게 되면 밑과 같은 화면이 나오게 된다. 화면이 제대로 나왔으면 정상적으로 설치 및 프로젝트 생성이 된 것이다.
<img src="/assets/imgs/django/django1-success-run-server.png"/>
