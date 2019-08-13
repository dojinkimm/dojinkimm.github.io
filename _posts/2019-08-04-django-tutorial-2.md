---
layout: post
title: "[Django Tutorial 2] App, Model 만들기, settings.py 설정 변경 및 DB에 App 추가하기"
date: 2019-08-04 17:00:00
author: Dojin Kim
categories: Django
tags: django python
cover:  "/assets/imgs/django_cover.jpg"
---



[장고튜토리얼즈](https://tutorial.djangogirls.org/ko/)와 공식 [장고도큐먼트](https://docs.djangoproject.com/en/2.2/)를 보면서 공부 및 내용 정리를 하는 글입니다.

### Tutorial 2 목표:

- App 생성하기하기
- 생성한 app 프로젝트 내에서 사용할 수 있도록 setting 변경하기
- 언어와 시간을 한국어로 setting 변경하기
- App에 사용될 model 만들기
- Migration 진행해서 model DB에 저장하기

## 1. App 설치하기

App이란 프로젝트내에서 특정한 기능을 하는 웹 어플리케이션을 의미한다(현 튜토리얼에서는 블로그 글 보고 등록하는 기능의 App) 

이 app을 만들기 위해서 몇개의 파일들이 필요하다. 다음과 같은 명령어를 cmd 혹은 terminal에 입력을 하면 `blog` 란는 directory가 생성이 된다. (이때 blog는 다른 이름으로 대체할 수 있다)

    $ python manage.py startapp blog

명령이 실행되면 내가 생성한 프로젝트 밑에 `blog` 를 가진 directory가 생성되고 구조는 다음과 같다. 

    blog/
    	__init__.py
    	admin.py
    	apps.py
    	migrations/
    		__init__.py
    	models.py
    	tests.py
    	urls.py
    	views.py

## 2. Database 설정하기

App 생성이 완료되었으면 해당 App을 사용할 수 있도록 `project_name/settigs.py` 의 설정을 변경해야 한다. `INSTALLED_APPS` 에 가면 기본적으로 깔려있는 app들의 이름이 미리 적혀있다. 내 app을 django에서 사용하기 위해서는 app의 이름을 추가해주면 된다.

```python
    # settings.py
    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'blog', # 이 부분이 추가 된 부분
    ]
```

## 3. 언어 및 시간 설정하기

`[settings.py](http://settings.py)` 는 default로 영어와 미국 시간을 기본으로 설정을 해놓는다. 언어를 한글로 변경하기 위해서는  `ko-kr` 로 바꿔주고 시간을 한국 시간대로 변경하려면 `Asia/Seoul` 로 설정을 바꿔주면 된다. 나중을 위해, 밑에 `STATIC_ROOT` 도 추가를 해준다.

```python
    #settings.py
    LANGUAGE_CODE = 'ko-kr' #'en-us'
    
    TIME_ZONE = 'Asia/Seoul' #'UST'
    
    USE_I18N = True
    
    USE_L10N = True
    
    USE_TZ = True
    
    
    # Static files (CSS, JavaScript, Images)
    # https://docs.djangoproject.com/en/2.2/howto/static-files/
    
    STATIC_URL = '/static/'
    STATIC_ROOT = os.path.join(BASE_DIR, 'static')  # 이 부분 추가
```

## 4. Model 만들기

`project_name/blog/models.py` 에는 DB에 포함할 model들에 대한 정보가 들어있다. 

```python
from django.db import models
    from django.utils import timezone
    
    
    class Post(models.Model):
        author = models.ForeignKey('auth.User', on_delete=models.CASCADE)
        title = models.CharField(max_length=200)
        text = models.TextField()
        created_date = models.DateTimeField(
                default=timezone.now)
        published_date = models.DateTimeField(
                blank=True, null=True)
    
        def publish(self):
            self.published_date = timezone.now()
            self.save()
    
        def __str__(self):
            return self.title
```

이 예시에서

- models.ForeignKey는 다른 모델에서 사용되는 키값이 여기서 사용되는 것을 의미한다
- models.CharFIeld는 글자 값들을 가지고 있고 지만 최대 글자수를 제한할 수 있다
- models.TextField는 글자 제한이 없는 값이다
- models.DateTImeField는 날짜와 시간 형식의 data type이다.

다른 언어(java)에 비유해서 이야기하면, title과 text는 `string title; string text;` 처럼 사용되는 것이다. 해당 data type에 해당하는 data들만 받아들인다는 것을 의미한다. created_date에 string 형식의 data를 넣는 것은 안된다.

## 5. 만든 Model DB에 추가하기

Model을 만들었으면 새로운 model이 추가되었다고 DB에 알려줘야 한다, 그래야지만 DB에서 해당 model을 사용할 수 있게 된다. 밑에 `makemigrations` 명령어를 작성하면 model이 변경(혹은 생성)되었다는 것을 django에게 알려준다. 

명령어 실행후에 파일로 model에 대한 migration이 `blog/migrations/0001_initial.py` 에 저장된다.

    $ python manage.py makemigrations blog

그 다음에는 migration을 실행시켜야 한다. 실행이 되면 자동적으로 DB 모델과 관련된 테이블이 생성된다. 이 때, default로 DB는 `sqlite3` 을 사용한다. 

    $ python manage.py migrate blog

**TIP:** 

만약 어떤 SQL 쿼리들이 만들어지는지 보고 싶으면 **`[sqlmigrate](https://docs.djangoproject.com/ko/2.2/ref/django-admin/#django-admin-sqlmigrate)`** 명령을 작성하면 된다. 

    $ python manage.py sqlmigrate blog 0001

```sql
BEGIN;
--
-- Create model Post
--
CREATE TABLE "blog_post" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "title" varchar(200) NOT NULL, "text" text NOT NULL, "created_date" datetime NOT NULL, "published_date" datetime NULL, "author_id" integer NOT NULL REFERENCES "auth_user" ("id") DEFERRABLE INITIALLY DEFERRED);
CREATE INDEX "blog_post_author_id_dd7a8485" ON "blog_post" ("author_id");
COMMIT;
```

### 발생할만한 ERROR

- model이 완벽하게 작성되었음에도 migration이 안된다?

    : [settings.py](http://settings.py) 에 `INSTALLED_APPS` 에 app의 이름이 추가가 되었는지 확인한다.