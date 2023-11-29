---
title: "[React/연습] #번외. React 개발 위해 back-end 세팅 다시 하기"
categories: back-end react web vscode
tags: java
toc: true
toc_sticky: true
---

어제까지 개발 잘 하고 있었는데 VS Code의 Spring Boot extension이 업데이트 되면서 원래 사용하고 있던 버전 설정에서 오류가 났다.
오늘은 이 오류를 해결해보고자 한다.

## Java 17 for MAC

원래 내 맥북에 설치된 JDK 버전은 15인데 이제 스프링부트를 쓸 수 있는 최소 버전이 17 버전이라, 우선 이걸 설치하기로 했다.

JDK17을 설치하기에 앞서 먼저 맥북에 brew가 설치되어 있어야 하는데 이 부분에 대한 정리는 생략하겠다.

### openjdk17 설치

```console
$ brew install openjdk@17
```

잘 설치되었다면 이런 식으로 뜬다.

![openjdk17-installed](/assets/images/react-study/231129_openjdk17.png)

### (참고) 위에서 설치 안 될 경우

아래의 명령어를 먼저 실행해 볼 것.

```console
$ brew tap adoptopenjdk/openjdk
```

### path 지정

```console
$ echo 'export PATH="/usr/local/opt/openjdk@17/bin:$PATH"' >> ~/.zshrc
```

```console
$ vi ~/.zshrc
```

파일에 아래의 내용을 추가한다.

```
export PATH=${PATH}:$JAVA_HOME/bin
export CPPFLAGS="-I/usr/local/opt/openjdk@17/include"
```

```console
$ source ~/.zshrc
```

### 버전 확인

```console
$ java -version
```

그럼 이제 17 버전으로 나오고 있는 것을 확인할 수 있다.

![openjdk17](/assets/images/react-study/231129_java17.png)

## Visual Studio Code 프로젝트 생성

본격적으로 다시 Java 17 버전에 맞는 프로젝트를 생성해 보자.<br>
[이 글](https://hei-jung.github.io/front-end/back-end/react/web/vscode/settings/react-init/)에서 했던 것처럼 그대로 버전만 바꿔서 생성하려고 한다.

1. '`command⌘`+`shift⇧`+`p`' => 'Spring Initializr: Create a Gradle Project' 선택
2. Spring Boot 버전 선택: ex. <span style="color:red">3.2.0</span>
3. 개발 언어 선택: <span style="color:red">**Java**</span>
4. GroupId 입력 => ArtifactId 입력
5. 패키지 타입 선택: <span style="color:red">**Jar**</span>
6. Java 버전 선택: 개발 환경에 맞는 버전으로 선택: <span style="color:red">17</span>
7. dependencies 선택: **Spring Web**, **Thymeleaf**

---

## 참고자료

- [MacOS 환경에서 Java JDK 설정 및 변경하기 : homebrew, 다운로드 파일](https://adjh54.tistory.com/216) => 16 이하의 버전을 설치하는 방법에 대해서도 잘 정리되어 있다.
- [Java 17 설치(Mac)](https://vkein.tistory.com/176)
