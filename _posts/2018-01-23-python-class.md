---
layout: post
title: Python - 클래스
featured-img: python
category: Python
summary: Class, Instance, Inheritance
---

## 클래스 (Class)
파이썬에선 앞서 포스팅 했던 함수도 객체이고, 심지어 문자열, 숫자까지도 모두 객체입니다. 숫자는 숫자를 표현하는 클래스의 인스턴스이고, `dir()` 함수를 통해 어떤 속성과 메서드를 가진 객체인지 확인이 가능합니다.<br>
파이썬에서 클래스는 객체를 만들기 위한 틀입니다. 흔히 게임에서 표현되는 캐릭터 역시 여러가지 속성과 메서드를 가진 클래스입니다.
```python
>>> class Character:
...     def __init__(self, name):
...             self.name = name
...
```
`__init__`은 초기화 메서드로, 객체를 생성할 때 인자를 주어 각기 다른 객체를 생성할 수 있습니다. `self`는 객체의 메서드를 정의할 때 항상 첫 번째 인수로 쓰입니다. `self.name`에서 알 수 있듯이, `Character` 클래스를 참조할 객체를 초기화 할 때 `name`을 정해줘야 합니다.
```python
>>> warrior = Character('John Doe')
```
위와 같은 방식으로 `warrior`라는 객체가 `Character`클래스를 참조하는 객체가 되었고, 인자로 `__init__` 함수에서 정했던 `name`을 받으면서 `warrior` 객체의 `name`은 `John Doe`가 되었습니다. 접근 방법은 `객체.속성명` 또는 `객체.메서드명`으로 가능합니다.
```python
>>> warrior.name
'John Doe'
```
### 인스턴스 메서드
인스턴스 메서드는 객체의 상태를 확인하거나 조작하는데 사용됩니다.
```python
class Character:
    def __init__(self, name, ch_type, skill):
        self.name = name
        self.ch_type = ch_type
        self.skill = skill

    def character_info(self):
        print(f'캐릭터 정보 ({self.name})')
        print(f'  타입: {self.ch_type}')
        print(f'  스킬: {self.skill}')

    def change_type(self, ch_type):
      self.ch_type = ch_type
```
```python
>>> warrior = Character('John Doe', '힘캐', '돌진')
>>> warrior.character_info()
캐릭터 정보 (John Doe)
  타입: 힘캐
  스킬: 돌진

>>> warrior.change_type('민첩캐')
>>> warrior.character_info()
캐릭터 정보 (John Doe)
  타입: 민첩캐
  스킬: 돌진
```

### 속성 접근 지정자
> 캡슐화?

객체를 구현할 때, 특정 부분을 은닉시켜 정해진 방법만을 통해서 객체를 조작할 수 있게 하는 것을 의미합니다. 기존 정적 프로그래밍 언어에서 사용하던 `private`, `protected`, `public`과 비슷하게 파이썬에서도 사용 가능합니다.
* `private` : `__속성명`
* `protected` : `_속성명`

```python
class Character:
    def __init__(self, name, ch_type, skill):
        self.name = name
        self.__ch_type = ch_type  # ch_type만 private 지정
        self.skill = skill

    def character_info(self):
        print(f'캐릭터 정보 ({self.name})')
        print(f'  타입: {self.__ch_type}')
        print(f'  스킬: {self.skill}')
    def change_type(self, ch_type):
        self.ch_type = ch_type

```
```python
>>> warrior = Character('John Doe', '힘캐', '돌진')
>>> warrior.skill
'돌진'
>>> warrior.ch_type
AttributeError                            Traceback (most recent call last)
<ipython-input-24-95b080b67034> in <module>()
----> 1 warrior.ch_type

AttributeError: 'Character' object has no attribute 'ch_type'
```
`ch_type`에 접근할 수 없다는 것을 확인할 수 있습니다. <br>
사실 다른 정적 프로그래밍언어 같은 경우는 변수에 직접적으로 접근하는게 불가능해서 `getter`나 `setter` 메서드로 접근 가능하지만, 파이썬에서는 private 지정자에 대해 `_클래스명__속성명` 으로 저장되어 있습니다. 물론 파이썬에서도 `getter`, `setter` 메서드 역할을 하는 프로퍼티가 존재합니다.

### getter/setter 프로퍼티

