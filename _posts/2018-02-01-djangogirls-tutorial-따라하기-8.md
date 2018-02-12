---
layout: post
title: Djangogirls Tutorial 따라하기 - 8
featured-img: django
category: Django
summary:
---

### template extend
지금까지 만든 html 파일인 `post_list.html`과 `post_detail.html`을 보면, 두 template 모두 header 부분이 같은 것을 확인할 수 있습니다. 이럴 때, base가 되는 template을 따로 정해주고, 그 base template를 불러와서 각각의 내용을 채우는 방식을 사용하면, 각 html 파일의 길이도 짧아지고, 가독성이 높아진다는 장점이 있습니다. `base.html`을 `template/` 경로에 만들어주겠습니다.
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
                <a href="">Django Girls Blog</a>
            </h1>
        </div>
    </div>
    <div class="container">
        <h2>{% block title %}{% endblock %}</h2>
        {% block content %}
        {% endblock %}
    </div>
</body>
</html>
```
{% endraw %}

기존에 있던 `post_detail.html`이나 `post_list.html`의 내용을 복사해서 만든 다음, `class가 container인 div`태그의 내용을 모두 지운 후, 위와 같이 작성합니다. {% raw %}`{% block [block 명] %}`{% endraw %} 과 같은 형태로 block을 만들어주고, 포함하는 범위의 끝에는 {% raw %}`{% endblock %}`{% endraw %} 로 마무리를 합니다.<br>
이 `base.html`을 `post_list.html`, `post_detail.html`에서 **extends** 해서 사용하게 되는데, 사용하는 방식은 다음과 같습니다.
#### blog/post_list.html

{% raw %}
```html
{% extends 'base.html' %}

{% block title %}Post list{% endblock %}
{% block content %}
    {% for post in posts %}
        <div class="post">
            <p class="post-date">published: {{ post.published_date }}</p>
            <h2 class="post-title"><a href="{% url 'post-detail' pk=post.pk %}">{{ post.title }}</a></h2>
            <p class="post-content">{{ post.content|linebreaksbr|truncatewords:30 }}</p>
        </div>
    {% endfor %}
{% endblock %}
```
{% endraw %}

#### blog/post_detail.html

{% raw %}
```html
{% extends 'base.html' %}

{% block title %}Post detail{% endblock %}
{% block content %}
    <div class="post">
        <p class="post-date">published: {{ post.published_date }}</p>
        <h2 class="post-title">{{ post.title }}</h2>
        <p class="post-content full">{{ post.content|linebreaksbr }}</p>
    </div>
{% endblock %}
```
{% endraw %}

우선, extends를 할 html 파일을 {% raw %}`{% extends '[html 파일명]' %}`{% endraw %}과 같은 방식으로 맨 위에 작성해줍니다. 그리고 `base.html` 파일에서 정해줬던 block을 적절한 위치에서 그대로 사용하면 됩니다. title block 같은 경우는 header 바로 밑에 title을 `h2` 태그로 나타내줍니다. content block 같은 경우는 나머지 내용인 detail, list를 나타내는 영역입니다.<br><br>

### 포스트 추가하기
그동안 admin 페이지에서 포스트를 추가해왔습니다. 이제는 웹페이지에서 직접 포스트를 추가하는 기능을 만들어보겠습니다. 사실 django에서 `form`에 대한 기능을 제공하지만, 직접 `form`을 만들어 보겠습니다.
#### templates/blog/post_add.html
{% raw %}
```html
{% extends 'base.html' %}

{% block title %}Post add{% endblock %}
{% block content %}
<form action="" method="POST">
    {% csrf_token %}
    <div class="form-group">
        <label for="title">제목</label>
        <input class="form-control" type="text" name="title" value="">
    </div>
    <div class="form-group">
        <label for="content">내용</label>
        <textarea name="content" class="form-control" id="content" cols="30" rows="10"></textarea>
    </div>
    <button class="btn btn-primary" type="submit">작성</button>
