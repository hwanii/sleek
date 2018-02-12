---
layout: post
title: Django Model
featured-img: django
category: Django
summary:
---

django는 웹 애플리케이션 개발 패턴으로 많이 알려져 있는 MVC 패턴과 비슷(사실상 같다고 봐도 무방)한 MTV 패턴을 이용합니다. 그 중, M - Model은 데이터베이스에 데이터를 저장하고 원하는 데이터를 데이터베이스에서 가져올 수 있는 layer입니다. <br>
일반적으로 데이터베이스에 접근하는 방법에는 `SQL`을 활용합니다. django에서는 ORM(Object-relational mapping)을 이용합니다. ORM에 대해서는 다른 포스팅에서 알아보겠습니다. <br>
app을 생성하고 나면, 여러 생성된 파일 중 `models.py`라는 파일에 `class`를 생성하고 테이블에 생성될 요소들을 정의해줍니다. 그리고 `manage.py`를 통해 테이블을 생성/수정하겠다는 migration 파일을 만든 후에, migrate를 통해 데이터베이스에 테이블 생성/수정하게 됩니다. `models.py`에서 일어나는 일들을 간단히 정리하자면,
1. 클래스 생성 및 정의
2. make migration(테이블을 어떻게 생성/수정하겠다는 계획) - `python manage.py makemigrations`
3. migrate(생성한 migration을 토대로 테이블 생성/수정) - `python manage.py migrate`