private 속성에 대해 직접 접근하는 것이 불가능 하지만, 앞서 언급한 대로 `getter`와 `setter` 메서드로 접근이 가능합니다. 정적 언어인 `Java`의 예를 들어보겠습니다.
```java
class exm{
  private int a;

  public int getA(){
    return a;
  }
  public void setA(int b){
    a = b;
  }
}
```
`private` 속성인 a의 값을 return 하는 것이 `getA()`, a의 값을 수정하는 것이 `setA()`로, 메서드를 통해 `private` 속성에 접근하고 있습니다.<br>
파이썬에서도 마찬가지로, `@property`와 `@속성명.setter`를 통해 접근 가능합니다.
```python
class Character:
    def __init__(self, name, ch_type, skill):
        self.name = name
        self.__ch_type = ch_type  # ch_type만 private 지정
        self.skill = skill

    def character_info(self):
        print(f'캐릭터 정보 ({self.name})')
        print(f'  타입: {self.__ch_type}')
        print(f'  스킬: {self.skill}')

    def change_type(self, ch_type):
        self.ch_type = ch_type

    @property
    def ch_type(self):
        return self.__ch_type

    @ch_type.setter
    def ch_type(self, new_type):
        self.__ch_type = new_type
        print(f'Set new type ({self.__ch_type})')
```
```python
>>> warrior = Character('John Doe', '힘캐', '돌진')
>>> warrior.ch_type
'힘캐'
>>> warrior.ch_type = '민첩캐'
Set new type (민첩캐)
>>> warrior.ch_type
'민첩캐'
```
`private` 속성에 대해 직접 접근하려 했을 때 오류가 났던 것과 다르게, `getter`, `setter`를 설정해주고 나면 오류 없이 읽기, 수정 모두 가능해집니다.
항상 둘 다 나타내는 것이 아니라 필요에 따라 `getter`만 나타낼 수 있고, `setter`만 나타낼 수 있습니다.

### 상속
클래스 간에는 상속 관계가 성립되는데, 상속하는 상위 클래스를 부모 클래스, 상속 받는 하위 클래스를 자식 클래스라고 합니다. 상속 받을 때는 클래스를 정의할 때 클래스명 옆에 소괄호 안에 부모 클래스를 적으면 됩니다.
```python
class User(Character):
  pass
```
상속 받은 자식 클래스는 부모 클래스에 있는 속성과 메서드를 모두 사용 가능 합니다. 그리고 메서드는 자식 클래스에서 다른 동작을 할 수 있게 만들 수 있습니다. 이 방법을 **메서드 오버라이드** 라고 합니다.<br>
`Character` 클래스에 있는 `character_info()` 함수를 실행했을 때 다른 문구가 나오게 하는 것도 메서도 오버라이딩을 하는 예 중에 하나입니다.(_Character 클래스에 있는 `ch_type` 속성을 상속 받는 자식 클래스만 접근 가능하게 private 형태에서 protected 형태로 바꿨습니다._)
```python
class User(Character):
    def character_info(self):
        print(f'User1의 캐릭터 정보 ({self.name})')
        print(f'  타입: {self.ch_type}')
        print(f'  스킬: {self.skill}')
```
```python
>>> minsu = User('Minsu','민첩캐','회오리폭풍')
>>> minsu.character_info()
User1의 캐릭터 정보 (Minsu)
  타입: 민첩캐
  스킬: 회오리폭풍
```
### 부모 클래스의 메서드 호출 : super()
메서드 오버라이딩 같이 상속 받은 메서드를 아예 새로 쓰는 것이 아니라, 부모 클래스에서 정의된 메서드에 추가 작업을 할 경우, `super()` 메서드를 통해 부모 클래스의 메서드를 직접 호출할 수 있습니다.
```python
class User(Character):
    def __init__(self, name, ch_type, skill, rating):
        super().__init__(name, ch_type, skill)
        self.rating = rating
    def character_info(self):
        print(f'User1의 캐릭터 정보 ({self.name})')
        print(f'  타입: {self.ch_type}')
        print(f'  스킬: {self.skill}')
        print(f'  레이팅: {self.rating}')
```
```python
>>> minsu = User('Minsu','민첩캐','회오리폭풍',13)
>>> minsu.character_info()
User1의 캐릭터 정보 (Minsu)
  타입: 민첩캐
  스킬: 회오리폭풍
  레이팅: 13
```
동물로 비교를 해보자면, 기본적인 동물 클래스에는 얼굴, 몸이 있다면 동물 클래스를 상속 받는 조류 클래스는 얼굴, 몸에 날개가 추가로 있습니다. 이런 경우도 마찬가지로 부모 클래스에 있는 `__init__` 메서드에 내용을 추가하는 경우라 할 수 있습니다.

