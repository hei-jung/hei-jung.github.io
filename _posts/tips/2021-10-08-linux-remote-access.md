---
title: "[Linux] 다른 PC에서 Linux로 원격 접속하기"
subtitle: "Jupyter Notebook, ip 및 port 설정 등등"
categories: linux
tags: linux jupyter-notebook
toc: true
toc_sticky: true
---

다른 PC에서 Linux로 원격 접속할 수 있도록 Linux 설정을 바꿔보자.



## 왜 이 글을 쓰게 됐는지?

우리 랩실은 원격 접속이 가능하다. 그래서 꼭 학교에 오지 않더라도 집에서도 필요한 작업을 자유롭게 할 수 있었다.<br>
그런데 어느 날 학교 측에서 보안 때문에 포트를 하나 빼고 다 막아버려서 추가 설정이 필요해졌다.<br>



## SSH 터미널 환경설정

이 설정을 하려면 먼저 linux 터미널에 ssh 명령어가 설치되어 있어야 한다.


### 1. ssh 설치 확인 및 설치

터미널을 열어 아래의 명령어를 실행한다.

```console
username@device:~$ command -v ssh
```

`command -v {프로그램}`는 linux pc에 특정 프로그램이 존재하는지 확인하는 명령어다.<br>
얘를 실행한 결과 `/usr/bin/ssh` 이런 식으로 나오면 설치되어 있는 거다. 그러면 설치 작업은 건너뛰고 바로 다음 단계로 넘어가면 된다!<br>

ssh가 설치되어 있지 않은 경우엔 아래의 명령어를 실행하여 설치해준다.

```console
username@device:~$ sudo apt install ssh
```


### 2. 포트 설정 변경

포트를 직접 설정하기 전에 기본 포트는 항상 22로 설정되어 있다.

```console
username@device:~$ sudo vi /etc/ssh/sshd_config
```

위의 명령어를 실행해보면 `Port 22`가 주석 처리되어 있는 것을 찾을 수 있는데, 방금 말한 것처럼 디폴트 값이 22이라는 의미다.

