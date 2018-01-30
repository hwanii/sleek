---
layout: post
title: Djangogirls Tutorial 따라하기 - 2
featured-img: django
category: Django
summary:
---
### blog app 만들기
django에서는 **기능** 단위로 app을 구분해주고, 이 app은 패키지와 동일합니다. 따라하기 1에서 생성해 놓은 config 폴더 역시 내부에 `__init__.py` 파일이 있기 때문에 파이썬 패키지라 할 수 있습니다. django 디렉토리로 이동 후 다음과 같은 명령어를 통해 app을 만들 수 있습니다.
```
$ python manage.py startapp blog
```
명령어 수행 후, `tree` 명령어를 통해 생성된 app 목록을 확인해봅니다.
```
$ tree
.
├── blog
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── config
│   ├── __init__.py
│   ├── __pycache__
│   │   ├── __init__.cpython-36.pyc
│   │   └── settings.cpython-36.pyc
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py
```
blog app을 만들었다면, `config` 폴더에 있는 `settings.py` 파일에 있는 `INSTALLED_APPS`에 방금 생성한 `blog`를 추가해줍니다.
```python
...

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog',
]

...
```
`INSTALLED_APPS` 목록을 보면, 방금 작성한 `blog` 외에 이미 설치되어 있는 다양한 app들이 있습니다. django에서는 기본적으로 제공되는 app들을 통해 다양한 기능을 빠르고 쉽게 구현할 수 있습니다. 이 튜토리얼에서는 `django.contrib.auth`를 이용해 블로그 글을 작성하는 작성자를 관리해볼 겁니다.<br><br>
`settings.py` 파일을 연 김에, 제작하려는 웹 애플리케이션의 시간대를 `Asia/Seoul`로 변경해주고, 언어 역시 `ko-kr`로 변경해줍니다.
```python
...

LANGUAGE_CODE = 'ko-kr'

TIME_ZONE = 'Asia/Seoul'

...
```


### 데이터베이스
`settings.py` 파일에서 데이터베이스에 관한 내용을 보면 다음과 같습니다.
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```
기본적으로 django 프로젝트를 생성하면 `sqlite3`라는 데이터베이스를 사용합니다. django에서는 `PostgreSQL`, `MySQL`, `MariaDB` 등 관계형 데이터베이스(RDB)를 지원합니다. 튜토리얼에서는 간단한 앱을 만들기 때문에 `sqlite3` 그대로 설정하겠습니다.<br>
django에서 데이터베이스를 관리하는 경우에 대표적으로 사용되는 명령어가 두 개 있습니다. 우선 데이터베이스 생성 또는 변경을 위한 Model을 구성했다고 가정합니다.
1. `python manage.py makemigrations`
  * migration을 생성하는 명령어로, `0001_initial.py` 또는 `00020002_add_field_created_date.py` 같은 형식의 파일이 생성되고 데이터베이스 테이블을 생성, 수정할 준비를 마칩니다.

2. `python manage.py migrate`
  * 준비된 migration을 적용하는 단계로 데이터베이스가 존재하지 않는 경우 `db.sqlite3` 같은 파일이 생성되면서 그 파일 안에 데이터베이스 테이블이 생성되고, 기존에 존재하는 경우 테이블이 수정됩니다.

현재 데이터베이스 자체가 존재하지 않기 때문에 `python manage.py migrate`를 통해 테이블을 갖지 않는 데이터베이스를 생성하겠습니다.
```
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying sessions.0001_initial... OK
```
문제 없이 생성이 되면 다음과 같은 메시지가 나타납니다.<br><br>
### 웹 서버 실행
지금까지 만들어 놓은 django app이 잘 실행되는지 확인해볼 수 있습니다.
```
$ python manage.py runserver

Performing system checks...

System check identified no issues (0 silenced).
January 30, 2018 - 22:54:58
Django version 2.0.1, using settings 'config.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```
위에 써 있는 `http://127.0.0.1:8000`, 또는 `localhost:8000`을 url로 웹 브라우저에 입력하면 성공적으로 연결되었다는 페이지를 볼 수 있습니다.
<img>
`127.0.0.1`은 localhost의 IP 주소를 의미하고, 콜론(:) 다음에 나오는 8000은 프로그램이 실행되는 포트(PORT)번호를 의미합니다.
