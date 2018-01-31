---
layout: post
title: Djangogirls Tutorial 따라하기 - 7
featured-img: django
category: Django
summary:
---
### template link 및 post_detail 만들기
Post model에 있는 데이터를 이용해 `post_list.html`을 만들었고, 이번에는 각 포스트를 상세히 볼 수 있는 `post_detail.html`을 만들어보려 합니다. `post_list.html`에 있는 링크로 되어있는 제목을 클릭하면 포스트 상세 페이지인 `post_detail.html`로 넘어갈 수 있게 하려면 template link라는 것이 필요합니다.
우선 `post_detail`을 위한 url과 view 함수를 만들어줍니다.
#### blog/views.py
```python
from django.shortcuts import render
from .models import Post

...

def post_detail(request, pk):
    post = Post.objects.get(pk=pk)
    context = {
      'post': post,
    }
    return render(request, 'blog/post_detail.html', context)
```
#### blog/urls.py
```python
from django.urls import path
from . import views

urlpatterns = [
    path('list/', views.post_list, name='post-list'),
    path('list/<int:pk>/', views.post_detail, name='post-detail'),
]
```
앞서 post_list와는 다르게, 한 개의 포스트만을 필요로 하기 때문에 `get()` 함수를 이용해서 필요한 요소를 가져옵니다. `pk`는 Primary Key로 데이터베이스에서 한 테이블의 대표적인 column입니다.(예를 들면, 3학년 1반 학생들이 한 테이블이라면, 학생들의 이름은 중복으로 있을 수 있지만 학번은 Unique하기 때문에 Primary Key입니다.) django에서는 model에서 migrate를 하게 되면, 따로 명시해주지 않는 이상 자동으로 `id`라는 이름의 column이 생기고 Primary Key가 됩니다. 그리고 그 `id`는 `pk`라고 표현할 수 있습니다.<br>
`urls.py`에 나와있는 `<int:pk>`같은 경우는 list의 다음 url로 해당 포스트의 pk값이 온다는 의미입니다. 만약 pk가 1인 경우엔 `list/1/`, 2인 경우엔 `list/2/`와 같은 방식으로 url이 생성됩니다. `path()` 함수의 속성으로 오는 `name`같은 경우엔 template link에서 링크가 어떤 url을 향하는지 name을 통해 명시해줄 수 있습니다. 이는 링크 주소를 절대경로나 상대경로로 하드코딩하지 않게 해줍니다.<br><br>
url과 view를 해결했으니, `post_detail.html`파일을 만들고, `post_list.html`파일 a태그의 링크 부분을 수정하겠습니다.
#### template/blog/post_detail.html
{% raw %}
```html
<!doctype html>
{% load static %}
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>DjangoGirls Tutorial</title>
    <link rel="stylesheet" href="{% static 'bootstrap/css/bootstrap.css' %}">
    <link rel="stylesheet" href="{% static 'css/blog.css' %}">
</head>
<body>
    <div class="header">
        <div class="container">
            <h1>
                <a href="{% url 'post-list' %}">Django Girls Blog</a>
            </h1>
        </div>
    </div>
    <div class="container">
        <div class="post">
            <p class="post-date">published: {{post.published_date}}</p>
            <h2 class="post-title">{{ post.title }}</h2>
            <p class="post-content full">{{ post.content|linebreaksbr }}</p>
        </div>
    </div>
</body>
</html>
```
{% endraw %}
#### template/blog/post_list.html
{% raw %}
```html
<!doctype html>
{% load static %}
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>DjangoGirls Tutorial</title>
    <link rel="stylesheet" href="{% static 'bootstrap/css/bootstrap.css' %}">
    <link rel="stylesheet" href="{% static 'css/blog.css' %}">
</head>
<body>
    <div class="header">
        <div class="container">
            <h1>
                <a href="{% url 'post-list' %}">Django Girls Blog</a>
            </h1>
        </div>
    </div>
    <div class="container">
        {% for post in posts %}
        <div class="post">
            <p class="post-date">published: {{post.published_date}}</p>
            <h2 class="post-title"><a href="{% url 'post-detail' pk=post.pk %}">{{ post.title }}</a></h2>
            <p class="post-content">{{ post.content|linebreaksbr|truncatewords:30 }}</p>
        </div>
        {% endfor %}
    </div>
</body>
</html>
```
{% endraw %}

우선, 공통적으로 있는 header 클래스 div 태그에 있는 a 태그는 포스트 목록이 있는 페이지로 갈 수 있게 링크를 설정해줘야 합니다. template 링크는 다음과 같은 형태로 주어집니다.
{% raw %}
```html
<a href="{% url '[urls.py에서 정한 path name]' %}">
```
{% endraw %}
따라서 template link를 {% raw %}`{% url 'post-list' %}`{% endraw %}로 작성합니다.<br>
그리고 포스트 목록에서 특정 포스트의 제목을 클릭하면 포스트의 상세 내용이 나올 수 있게 하기 위해서는, 마찬가지로 일단 `post-detail`로 명시해준 다음, `post-detail` url에 있던 `<int:pk>`에서의 pk를 정해줘야 합니다. 각 포스트마다 pk가 있기 때문에 `<int:pk>`의 pk를 `post.pk`로 할당해줍니다.
{% raw %}
```html
<a href="{% url 'post-detail' pk=post.pk %}"> <!-- url의 name을 먼저 작성하고 path에 있는 pk값을 정해줌 -->
```
{% endraw %}
정리해보자면,
```
url name : post-list
path: list/
url: localhost:8000/list/
```
post-list같은 경우는 다른 인자가 필요 없는 url이기 때문에, template link를 사용할 때 name만 명시해주면 됩니다.


```
url name: post-detail
          kwargs: {'pk':<int value>} # pk라는 명칭의 정수가 주어져야 함
path: list/<int:pk>/
url : localhost:8000/list/?????????
```
반면에 post-detail 같은 경우는 url에서 `<int:pk>`로, 정수 형태의 `pk`라는 인자를 같게 됩니다. 파이썬에서 인자를 입력해야하는 함수에 인자를 전달하지 않으면 에러가 나듯이, 정수 형태의 인자가 전달이 되어야 합니다. 꼭 `pk`라는 명칭으로 인자를 정할 필요는 없지만, 되도록 의미가 맞는 명칭으로 정하는 것이 좋습니다.
