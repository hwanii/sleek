---
layout: post
title: Djangogirls Tutorial 따라하기 - 4
featured-img: django
category: Django
summary:
---

### django - admin

django는 정의한 모델에 대해서 편집할 수 있는 관리자 모드(admin)를 지원합니다. 이 전에 만들었던 `Post` 모델을 편집할 수 있게 `admin.py` 파일에 내용을 추가하겠습니다.
#### admin.py
```python
from django.contrib import admin
from .models import Post

admin.site.register(Post)
```
`admin.py` 파일에 관련 내용을 추가한 후, 서버를 실행 시키고 `127.0.0.1:8000/admin` 또는 `localhost:8000/admin`에서 관리자 모드를 수행할 수 있습니다. 다만, 지금 `superuser`를 생성하지 않았기 때문에 admin 모드를 이용할 수 없습니다. `python manage.py createsuperuser` 명령어로 `superuser`를 추가해줍니다.
```
$ python manage.py createsuperuser
Username (leave blank to use 'taehwan'): hwanii
Email address:
Password:
Password (again):
Superuser created successfully.
```
추가해준 superuser로 관리자 모드를 이용할 수 있습니다.<br><br>
관리자 모드 또한 사용자가 원하는 모습으로 Custom을 할 수 있는데, 관리자 모드 Custom은 추후에 포스팅하겠습니다.