</form>
{% endblock %}
```
{% endraw %}

우선 포스트를 추가하기 위한 페이지인 `post_add.html` 파일을 만들고 마찬가지로 `base.html`을 extends 하겠습니다. 그동안 **GET** 방식으로 데이터를 조회하는 작업만 수행했다면, 이번에는 데이터를 추가하는 것이기 때문에 서버 데이터에 접근하는 방식인 **POST** 방식을 사용해야 합니다. <br>
**POST** 방식으로 데이터를 서버에 전달하면, `request`에 `method` 항목이 `POST`로 주어지게 됩니다. 그리고 위의 `input` 태그와 `textarea` 각각의 `name`과 내용이 딕셔너리 형태로 전달되게 됩니다.<br>
그리고 django에서는 **POST** 방식으로 데이터를 서버에 전송할 때, **csrf(사이트 간 요청 위조)** 공격을 방지하기 위해 {% raw %}`{% csrf_token %}`{% endraw %}을 사용합니다. 자세한 내용은 [위키백과](https://ko.wikipedia.org/wiki/%EC%82%AC%EC%9D%B4%ED%8A%B8_%EA%B0%84_%EC%9A%94%EC%B2%AD_%EC%9C%84%EC%A1%B0)을 참고하시면 되겠습니다.<br><br>

그리고 언제든지 포스트를 추가할 수 있게 `base.html`에 포스트 추가 태그를 추가합니다.
#### templates/base.html
{% raw %}
```html
...

        <div class="container">
            <h1>
                <a href="{% url 'post-list' %}">Django Girls Blog</a>
            </h1>
            <a class="btn btn-primary" href="{% url 'post-add' %}">포스트 추가</a>
        </div>

...
```
{% endraw %}

url path는 `add/`로 하겠습니다.
#### blog/urls.py
```python
from django.urls import path
from . import views

urlpatterns = [
    path('list/', views.post_list, name='post-list'),
    path('list/<int:pk>/', views.post_detail, name='post-detail'),
    path('add/', views.post_add, name='post-add'),
]
```

마지막으로 `views.py`에 `post_add()` 함수를 작성해보겠습니다. 앞서 언급한대로, **POST** 방식일 땐, `request`에 `method` 항목의 값으로 `POST`가 오게 되고, 서버로 보내는 데이터는 `request`의 `POST` 항목으로 딕셔너리 형태로 넘어오게 됩니다.
#### blog/views.py
```python
...

def post_add(request):
    if request.method == 'POST':
        title = request.POST['title']  # 제목 부분에 입력한 내용 할당
        content = request.POST['content']  # 내용 부분에 입력한 내용 할당
        post = Post.objects.create(
            author = request.user,  # 현재 admin에 로그인 되어있는 superuser
            title = title,
            content = content,
        )
        return redirect('post-detail', pk=post.pk)  # POST 요청일 때의 return
    return render(request, 'blog/post_add.html')  # 일반적인 GET 요청일 때(post-add 화면으로 갔을 때)의 return
```
우선, 기본적으로 admin 페이지에서 superuser로 로그인이 되어있는 상황을 만들겠습니다. 그러면, `request.user`는 현재 로그인 되어있는 superuser를 의미하게 됩니다. <br>
그리고 지금까지 `render()` 함수를 이용해서 특정 html의 주소로 context를 보내는 렌더링 방식을 사용했지만, 이 방식으로 포스트를 추가하게 되면(POST 방식으로 서버에 접근하게 되면), 화면은 `post-detail`를 보여주지만 url이 `localhost:8000/add/`가 될 것입니다. 그 것을 방지해주고 특정 url path로 redirect 하는게 `redirect()` 함수입니다. 첫 번째 인자로 redirect 하려는 url의 name을 정해주고, 해당 url에 필요한 인자가 있다면 그 다음 인자로 작성을 합니다. 다만, 텍스트를 입력하고 작성 버튼을 누를 때 POST 방식으로 서버에 접근하게 되지만, 그 외의 경우는 우선 `post_add.html` 페이지를 띄워줘야 합니다. 따라서, 마지막 줄 같이 `post_add.html`로 렌더링 하는 부분도 추가해줘야 합니다. <br>
정리하자면, 다음과 같은 순서로 `post-add`가 이루어집니다.

1. post_add 페이지를 보여줌 (GET 요청)
2. post_add 페이지에서 글 작성 (POST 요청)
3. post_add view에서 POST 요청에 대한 처리 완료 후, 브라우저에는 post-detail(pk=...)에 해당하는 주소로 redirect를 하도록 응답 (301 redirect, URL: /3)
4. 브라우저는 301 redirect 코드를 갖는 HTTP response를 받고, 해당 URL로 GET 요청을 보냄(새로 만든 Post의 pk가 3일때) 브라우저는 '/3'주소로 GET 요청
5. '/3'주소로 온 요청은 다시 `runserver -> Django코드 -> urls.py -> blog/urls.py -> def post_detail(request, pk)`로 도착, post_detail 뷰 처리가 완료된 post_detail.html의 내용을 응답 -> 브라우저는 해당 내용을 보여줌
