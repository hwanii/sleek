---
layout: post
title: Djangogirls Tutorial 따라하기 - 9
featured-img: django
category: Django
summary:
---
### 포스트 삭제하기
post-detail 화면에서 삭제 버튼을 누르면 포스트가 삭제되는 기능을 추가하려 합니다. 삭제 기능 같은 경우에도 포스트가 가진 pk 값을 알아야 해당 포스트를 삭제할 수 있기 때문에 pk 값을 인자로 전달해줘야 합니다. 또한, 특정 사용자가 url로 접근(GET 요청)해서 삭제하는 것을 불가능하게 해야 합니다. 따라서 POST 요청으로 삭제 버튼을 만들어줍니다.
#### templates/blog/post_detail.html

{% raw %}
```html
...

    <form action="{% url 'post-delete' pk=post.pk %}" method="POST">
        {% csrf_token %}
        <button type="submit" class="btn btn-danger">삭제</button>
    </form>
{% endblock %}
```
{% endraw %}

`post_detail.html`에 삭제 버튼을 우선 추가해줍니다. 앞서 언급한 대로, 삭제를 하는 행위 역시 서버에 접근하는 것이기 때문에 `POST` 요청 방식의 form을 사용합니다. `POST` 요청이기 때문에 마찬가지로 `csrf` 방지를 위한 구문을 넣어줍니다. 그리고 `post-detail`에서 사용되고 있는 post의 pk를 함께 인자로 작성해줍니다.
#### blog/urls.py
```python
from django.urls import path
from . import views

urlpatterns = [
    path('list/', views.post_list, name='post-list'),
    path('list/<int:pk>/', views.post_detail, name='post-detail'),
    path('add/', views.post_add, name='post-add'),
    path('<int:pk>/delete/', views.post_delete, name='post-delete'),
]
```

#### blog/views.py
```python
...

def post_delete(request, pk):
    post = Post.objects.get(pk=pk)
    if request.method == 'POST':
        post.delete()
        return redirect('post-list')
    return redirect('post-detail', pk=post.pk)
```
특정 pk를 가지는 `Post`의 인스턴스를 요청이 `POST`일 경우 삭제해주고, 그렇지 않을 경우 `post-detail` 페이지가 나타날 수 있도록 합니다.

### 포스트 수정하기