### 정적 메서드 : @staticmethod
정적 메서드는 해당 클래스 내부에 정의된 일반 함수를 의미하고, 다른 클래스 메서드와는 다르게 인스턴스에게 아무런 영향을 주지 않습니다. 정적 메서드는 메서드 선언 바로 위에 @staticmethod 데코레이터를 붙여 사용합니다.
정적 메서드는 다양한 방식으로 인스턴스를 생성하는 클래스를 작성할 때 자주 사용됩니다.
```python
import random

class Random_item:
    def __init__(self, name, rating):
        self.name = name
        self.rating = rating
    @staticmethod
    def rand_rating():
        rating_list = ('1등급','2등급','3등급','4등급')
        selected_rating = random.choice(rating_list)
        sword_list = ('집행검', '할배검', '참백도')
        selected_sword = random.choice(sword_list)
        return Random_item(selected_sword, selected_rating)
```
```python
>>> random_sword = Random_item.rand_rating()
>>> print(random_sword.name, random_sword.rating)
집행검 4등급
>>> random_sword = Random_item.rand_rating()
>>> print(random_sword.name, random_sword.rating)
할배검 1등급
```
### 클래스 메서드 : @classmethod
클래스 메서드는 클래스 속성에 대해 동작하는 메서드입니다. 인스턴스 메서드와 달리 호출 주체가 클래스이고, 첫번째 인자도 클래스입니다. 첫번째 인자가 인스턴스로 주어지더라도 해당 인자의 클래스로 자동으로 바뀌어 전달됩니다.<br>
클래스 메서드는 `@classmethod` 데코레이터를 붙여 선언하고, 첫번째 인자인 클래스를 관용적으로 `cls`로 사용합니다.<br>
부모 클래스를 상속받은 자식클래스에서 해당 부모클래스의 클래스 메서드를 사용할 경우, 자기 자신의 클래스를 사용할 수 있습니다.
```python
import random

class Random_item:
    def __init__(self, name, rating):
        self.name = name
        self.rating = rating
    @staticmethod
    def rand_rating():
        rating_list = ('1등급','2등급','3등급','4등급')
        selected_rating = random.choice(rating_list)
        sword_list = ('집행검', '할배검', '참백도')
        selected_sword = random.choice(sword_list)
        return Random_item(selected_sword, selected_rating)
    @classmethod
    def dummy_item(cls):
        return cls('알 수 없음', '알 수 없음')

class Virtual_sword(Random_item):
    def sword_sound(self):
        print('쉬싱')
```
```python
>>> dummy = Random_item.dummy_item()
>>> print(dummy.name)
알 수 없음
>>> print(Random_item)
class __main__.Random_item
>>> print(type(dummy))
class __main__.Random_item

>>> dummy2 = Virtual_sword.dummy_item() # 자식 클래스에서 부모의 클래스 메서드 사용
>>> print(dummy2.name)
알 수 없음  # 부모의 클래스 메서드로 인해 name이 알 수 없음이 된 것을 확인할 수 있음
>>> dummy2.sword_sound()
쉬싱
>>> print(Virtual_sword)
class __main__.Virtual_sword
>>> print(type(dummy2))
class __main__.Virtual_sword
```

### 다형성, 동적 바인딩

