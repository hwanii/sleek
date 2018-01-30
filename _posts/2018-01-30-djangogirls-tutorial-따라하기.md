---
layout: post
title: Djangogirls Tutorial 따라하기 - 1
featured-img: django
category: Django
summary:
---
## Django Tutorial
[Djangogirls](https://tutorial.djangogirls.org/ko/) 튜토리얼을 따라 블로그를 만들어보려 합니다. Djangogirls 홈페이지에 나와있는 튜토리얼은 django 1버전이라 최근에 새로 나온 djnago 2버전으로 튜토리얼을 진행하려 합니다. 또한 일반 편집기가 아닌, `Pycharm Professional`로 진행했습니다.
### 1. 가상환경 설정, django 설치 및 프로젝트 폴더 지정

우선 가상환경 설정은 [가상환경 설정](https://hwanii.github.io/python-%EC%84%A4%EC%B9%98-%EB%B0%8F-%EA%B0%80%EC%83%81%ED%99%98%EA%B2%BD%EC%84%A4%EC%A0%95/) 페이지에 나와있는 대로 필요한 패키지들을 설치하고, `re-djnagogirls`라는 가상환경을 만들어서 `djangogirls_tutorial` 디렉토리에 가상환경을 local화 하겠습니다.
```
djangogirls_tutorial $ pyenv local re-djangogirls
```
django 프로젝트를 제작하면서 git으로 관리할 것이기 때문에 `.gitignore` 파일을 생성해주고, [gitignore.io](gitignore.io) 페이지에 가서 검색어에 mac 사용자분들은 `macOS`, 리눅스 사용자분들은 `Linux`, 그리고 공통으로 `Pycharm+all`, `Python`, `git`, `django`를 한번에 검색해서 나온 결과를 `.gitignore`에 넣겠습니다.<br><br>
그리고 git으로 관리해주기 위해 `git init` 명령어를 입력합니다. 마지막으로 파이썬 인터프리터를 새로 만든 가상환경으로 지정해줍니다.
전체적인 과정을 정리하자면,
1. `djangogirls_tutorial` 디렉토리 생성 및 이동
2. `re-djangogirls`라는 이름의 가상환경 추가 및 설정
3. `pip install django` 명령어로 django 설치
4. `.gitignore` 파일 생성 후, [gitignore.io](gitignore.io) 사이트에서 `macOS`, `Linux`, `Pycharm+all`, `Python`, `git`, `django` 를 입력해서 나온 텍스트를 `.gitignore`에 내용 추가
5. `git init` 명령어로 프로젝트 폴더를 git으로 관리
6. 파이썬 인터프리터 변경

### 2. django 프로젝트를 위한 기본 setting
django 프로젝트를 생성하는 명령어를 통해 djangogirls_tutorial 폴더 내에 프로젝트를 생성합니다.
```
$ django-admin startproject mysite
```
여기서 mysite는 프로젝트 이름을 의미합니다. 프로젝트 성격에 맞게 이름을 정하면 됩니다. 우분투에서는 `tree` 명령어를 통해 디렉토리 구조를 확인할 수 있습니다.
```
$ tree
.
└── mysite
    ├── manage.py
    └── mysite
        ├── __init__.py
        ├── settings.py
        ├── urls.py
        └── wsgi.py
```
.는 djangogirls_tutorial 폴더를 의미하고, 밑에 mysite라는 django 프로젝트 폴더가 생성된 것을 확인할 수 있습니다. 가장 위에 있는 mysite라는 디렉토리 이름을 django 프로젝트만을 위한 디렉토리라는 의미로 django로 바꿔주겠습니다.
```
.
└── django
    ├── manage.py
    └── mysite
        ├── __init__.py
        ├── settings.py
        ├── urls.py
        └── wsgi.py
```
바꾼 후, pycharm에서 django 폴더를 root directory로 바꾸지 않으면 django 폴더 자체를 패키지로 인식할 수 있기 때문에 `Mark directory as -> Sources root`를 클릭해줍니다.<br>
<img>

django 폴더 바로 밑에 있는 mysite 폴더는 django 프로젝트 전체의 설정들을 변경할 수 있는 파일들이 있기 때문에 `config`라고 이름을 바꿔주겠습니다. 이 때, pycharm에서는 파이썬 파일 내에서 mysite로 쓰인 부분을 한 번에 바꿔줄 수 있기 때문에 Rename 하는 순간 나오는 체크박스를 모두 선택해 줍니다.
<img>

그리고 밑에 console 창에 refactor에 대한 내용이 나오면 do refactor를 클릭해주면 관련 내용들이 모두 변경이 됩니다.<br><br>
가장 상위에 있던 mysite를 django로 바꿔주고, 그 안에 있던 mysite를 config를 바꾸는 이유는 프로젝트를 진행하면서 무수히 많은 파일 또는 폴더가 생성됐을 때, 우리가 필요로 하는 부분을 빠르게 찾기 위해서입니다. mysite라는 폴더가 중복으로 있으면 커맨드라인 상에서 헷갈릴 일도 있을 수 있겠죠.
<br>
정리하자면,
* djangogirls_tutorial -> 프로젝트를 위한 부수적인 파일들도 있는 폴더
* django -> django에만 해당하는 폴더(django 프로젝트 폴더)
* config -> django 프로젝트의 설정들을 관리하는 폴더

django 폴더 안에서 쓰이는 패키지들은 모두 django app이라 불리기도 합니다.
