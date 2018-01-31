---
layout: post
title: Djangogirls Tutorial 따라하기 - 5
featured-img: django
category: Django
summary:
---

### URL
URL(Uniform Resource Locator, 문화어: 파일식별자, 유일자원지시기)은 네트워크 상에서 자원이 어디 있는지를 알려주기 위한 규약입니다(출처:[위키백과](https://ko.wikipedia.org/wiki/URL)). 앞서 진행을 해오면서 이용했던 `127.0.0.1:8000`이나 `localhost:8000` 역시 마찬가지로 URL이라 할 수 있고, DNS로 된 `https://www.naver.com` 같은 것들도 URL이라 할 수 있습니다.<br>
django에서는 `URLconf(URL configuration)`이란 파이썬 모듈을 사용해 URL을 설계할 수 있습니다.
#### config/urls.py
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('blog.urls')),
]
```
config에 있는 urls.py 파일을 다음과 같이 수정을 해줍니다. 현재 blog라는 app을 만들어 놓은 상태고, `include('blog.urls')` 표현의 의미는 blog app에 있는 `urls.py` 파일에 있는 url pattern들을 포함하겠다는 의미입니다. 따라서, blog app에도 `urls.py` 파일을 생성해야 합니다.

#### blog/urls.py
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.post_list),
]
```
`path()` 함수의 첫번째 인자로 디자인 하려는 url 패턴을 정해줍니다. ''로 아무 것도 입력하지 않았다면 현재 상황에서는 `localhost:8000/`을 의미합니다. `python manage.py runserver` 명령어로 서버를 가동시켜보겠습니다.
```
$ python manage.py runserver
...

AttributeError: module 'blog.views' has no attribute 'post_list'
```
에러가 발생하는 것을 확인할 수 있습니다. urlpatterns에 입력한 path 중에  `views.post_list` 부분에 있는 `post_list`가 존재하지 않기 때문에 발생하는 에러입니다.

### Views
정리하자면, `urls.py`에 있는 path는 결국 `views.py`에 있는 특정 함수를 통해 페이지가 구현되는 형태를 보입니다. 그렇다면 `views.py`에 `post_list` 함수를 만들어보겠습니다.

#### blog/views.py
```python
from django.http import HttpResponse

def post_list(request):
    return HttpResponse('post list')

```
우선 `post list`라는 메시지를 응답으로 페이지에 띄워주는 코드를 작성해봤습니다. 서버를 실행시키고 `localhost:8000/` 주소를 입력해보겠습니다.
<img>
빈 페이지에 post list라는 메시지가 쓰여있는 것을 확인할 수 있습니다. 이런 방법으로 `HttpResponse` 함수에 html 문서를 빼곡히 적어도 정상적으로 작동은 하겠지만, 상당히 보기에도 좋지 못하고 django의 패턴인 MTV 패턴에도 만족하지 않습니다. 현재까지 M - model, V - view를 만들었기 때문에 client에게 보여지는 T - template를 만들어보겠습니다.

### Templates
template는 기본적으로 client이 보게 되는 html 문서를 의미합니다. 기존의 MVC 패턴을 알고 있다면 MVC 패턴의 View와 동일한 기능을 합니다. 우선, template를 위한 폴더를 따로 생성한 후에 그 폴더를 template을 위한 폴더라는 것을 `settings.py`에 명시해줘야 합니다.
#### config/settings.py
```python
# 'django/templates' 폴더
TEMPLATES_DIR = os.path.join(BASE_DIR, 'templates')  # TEMPLATES 경로 추가

...

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        # Django에서 템플릿을 검색할 경로 목록
        'DIRS': [
            TEMPLATES_DIR,
        ],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

...
```
template 폴더의 경로를 `settings.py`에 설정해줬다면, `django/template/blog/`까지 디렉토리를 만들고 그 안에 `post_list.html` 파일을 만듭니다. 그리고 `blog/views.py`에서 이 html을 불러 올 수 있게 수정하겠습니다.
#### blog/views.py
```python
from django.shortcuts import render


def post_list(request):
    return render(request, 'blog/post_list.html')
```
request를 받아서 `post_list.html`으로 전달합니다. `post_list.html`도 내용이 잘 연결되는지 html 문서를 작성해보겠습니다.
#### django/template/blog/post_list.html
```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <h1>post list 페이지입니다.</h1>
</body>
</html>
```
<img>
제대로 작동하는 것을 확인할 수 있습니다. 이쯤에서 전체적인 과정을 정리 해보자면,

1. 브라우저에서 특정 페이지(`localhost:8000/`) 요청
2. 요청이 runserver로 실행 중인 서버에 도착
3. runserver는 요청을 django code로 전달
4. django code중 `config.urls` 모듈이 해당 요청을 받음
5. `config.urls` 모듈은 ''(admin/를 제외한 모든 요청)을 `blog.urls` 모듈로 전달
6. `blog.urls` 모듈은 받은 요청의 URL과 일치하는 패턴이 있는지 검사
7. 있다면 일치하는 패턴과 연결된 함수(view)를 실행
  * `settings.py` 모듈의 TEMPLATES 속성 내의 DIRS 목록에서 `blog/post_list.html` 파일의 내용을 가져옴
  * 가져온 내용을 적절히 처리(렌더링, `render()`함수)하여 리턴
8. 함수의 실행 결과를 브라우저로 다시 전달
