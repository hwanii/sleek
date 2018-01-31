---
layout: post
title: Djangogirls Tutorial 따라하기 - 6
featured-img: django
category: Django
summary:
---
### django ORM(QuerySet)
`models.py`에 class를 선언해서 Post라는 객체를 선언했고, `makemigrations`, `migrate`를 통해 데이터베이스에 테이블을 생성했습니다. 일반적으로 데이터베이스에 접근할 때, `SQL`이란 것을 통해 데이터를 수정, 삭제, 조회 등의 작업을 할 수 있습니다.<br>
django에서는 ORM(객체 관계 매핑)을 활용해서 데이터베이스에 접근합니다. `python manage.py shell` 명령어로 데이터베이스에 접근해보겠습니다.
```python
In [1]: from blog.models import Post

In [2]: Post.objects.all()
Out[2]: <QuerySet [<Post: Gloria>, <Post: 기억의 빈자리>, <Post: 바람기억>, <Post: 같은 시간 속의 너>]>

In [3]: Post.objects.get(id=1)
Out[3]: <Post: Gloria>

In [4]: Post.objects.get(title='기억의 빈자리')
Out[4]: <Post: 기억의 빈자리>

```
위와 같은 방식으로 데이터베이스에 접근 가능합니다. 위에 있는 내용은 관리자 모드를 통해 내용을 미리 임의로 입력해놓은 것입니다. 삭제나 수정 같은 다른 기능에 대해서는 다른 포스트에서 자세하게 다루겠습니다.

### template에서 데이터 동적으로 불러오기
데이터를 이용하지 않는 정적 웹사이트와는 다르게, 회원 정보나 특정 기능을 위해 데이터가 필요한 경우 데이터베이스에 저장되어 있는 데이터를 활용해야 합니다. 이 때 django에서는 `settings.py`에 `'BACKEND': 'django.template.backends.django.DjangoTemplates'`라고 명시되어 있듯이, `DjangoTemplates`라는 것을 활용해 데이터를 동적으로 불러옵니다. 표현식을 나타낼 땐 `\{\% [내용] \%\}`와 같이 사용하고, 특정 변수를 나타낼 땐 `\{\{ [변수] \}\}`와 같은 방식으로 나타냅니다.(문서 편집을 위해 붙인 역슬래시(`\`)는 모두 제거합니다.)<br><br>
template에서 데이터를 불러오기 위해서는 `views.py`에 있는 해당 template와 연관된 함수의 return에 context를 작성해줘야 합니다.
#### blog/views.py
```python
from django.shortcuts import render
from .models import Post

def post_list(request):
    posts = Post.objects.all()
    context = {
        'posts': posts,
    }
    return render(request, 'blog/post_list.html', context)
```
위에 ORM에서 예시로 봤던 `Post.objects.all()`을 통해 Post에 저장되어 있는 모든 데이터들을 불러옵니다.
context는 딕셔너리 구조로 만들어주고, 딕셔너리의 **키 값** 은 마치 변수처럼 return에 입력한 `blog/post_list.html`에서 사용 가능합니다.
#### templates/blog/post_list.html
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
    <div>
        <h1><a href="">Django Girls Blog</a></h1>
    </div>
    <!-- 역슬래시(\)는 모두 제거할 것 -->
    \{\% for post in posts \%\}
    <div>
        <p>\{\{ post.published_date \}\}</p>
        <h2><a href="">\{\{ post.title \}\}</a></h2>
        <p>\{\{ post.content \}\}</p>
    </div>
    \{\% endfor \%\}

</body>
</html>
```
<img>

### (별첨) css와 bootstrap으로 간단하게 꾸미기
templates 폴더처럼, 프로젝트에 쓰이는 정적 표현(css, sass bootstrap 등)들의 경로도 따로 정해서 사용합니다. `django/static/`으로 폴더를 만들고 `static/bootstrap/`에는 [부트스트랩](https://getbootstrap.com/)에서 다운 받은 css와 javascript 폴더를 넣어줍니다. 추가로 custom css를 위해 `static/css/` 폴더를 생성합니다.그리고 나서 `settings.py`에 static의 경로를 명시해줍니다.
#### config/settings.py
```python
...

# 'django/static' 폴더
STATIC_DIR = os.path.join(BASE_DIR, 'static')
STATICFILES_DIRS = [
    STATIC_DIR,
]

...
```
`static/css/`에 `blog.css` 파일을 만들어 주고 다음 내용을 붙여 넣기 합니다.
```css
body{
    font-size: 13px;
}
h1{
    font-size: 30px;
}
h2{
    font-size: 24px;
}
.header{
    background-color: #393939;
    padding-top: 15px;
    padding-bottom: 15px;
    margin-bottom: 10px;
}
.header a{
    color: white;
    text-decoration: none;
}
.post{
    padding-bottom: 10px;
    border-bottom: 1px solid #ccc;
    margin-bottom: 10px;
}
.post:last-child{
    border : none;
}
.post > .post-date{
    text-align: right;
    font-size: 10px;
    margin-bottom: -6px;
}
.post > .post-title{

}
.post > .post-content{
    max-height: 190px;
    overflow: hidden;
}
.post > .post-content.full{
    max-height: initial;
    overflow: initial;
}
```
또한 기존에 있던 `post_list.html`을 다음과 같이 수정합니다.
```html
<!doctype html>
<!-- 역슬래시(\)를 제거할 것 -->
\{\% load static \%\}
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>DjangoGirls Tutorial</title>
    <!-- 역슬래시(\)를 제거할 것 -->
    <link rel="stylesheet" href="\{\% static 'bootstrap/css/bootstrap.css' \%\}">
    <link rel="stylesheet" href="\{\% static 'css/blog.css' \%\}">
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
        <!-- 역슬래시(\)를 제거할 것 -->
        \{\% for post in posts \%\}
        <div class="post">
            <p class="post-date">published: \{\{ post.published_date \}\}</p>
            <h2 class="post-title"><a href="">\{\{ post.title \}\}</a></h2>
            <p class="post-content">\{\{ post.content|linebreaksbr|truncatewords:30 \}\}</p>
            <!--linebreaksbr : whitespace에서 pre-line과 같은 기능-->
            <!--truncatewords : 30 -> 30개의 단어만 출력. 길이에 대한건 CSS로 해야함-->
        </div>
        \{\% endfor \%\}
    </div>
</body>
</html>
```
수정 후, 서버를 가동시키고 `localhost:8000/` 주소를 확인해보겠습니다.
<img>