SQL로 테이블을 create할 때와 마찬가지로, django에서도 필드의 속성을 결정해줘야 합니다. 어떤 필드들이 있는지 [django공식문서](https://docs.djangoproject.com/en/2.0/ref/models/fields/#model-field-types)를 참고하면 되겠습니다.

### Primary Key, Foreign Key
정의만 먼저 보자면, Primary Key는 테이블에 존재하는 수많은 데이터들을 중복 없이 식별할 수 있는 대표값을 의미하고, Foreign Key는 다른 테이블의 Primary Key를 참조하는 Key를 의미합니다. 대표적인 예를 보자면 사원과 부서 테이블이 있습니다. <br>
```
employee
- employee_id -> PK
- first_name
- last_name
....

department
- department_id -> PK
- employee_id -> FK
- department_name
....

```
회사의 모든 사원들의 정보가 담겨있는 employee 테이블과 모든 부서의 정보가 담겨있는 department 테이블을 보면, 각각 `employee_id`, `department_id`라는 PK를 가집니다. 부서에는 여러 명의 사원이 존재하고, 부서 테이블에서 어느 사원이 어떤 부서에 속해있는지 확인할 수 있는 이유는 employee 테이블의 PK를 department 테이블에서 참조하기 때문입니다.<br>
django에서는 `ForeignKey()`필드를 사용해서 Foreign Key를 정해주고, 특정 필드에 `primary_key=True`를 주게 되면, 그 필드는 Primary Key가 됩니다.
```python
class Employee(models.Model):
  employee_id = models.IntegerField(primary_key=True)  # Primary Key 지정

class Department(models.Model):
  employee = models.ForeignKey(Employee, on_delete=models.CASCADE) # Foreign Key 지정
```
참조 방식은 models의 `ForeignKey()`필드를 이용하고, 참조할 테이블을 인자로 합니다. 더불어 참조하고 있는 Primary Key가 삭제될 때 Foreign Key를 어떻게 할지 정하는 on_delete 속성도 명시해줘야 합니다. `models.CASCADE`는 Primary Key가 삭제될 때 해당 Foreign Key의 row 역시 삭제되는 것을 의미합니다. <br>
다른 테이블의 Primary Key를 참조하는 Foreign Key는 `<테이블 이름(소문자)>_id` 형태로 테이블에 생성됩니다.<br>
Foreign Key는 일반적으로 위와 같이 참조하는 테이블을 소문자로 바꾼 문자열로 권장하지만, 꼭 그럴 필요는 없습니다. Foreign Key를 정해줄 때, 참조하는 테이블 선언이 앞 쪽에 있는지 뒤 쪽에 있는지 명확하지 않다면 `'<참조하는 테이블 명>'`과 같은 방식으로 참조합니다.<br><br>

Foreign Key를 통해 참조하는 테이블의 필드를 읽을 수 있습니다.<br>

예를 들면,
```python
from django.db import models


class Manufacturer(models.Model):
    name = models.CharField("Manufacturer's name", max_length=50)

    def __str__(self):
        return self.name


class Car(models.Model):
    manufacturer = models.ForeignKey(Manufacturer, on_delete=models.CASCADE)
    name = models.CharField("Car's name", max_length=60)

    def __str__(self):
        return f'{self.manufacturer.name} {self.name}'

```
Car의 `__str__`을 보면, `self.manufacturer.name`과 같은 방식으로 참조하는 테이블의 필드 값을 찾을 수 있습니다. 반대로, Primary Key를 가진 테이블에서 역참조를 할 때는 `<class명 소문자>_set`과 같은 방식으로 필드 값을 찾을 수 있습니다.


### recursive model(순환 관계)
테이블은 자기 자신을 참조할 수 있습니다. 예를 들면, `사람`이라는 테이블에 누가 누구의 `선생님`인가에 대해 나타낼 때 자기 자신을 참조하는 방식을 사용합니다.
```python
class Person(models.Model):
    name = models.CharField(max_length=60)
    # 자기자신을 다대일로 연결하는 필드
    teacher = models.ForeignKey('self', on_delete=models.SET_NULL, null=True, blank=True)
```
자기 자신을 참조할 땐 첫 번째 인자로 반드시 `'self'`라 명시해줍니다. on_delete의 값인 models.SET_NULL은 연결된 객체가 삭제되면 함께 삭제되지 않고 연결된 객체만 삭제되는 것을 의미합니다.(CASCADE와는 반대 의미)

### simple ManyToMany

피자와 토핑의 예를 들면, 토핑이 여러 종류 있고 피자도 여러 종류 있을 때, 한 피자에 여러 토핑이 필요한 것처럼, 한 토핑은 여러 피자에 들어갈 수 있습니다.

```python
from django.db import models

class Topping(models.Model):
    name = models.CharField(max_length=50)

    def __str__(self):
        return self.name

class Pizza(models.Model):
    name = models.CharField(max_length=50)
    toppings = models.ManyToManyField(Topping)

    def __str__(self):
        return self.name
```
위와 같은 형태로 ManyToMany를 선언해주면, Topping 테이블과 Pizza 테이블이 생기고, 두 테이블을 이어주는 역할을 하는 pizza_topping 테이블이 만들어집니다.

| id | pizza_id | topping_id |
| ------------- |:-------------:| -----:|
| 1 | 1 | 1 |
| 2 | 1 | 2 |
| 3 | 2 | 1 |

### recursive ManyToMany

```python
# 누구를 친구 추가 했으면 서로 친구가 됨 -> 내가 너의 친구면 너도 나의 친구 -> 동등한 관계가 됨.
class FacebookUser(models.Model):
    name = models.CharField(max_length=50)
    friends = models.ManyToManyField('self')

    def __str__(self):
        return self.name
```
마찬가지로 facebookuser_friends 테이블이 생김

| id | from_facebookuser_id | to_facebookuser_id |
| ------------- |:-------------:| -----:|
| 1 | 1 | 2 |
| 2 | 2 | 1 |

필드가 자동적으로 from, to로 생김

이걸 바꾸고 싶다면 `symmetrical=False`를 사용(대칭 관계가 아니다라는 의미)

```python
class InstagramUser(models.Model):
    name = models.CharField(max_length=50)
    following = models.ManyToManyField(
        'self',
        # 대칭관계가 아님
        symmetrical=False,
    )

    def __str__(self):
        return self.name

    def followers(self):
        return self.instagramuser_set.all()
```
| id | from_instagramuser_id | to_instagramuser_id |
| ------------- |:-------------:| -----:|
| 1 | 1 | 2 |
| 2 | 1 | 3 |

Facebook의 경우 친구 관계를 맺는 순간 쌍방향의 관계가 되지만, Instagram의 경우 한 쪽에서 팔로우를 하면 동시에 팔로우 되는 것이 아닌, 팔로우한 사람만 팔로워가 되는 형태. 이럴 때 symmetrical을 사용


### extra ManyToMany

단순 ManyToMany와는 다르게 두 테이블을 연결하는 다른 데이터가 존재할 수 있습니다. 두 테이블이 ManyToMany 관계면서 두 테이블간 관계에 다른 데이터를 추가하기 위해서는 **intermediate 모델** 이 필요합니다.
적절한 예로는, 마찬가지로 SNS 중 Twitter에서 관계를 맺을 때, 관계에는 팔로우뿐만 아니라 팔로우를 막는 block 기능도 있습니다.

```python
from django.db import models

class TwitterUser(models.Model):
  name = models.CharField(max_length=50)
  relations = models.ManyToManyField(
    'self',
    symmetrical=False,
    through='Relation',
    related_name='+',  #  +는 역참조를 없앤다는 의미
  )

  def __str__(self):
    return self.name


class Relation(models.Model):
  """
  유저 간의 관계를 정의하는 모델
  단순히 자신의 MTM이 아닌 intermediate 모델의 역할을 함
  추가적으로 받는 정보는 관계의 타입(팔로우 또는 차단)
  """
  RELATION_TYPE_FOLLOWING = 'f'
  RELATION_TYPE_BLOCK = 'b'
  CHOICE_TYPE = (
    (RELATION_TYPE_FOLLOWING, '팔로잉'),
    (RELATION_TYPE_BLOCK, '차단')
  )
  from_user = models.ForeignKey(
    TwitterUser,
    on_delete=models.CASCADE,
    # 자신이 from_user인 경우의 Relation 목록을 가져오고 싶을 때 사용하는 명령어
    related_name='relations_by_from_user',
  )
  to_user = models.ForeignKey(
    TwitterUser,
    on_delete=models.CASCADE,
    # 자신이 to_user인 경우의 Relation 목록을 가져오고 싶을 때 사용하는 명령어
    related_name='relations_by_to_user',
  )
  # 팔로우: f, 차단: b
  type = models.CharField(max_length=1, choices=CHOICE_TYPE)
  created_date = models.DateTimeField(auto_now_add=True)

  class Meta:
    unique_together = (
      # from_user와 to_user의 값이 이미 있는 경우 DB에 중복 데이터 저장을 막음
      # ex) from_user가 1, to_user가 3인 데이터가 이미 있다면 두 항목의 값이 모두 같은 또 다른 데이터가 존재할 수 없음
      ('from_user','to_user')
    )
```
`related_name`에 각각 `relations_by_to_user`, `relations_by_from_user`로 `related_name`을 정해줬기 때문에 기존 역참조 할 때의 `[class명]_set`을 통해 Foreign Key가 있는 테이블의 값을 가져오는게 아니라, `relations_by_to_user`, `relations_by_from_user`로 가져옵니다. 현재 한 클래스에 Foreign Key가 두 개 있기 때문에 이 경우엔 `related_name`을 정해줘야만 합니다.<br>
따라서 위와 같은 방식으로 TwitterUser 인스턴스가 팔로잉하는 user는 누구인지, 인스턴스의 팔로워는 누구인지 탐색할 수 있습니다.

```python
...

  @property
  def following(self):
    # Relation의 인스턴스 중 type이 RELATION_TYPE_FOLLOWING인 인스턴스를 가져옴
    following_relations = self.relations_by_from_user.filter(
      type = Relation.RELATION_TYPE_FOLLOWING,
    )
    # 위의 쿼리셋에서 user가 팔로잉하는 다른 user(to_user) 값만 필터링. to_user값 자체가 id 걊임
    following_pk_list = following_pk_list.values_list('to_user', flat=True)
    # values_list의 리턴은 쿼리셋 '리스트'이기 때문에 in으로 검색
    following_user = TwitterUser.objects.filter(pk__in=following_pk_list)
    return following_users

  # block_user
  @property
  def block_users(self):
    pk_list = self.relations_by_from_user.filter(type = Relation.RELATION_TYPE_BLOCK).values_list('to_user', flat=True)
    return TwitterUser.objects.filter(pk__in=pk_list)

  def follow(self, to_user):
    """
    to_user에 주어진 TwitterUser를 follow함
    """
    # to_user를 follow하든 block하든 일단 관계를 delete함
    self.relations_by_from_user.filter(to_user=to_user).delete()
    self.relations_by_from_user.create(
      to_user=to_user,
      type=Relation.RELATION_TYPE_FOLLOWING
    )

...
```

-------
1 - 1 relationship
객체가 다른 객체를 확장할 때 사용

hasattr -> exception 없이 요소(속성)을 갖고 있는지 확인 가능




-------
Model Inheritance

django에서 상속은 python의 기본적인 상속과 비슷함
### Abstract base classes(추상화 기반 클래스)
python에서의 기본적인 상속과 비슷하게, 공통된 정보를 다른 모델에 똑같이 적용할 때 사용함. 추상 클래스는 Meta 클래스에 `abstract=True`를 통해 명시함. 추상클래스는 테이블로 migrate 되지 않음
```python
from django.db import models

class Person(models.Model):
  name = models.CharField(max_length=50)

  def __str__(self):
    return self.name

class Item(models.Model):
  person = models.ForeignKey(
    Person,
    on_delete=models.CASCADE
    related_name='items'
    related_query_name='item'
  )
  # 해당 클래스가 추상 클래스임을 명시
  class Meta:
    abstract = True

class Fruit(Item):
  name = models.CharField(max_length=100)

  def __str__(self):
    return self.name

class Food(Item):
  name = models.CharField(max_length=100)

  def __str__(self):
    return self.name
```
python에서의 상속과 같은 방식으로 상속받는 클래스에 상속하는 클래스 명을 명시해줍니다. 클래스를 생성했으니 데이터베이스에 테이블을 추가하기 위해 migrate를 해봅니다.<br>
makemigrations 명령어에서 오류가 납니다. 그 이유는 `Person` 클래스를 `Item` 클래스가 참조하고, `Fruit`, `Food` 클래스가 `Item` 클래스를 상속받는 상황에서 `Person`의 인스턴스가 `Fruit`, `Food`를 역참조할 때의 값(`related_name='items'`)이 겹치기 때문입니다. 현재는 편의상 items라 했지만, 앞으로 더 많은 앱과 클래스를 다루게 된다면, `related_name`을 어떻게 지어야 할지 꽤나 큰 고민이 생길 것입니다. django에서는 이런 고민을 떨쳐내기 위해 다음과 같은 방법을 제공합니다.

* `%(app_label)s` -> app name
* `%(class)s` -> 해당 class name

위와 같은 표현을 이용해서 `related_name`을 정해줍니다.
```python
...

class Item(models.Model):
  person = models.ForeignKey(
    Person,
    on_delete=models.CASCADE,
    related_name='%(app_label)s_%(class)ss',
    related_query_name='%(app_label)s_%(class)s',
  )

...
```
`related_name` 밑에 계속해서 있는 `related_query_name`은 `related_name`이 model의 인스턴스가 역참조할 때 사용되는 것과 다르게, model의 Manager를 이용해서 역참조 관계에 있는 테이블의 요소를 탐색할 때 사용합니다.

```python
>>> from inheritance.rel.models import *
>>> iu = Person.objects.create(name='아이유')
>>> apple = Fruit.objects.create(name='사과',person=iu)
>>> applepie = Food.objects.create(name='사과파이', person=iu)
>>> banana = Fruit.objects.create(name='바나나',person=iu)
>>> banana_cake = Food.objects.create(name='바나나팬케이크', person=iu)
>>> banana_bread = Food.objects.create(name='바나나빵', person=iu)
>>> banana_juice = Food.objects.create(name='바나나주스',person=iu)
>>> dragon = Fruit.objects.create(name='용과',person=iu)
>>> Food.objects.values_list('name', flat=True)
<QuerySet ['사과파이', '바나나팬케이크', '바나나빵', '바나나주스']>
>>> Fruit.objects.values_list('name', flat=True)
<QuerySet ['사과', '바나나', '용과']>

# related_query_name 사용 예
>>> Person.objects.filter(rel_fruit__name__continas='바나나')
<QuerySet [<Person: 아이유>]>
>>> Person.objects.filter(rel_food__name__contains='바나나')
<QuerySet [<Person: 아이유>, <Person: 아이유>, <Person: 아이유>]>
```
`rel_food`에서 `rel`은 `%(app_label)s`, `food`는 `%(class)s`를 나타내게 됩니다. `Item` 클래스가 `Person`클래스를 참조하고, 이를 상속받는 `Fruit`, `Food` 클래스의 요소를 역참조할 때 위와 같은 방식을 사용합니다.<br><br>

-------

* 출처 : [Django 공식문서](https://docs.djangoproject.com/en/2.0/topics/db/models/)
