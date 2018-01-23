---
layout: post
title: Python - Pycharm 설치
featured-img: python
category: Python
summary: Pycharm 설치 및 세팅
---

## Pycharm
JetBrain 기업이 운영하는 파이썬 IDE 중 하나

### Pycharm 설치(Ubuntu)

* [JetBrain](https://www.jetbrains.com/pycharm/) 페이지에서 `Download now` 버튼 클릭
* 대학생인 경우, 인증을 통해 유료 버전인 `Professional` 설치 가능합니다.
* 그 외의 경우 무료버전인 `Community`버전을 다운로드 합니다.
* 다운로드 할 경우 압축파일로 다운 받아지고, 다운로드 된 경로로 들어가 압축을 풀어줍니다.
```
tar xvf pycharm-community-2017.3.3.tar
```
* 압축을 풀고, `pycharm-community-2017.3.3`폴더에 있는 `bin` 폴더로 들어가서 Pycharm을 실행시켜줍니다.
```
./pycharm.sh
```
* 처음에 뜨는 파이참 테마를 제외하곤 처음 뜬 창을 닫아줍니다.
* open 클릭 후 프로젝트 디렉토리 선택
* 상단 메뉴 중 `Create Command-Line Launcher`를 선택 후 `OK`
* 원하는 프로젝트 폴더에 들어가 `charm .`이라는 명령어를 실행하면 해당 폴더를 프로젝트 파일로 pycharm이 실행됩니다.
