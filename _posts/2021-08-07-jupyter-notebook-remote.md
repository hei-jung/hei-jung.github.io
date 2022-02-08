---
title: "[Linux] jupyter notebook 원격 서버 설정"
categories: linux
tags: linux remote-access
toc: true
toc_sticky: true
---

우분투 pc에서 작업 중인 jupyter notebook 프로젝트를 외부 pc에서도 작업하고 싶을 때!<br>
터미널을 열어 다음과 같은 설정을 해주면 된다.


## 1. 우분투 포트 방화벽 해제

```
sudo ufw allow 8888
```

원격 접속 시 사용할 포트(ex. 8888) 방화벽 해제


## 2. jupyter notebook 환경 설정(config 파일 만들기)

```
jupyter notebook --generate-config
```

`/home/사용자/.jupyter` 디렉터리에 config 파일이 생성된다.


## 3. 서버 암호 생성

```
ipython
```

```python
from notebook.auth import passwd
passwd()
```

`passwd()`까지 치고 나면,


```
Enter password:
```

가 뜨는데 그러면 원격 서버 접속 시 사용할 비밀번호를 입력하고 엔터 키 누르면 된다.<br>
참고로 터미널 상에는 입력 중인 비밀번호가 표시되지 않는다.

```
Verify password:
```

위에서 설정한 비밀번호를 한 번 더 입력한다. 입력하고 엔터 치면 작은따옴표 안에 암호화된 비밀번호가 출력되는데, 이 비밀번호를 미리 복사해둔다. (뒤에서 사용)


## 4. 서버 환경설정

```
vi ~/.jupyter/jupyter_notebook_config.py
```

수정할 코드의 '#'을 지워서 주석을 해제한다.

```python
c.NotebookApp.allow_origin = '*'  # 외부 접속 허용
c.NotebookApp.notebook_dir = '작업경로'  # 작업경로 설정. 절대경로로 입력해야 함
c.NotebookApp.ip = 'ip주소'  # 외부 접속 시 사용할 ip 설정
c.NotebookApp.port = '8888'  # 외부 접속 시 사용할 포트 설정. 기본값 8888
c.NotebookApp.password = 아까 복사해둔 비밀번호  # 비밀번호 설정
c.NotebookApp.password_required = True  # 비밀번호 요구
c.NotebookApp.open_browser = False  # 서버pc에서 브라우저 자동으로 안 열리도록 설정
```


## 5. 서버 실행

```
jupyter notebook
```

이제 외부 pc에서 브라우저를 열어 ip주소:포트번호를 주소창에 치면 접속됨!<br>
나는 password_required=True로 설정해놔서 비밀번호 치는 창이 먼저 나왔다.<br>
그럼 위에서 내가 설정했던 비밀번호를 치면 로그인이 되며 작업 경로로 접속된다.

﻿