![Screenshot from 2021-10-08 14-32-42](https://user-images.githubusercontent.com/40985307/136503608-0dc2048b-a314-4866-8635-9800d43338af.png)

우리는 여기서 `#`을 지워 주석을 해제하고, 22 대신 할당하고자 하는 포트(ex.1234)를 적어주면 된다.<br>
그런 다음에 ssh를 재실행하여 바꾼 설정을 적용한다.

```console
username@device:~$ sudo /etc/init.d/ssh restart
```


### 3. 포트 변경 확인

다음 명령어를 쳐서 포트가 제대로 바뀌었는지 확인한다.

```console
username@device:~$ sudo netstat -anp|grep LISTEN|grep sshd
```

밑의 결과처럼 설정한 포트 값으로 나오면 잘 바뀐 것이다.

```console
tcp        0      0 0.0.0.0:1234              0.0.0.0:*               LISTEN      1525/sshd           
tcp6       0      0 :::1234                   :::*                    LISTEN      1525/sshd           
```


### 4. 타 PC에서 원격 접속

이제 다른 컴퓨터를 켜서 우리가 설정한 주소로 잘 접속이 되는지 확인해보자.<br>
Linux나 Mac 환경을 쓰는 경우엔 터미널을, Windows 환경을 쓰면 Anaconda Prompt 등을 열어 아래와 같이 입력한다.

```console
$ ssh username@ip -p 1234
```

username은 linux 컴퓨터의 계정 이름이고 ip는 linux 컴퓨터의 ip 주소(모른다면 터미널에 `ifconfig` 명령어를 쳐서 확인할 수 있다)를 쓰면 된다.
`-p`는 포트 옵션을 의미하고 `-p`를 쓴 다음에 접속하고자 하는 포트 넘버를 쓰면 해당 포트로 접속하겠다는 뜻이 된다.<br>

sftp를 쓰는 경우,

```console
$ sftp -P 1234 username@ip
```

이렇게 `ssh` 대신 `sftp`를 쓰면 된다. 바로 `Connected to username.`라고 뜨거나 비밀번호가 설정돼 있는 경우 비밀번호를 입력하라고 뜨면 원격 연결이 잘 되고 있는 것이다.


### 참고. 원격 접속할 ip 및 port 키워드로 저장하기

저렇게 매번 username, ip, port를 치기 귀찮을 때, 얘네를 한꺼번에 묶어 하나의 키워드로 저장하는 편리한 방법이 있다.<br>

```console
$ vi ~/.ssh/config
```

`~/.ssh/config`은 ssh 설정 파일이다. 여기에 키워드를 추가해줄 것이다. 요렇게:

```
Host {my id}
        Hostname {my ip}
        Port {port}
        User {username}
```

예를 들어서 나는 이런 식으로 설정했다.

```
Host hj
        Hostname 000.000.000.000  # 랩실 컴퓨터 ip주소
        Port 1234
        User asdf  # 랩실 컴퓨터에서 쓰는 계정 이름
```

그럼 다음에 ssh나 sftp 접속을 할 시에는 명령어가 훨씬 간단해진다.

```console
$ ssh hj
$ sftp hj
```



## Jupyter Notebook 환경설정

ssh 터미널을 사용하면 내 PC에서 linux PC 파일에 접근할 수 있어 편리하다.<br>
그런데 꼭 파일에 접근할 필요 없이 Jupyter Notebook만 사용하는 게 목적이라면 Jupter Notebook 설정을 해주는 것만으로 원격 접속이 가능하다.


### 1. config 파일

```console
username@device:~$ vi ~/.jupyter/jupyter_notebook_config.py
```

`~/.jupyter/jupyter_notebook_config.py`는 Jupyter Notebook 설정 파일이다. 여기서 몇 가지 항목을 조금 수정하려고 한다.

```
c.NotebookApp.allow_origin = '*'
c.NotebookApp.ip = 'my ip'  # 내 linux pc의 ip주소
c.NotebookApp.open_browser = False
c.NotebookApp.port = 1234  # 주피터 노트북 접속에 사용할 포트번호
```

해당되는 부분의 주석을 해제하고 값을 변경한다.<br>
참고로 config 파일 길이가 엄청 길어서 고쳐야 할 부분을 찾기가 귀찮은데, 아래 명령어를 쓰면 필요한 부분을 검색할 수 있다.

```config
: /검색어
```

예를 들어서,

```config
: /c.NotebookApp.ip
```

라고 치고 엔터를 누르면 커서가 c.NotebookApp.ip로 바로 이동한다.<br>

설정값을 바꾸는 변수들에 대해서 조금 설명을 보충하자면, 먼저 `allow_origin`을 `*`로 하면 모든 외부 접속을 허용하겠다는 의미가 된다.<br>

`open_browser`는 자동으로 Jupyter Notebook 창을 열어주겠냐는 건데, 기본값 True를 사용하면 Jupyter Notebook 실행 시 서버 컴퓨터(linux)의 브라우저가 자동으로 열려서 다른 컴퓨터에서 주소를 치고 원격 접속을 할 수 없게 된다.
따라서 얘는 False로 설정해줘야 한다.<br>그럼 Jupyter Notebook을 열고자 하는 컴퓨터에서 브라우저를 열고 주소를 쳐서 들어갈 수가 있다.<br>

이 부분은 바로 아래 단계에서 무슨 뜻인지 이해할 수 있을 것이다.
그리고 `ip`와 `port`는 본인 linux 컴퓨터의 ip와 원격 접속에 쓸 포트를 기입하면 된다.


### 2. Jupyter Notebook 실행

서버 컴퓨터인 linux 환경에서 실행해줘야 한다. linux 터미널을 열어 `jupyter notebook`을 입력하자.

```console
username@device:~$ jupyter notebook
[I 15:25:23.377 NotebookApp] JupyterLab extension loaded from /compuworks/anaconda3/lib/python3.8/site-packages/jupyterlab
[I 15:25:23.377 NotebookApp] JupyterLab application directory is /compuworks/anaconda3/share/jupyter/lab
[I 15:25:23.379 NotebookApp] Serving notebooks from local directory: /home/username/Desktop
[I 15:25:23.379 NotebookApp] Jupyter Notebook 6.1.4 is running at:
[I 15:25:23.379 NotebookApp] http://my ip:1234/
[I 15:25:23.379 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
```

그럼 중간에 `http://ip:port` 형태로 주소가 뜰 텐데, 이제 이 주소를 가지고 현재 linux 컴퓨터 외의 다른 기기에서도 접속할 수 있다.


### 3. 다른 기기에서 원격 접속

브라우저(Chrome, Safari, Microsoft Edge 등등 본인이 편한 거) 실행 > 주소창에 `ip:port` 입력 후 엔터

![Screenshot from 2021-10-12 12-10-24](https://user-images.githubusercontent.com/40985307/136885057-61530172-f94f-4f6a-be53-d0ed6273f3cc.png)

이런 식으로 Jupyter Notebook 화면이 나오면 잘 되고 있는 거다.


### 참고. 작업 경로 설정

나는 처음에 저대로 설정하고 주피터 노트북에 들어갔더니, 내가 쓰는 폴더들이 보이지 않았다.
알고 보니 이것도 추가 설정이 필요했다.<br>다시 config 파일을 열어,

```
c.NotebookApp.notebook_dir = '내 작업경로(절대경로)'
```

이 부분을 수정했다. 기본값은 공백인데, 그럼 `~/.jupyter/`가 제일 root 경로로 설정되는 모양이다.<br><br>
그렇게 되면 `~/.jupyter/` 디렉터리 바깥의 파일들에는 접근할 수가 없기 때문에 기호에 따라서 필요한 파일들을 전부 여기로 옮기거나, 아니면 이렇게 경로를 직접 설정해주는 작업이 필요하다.
이때 경로는 반드시 상대경로가 아닌, **절대경로**를 적어줘야 한다.


## <span style="color:red">유의할 점(매우 중요!!!)</span>

하나의 port를 여러 가지 용도로 사용하는 것은 불가능하다. 이게 무슨 말이냐면, ssh를 위해 열어둘 포트와 Jupyter Notebook을 원격으로 쓰기 위한 포트는 같은 걸 쓸 수 없다.<br>
예를 들어서 ssh 포트를 1234로 설정한 다음에 Jupyter Notebook config 파일에서도 포트를 똑같이 1234로 설정하고 Jupyter Notebook을 실행하면,

```console
[I 11:43:55.355 NotebookApp] The port 1234 is already in use, trying another port.
[I 11:43:55.411 NotebookApp] JupyterLab extension loaded from /compuworks/anaconda3/lib/python3.8/site-packages/jupyterlab
[I 11:43:55.411 NotebookApp] JupyterLab application directory is /compuworks/anaconda3/share/jupyter/lab
[I 11:43:55.413 NotebookApp] Serving notebooks from local directory: /home/username/Desktop
[I 11:43:55.413 NotebookApp] Jupyter Notebook 6.1.4 is running at:
[I 11:43:55.413 NotebookApp] http://my ip:1235/
```

이렇게 첫 번째 줄에서 `1234`는 이미 사용중이므로 다른 포트를 사용하라는 메시지가 뜨며 포트가 `1234`의 다음 번지인 `1235`로 자동 할당 된다.<br>

자동 할당된 포트도 외부 PC에서 접근할 수 있으면 괜찮은데, 만약 나처럼 원격 접속에 사용할 수 있는 포트가 한 개 뿐이라면 필요에 따라 ssh나 Jupyter Notebook 둘 중에 하나만 해당 포트를 사용하도록 해야 한다.<br>
내 경우엔 원격 접속의 주요 목적이 Jupyter Notebook 사용이라서 ssh 포트를 주석 처리하고 Jupyter Notebook config 파일에서만 `c.NotebookApp.port = 포트번호`로 설정했다.
그 외 ssh나 sftp 프로토콜을 써야 할 때는 [ssh 포트 설정](#SSH-터미널-환경설정)을 따르고 있다.<br>

이건 윈도우의 원격 접속을 설정할 때에도 유의할 점으로, 다음 글에서 다른 PC에서 윈도우로 원격 접속하는 방법을 정리하며 다시 얘기하려 한다.