**다형성** : 하나의 코드가 여러 역할을 한다는 것을 의미합니다. 흔히 사용하는 형변환 메서드인 `str()`의 경우, 인자로 어떤 타입의 객체에 상관 없이 문자열로 변환시켜 줍니다. 다양한 객체들을 인수로 전달할 수 있는 것을 보아, `str()` 메서드가 다형성을 띄고 있다는 것을 알 수 있습니다.
```python
>>> str(1234)
'1234'
>>> str([1,2,3,4])
'[1,2,3,4]'
>>> str((1,2,3,4))
'(1,2,3,4)'
>>> str(list)
"<class 'list'>"
```
<br>
**동적 바인딩** : 타입을 신경 쓰지 않고 인스턴스를 사용할 수 있게 해주는 방식입니다. 앞서 언급한 다형성을 띄고 있는 함수에 인자로 다야한 객체들을 전달할 수 있습니다.<br>
동적 바인딩은 속성 검색 과정을 통해 이루어집니다. 예를 들어, `minsu`라는 인스턴스의 속성인 `name`에 `minsu.name`으로 속성에 접근하면, `minsu`에서 `name`을 검색하고, 그 다음엔 `minsu`의 클래스인 `User`에서, 그리고 그 다음엔 상속받은 클래스가 있다면 상속받은 클래스인 `Character`에서 해당 속성을 검색하며 가장 먼저 검색한 속성을 반환합니다.
<br>
이 과정에서 `minsu`가 어떤 타입의 객체인지 상관 없고, `minsu`가 `name`에 해당하는 속성을 가졌는지만 검사합니다. 이러한 작동 방식은 `덕 타이핑(duck typing)`이라고 하고 객체가 가진 변수와 메서드가 해당 객체의 타입을 결정한다는 것을 의미합니다.<br>
덕 타이핑에 대한 [위키백과](https://ko.wikipedia.org/wiki/%EB%8D%95_%ED%83%80%EC%9D%B4%ED%95%91)의 예제를 보겠습니다.
```python
class Duck:
        def quack(self): print("꽥꽥!")
        def feathers(self): print("오리에게 흰색, 회색 깃털이 있습니다.")

class Person:
        def quack(self): print("이 사람이 오리를 흉내내네요.")
        def feathers(self): print("사람은 바닥에서 깃털을 주어서 보여 줍니다.")

def in_the_forest(duck):
        duck.quack()
        duck.feathers()

def game():
        donald = Duck()
        john = Person()
        in_the_forest(donald)
        in_the_forest(john)
```
```python
>>> game()
꽥꽥!
오리에게 흰색, 회색 깃털이 있습니다.
이 사람이 오리를 흉내내네요.
사람은 바닥에서 깃털을 주어서 보여 줍니다.
```
예에서 보듯이, `in_the_forest` 함수가 인자를 받을 때 `Duck` 클래스의 인스턴스인 `donald`, `Person` 클래스의 인스턴스인 `john` 서로 다른 두 객체를 인자로 받고, 받은 객체의 `quack()`, `feathers()` 함수를 실행하는 것을 보면, `in_the_forest` 함수를 통해 동적 바인딩이 이루어지는 것을 알 수 있습니다.<br>
`in_the_forest` 함수에 인자로 `donald`가 올 땐, `donald`의 클래스인 `Duck` 클래스에서 정의해준 `quack()`, `feathers()` 함수가 호출되고, 이와 동일하게 인자로 `john`이 올 때도 `john`의 클래스인 `Person` 클래스의 함수들이 호출됩니다.<br><br>
계속 사용했던 예시에 적용해보겠습니다.
```python
class User(Character):
    def __init__(self, name, ch_type, skill, rating):
        super().__init__(name, ch_type, skill)
        self.rating = rating
        self.attack = 0
    def character_info(self):
        print(f'User1의 캐릭터 정보 ({self.name})')
        print(f'  타입: {self.ch_type}')
        print(f'  스킬: {self.skill}')
        print(f'  레이팅: {self.rating}')
        print(f'  공격력: {self.attack}')
    def equip(self, item):
        item.use(self)

class Random_item:
    def __init__(self, name, rating):
        self.name = name
        self.rating = rating
    @staticmethod
    def rand_rating():
        rating_list = ('1등급','2등급','3등급','4등급')
        selected_rating = random.choice(rating_list)
        sword_list = ('집행검', '할배검', '참백도')
        selected_sword = random.choice(sword_list)
        return Random_item(selected_sword, selected_rating)
    def use(self, user):
        if self.rating == '1등급':
            user.attack += 10
        elif self.rating == '2등급':
            user.attack += 20
        elif self.rating == '3등급':
            user.attack += 30
        else :
            user.attack += 40

```
```python
>>> hwanii = User('Ryu', '마법사', '도망가기', 1200)
>>> random_sword = Random_item.rand_rating()
>>> random_sword.rating
3등급
>>> hwanii.equip(random_sword)
>>> hwanii.character_info()
User의 캐릭터 정보 (Ryu)
  타입: 마법사
  스킬: 도망가기
  레이팅: 1200
  공격력: 30
```
