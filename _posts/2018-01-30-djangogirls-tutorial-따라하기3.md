---
layout: post
title: Djangogirls Tutorial 따라하기 - 3
featured-img: django
category: Django
summary:
---

### 블로그 글 모델 만들기
일반적인 블로그를 떠올려보면, 글 제목(Title), 글 내용(Content), 글쓴이(Author), 날짜(Created_date, Published_date)가 필요합니다. 이런 항목들을 한 곳에 모아놓은 데이터베이스 테이블을 떠올려보면,
```
Post : 게시글 포스트 모델
  author : 글쓴이
  title : 제목
  content : 내용
  created_date : 작성일
  published_date : 배포일
```
이런식으로 모델링을 할 수 있습니다. `models.py` 파일에 클래스 정의를 통해 위 내용을 django 모델로 만들 수 있습니다.
```python
from django.db import models
from django.utils import timezone

class Post(models.Model):
  author = models.ForeignKey(
    'auth.User',
    on_delete=models.CASCADE
  )
  title = models.CharField(max_length=200)
  content = models.TextField(blank=True)
  created_date = models.DateTimeField(
    default = timezone.now
  )
  published_date = models.DateTimeField(
    blank=True, null=True
  )

  def publish(self):
    self.published_date = timezone.now()
    self.save()

  def __str__(self):
    return self.title
```

* 정의한 Post 클래스는 데이터베이스의 형태를 결정해주는 `models` 모듈의 Model 클래스를 상속 받아서 데이터베이스 모델링을 합니다.

* author에서 정의해준 `ForeignKey`는 외래키로, django 프로젝트 생성 할 때 `INSTALLED_APPS` 목록에 있던 `auth.User`의 `Primary Key`를 사용합니다.
다음 줄에 있는 `on_delete=models.CASCADE`는 연결되어있는 row가 사라질 경우 함께 사라진다는 의미입니다.

* title의 `CharField`는 최대 길이가 200인 문자를 의미합니다.

* 반면에, content의 `TextField`는 길이에 제한이 없지만, `CharField`는 Performance 측면에서 더 우수하기 때문에 짧은 길이의 글은 `CharField`, 긴 글은 `TextField`를 사용하는 것이 좋습니다.

* created_date와 published_date는 `DateTimeField`를 이용해 시간을 표현합니다. `django.utils`에 있는 `timezone`을 이용합니다.

* `publish()` 함수는 현재 시간을 published_date에 할당해주고, 데이터를 저장해줍니다. 데이터베이스와 연동될 때는 할당으로 끝나는 게 아니라, `save()` 함수를 통해 데이터베이스에 저장을 해줘야 합니다.


데이터베이스 모델을 생성했기 때문에, migration을 만들어주고, migrate를 해줘야 합니다.
```
$ python manage.py makemigrations blog
Migrations for 'blog':
  blog/migrations/0001_initial.py
    - Create model Post

$ python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, blog, contenttypes, sessions
Running migrations:
  Applying blog.0001_initial... OK
```
위 명령어들을 입력 후, 파일 목록을 보면 `0001_initial.py` 파일이 생성된 것을 확인할 수 있습니다.
<img>
