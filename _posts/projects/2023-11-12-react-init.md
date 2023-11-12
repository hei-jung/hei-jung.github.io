---
title: "[React/연습] #01. Visual Studio Code에서 React 개발하기"
categories: front-end react web vscode settings
tags: java javascript react
toc: true
toc_sticky: true
---

React 웹을 구현해보자.

## Visual Studio Code

Java나 Kotlin 기반의 웹 개발은 주로 IntelliJ IDEA Ultimate에서 하는 경우가 많지만 아무래도 이 프로그램이 유료라 개인 프로젝트 연습을 하기엔 한계가 있어서 여기선 VS Code를 사용하려고 한다. (참고: **맥북** 기준)

### 유용한 단축키

개발 환경설정에 앞서 유용하게 쓸 수 있는 단축키를 정리한다.

- `command⌘`+`shift⇧`+`x`: extensions 단축키
- `command⌘`+`shift⇧`+`p`: 명령어 팔레트

### 필요한 extensions 설치 (for back-end)

#### 1. Extension Pack for Java

'`command⌘`+`shift⇧`+`x`' => 검색창에 'java' 검색 => 'Extension Pack for Java' 설치

![Java extension pack 설치](/assets/images/react-study/231112_install_java.png)

#### 2. Spring Boot Extension Pack

extensions 검색창에 'spring boot' 검색 => 'Spring Boot Extension Pack' 설치

![Spring-boot extension pack 설치](/assets/images/react-study/231112_install_springboot.png)

### npm (Node.js Package Manager) 설치 (for front-end)

Node.js는 확장성 있는 네트워크 앱(특히 서버) 구현을 위해 설계된 JavaScript 런타임 환경이다.
npm은 Node.js의 설치 및 실행 프로그램 패키지라고 생각하면 된다.<br>
터미널에서 npm 설치 여부를 확인한다.

#### npm 설치 여부 확인

```console
$ node --version
```

```console
$ npm --version
```

설치가 되어 있는 상태라면 버전이 뜬다.

![npm 확인](/assets/images/react-study/231112_npm_version.png)

설치가 안 되어 있다면 [여기서](https://nodejs.org/en) 파일을 받는다.

![npm 설치](/assets/images/react-study/231112_install_npm.png)

## Back-end 프로젝트 생성

다시 VS Code로 돌아와서 백엔드 프로젝트를 생성할 것이다.

(1) '`command⌘`+`shift⇧`+`p`' => 'Spring Initializr: Create a Gradle Project' 선택

![back-end-01](/assets/images/react-study/231112_spring_init.png)

(2) Spring Boot 버전 선택: 개발 환경에 맞는 버전으로 선택 (ex. `2.7.17`)

![back-end-02](/assets/images/react-study/231112_init01.png)

(3) 개발 언어 선택: **Java**

![back-end-03](/assets/images/react-study/231112_init02.png)

(4) GroupId 입력 => ArtifactId 입력 (자유롭게)

![back-end-04](/assets/images/react-study/231112_init03.png)

![back-end-05](/assets/images/react-study/231112_init04.png)

(5) 패키지 타입 선택: **Jar**

![back-end-06](/assets/images/react-study/231112_init05.png)

(6) Java 버전 선택: 개발 환경에 맞는 버전으로 선택 (ex. `8`)

![back-end-07](/assets/images/react-study/231112_init06.png)

(7) dependencies 선택: **Spring Web**, **Thymeleaf**

![back-end-08](/assets/images/react-study/231112_init07.png)

그리고 경로까지 선택하면 프로젝트 생성이 완료된다.<br>
주의점으로 Spring Boot 버전과 Java 버전을 선택할 때는 꼭 본인의 컴퓨터 개발 환경에 맞는 버전으로 잘 선택해야 한다. 안 그러면 프로그램 실행이 안 된다.

### Back-end 프로젝트 실행

`src/main/` 안으로 가서 `~~Application.java` 파일을 열면 main문 위에 `Run`/`Debug` 버튼이 있는 것을 확인할 수 있다.

![back-end-java](/assets/images/react-study/231112_backend_app.png)

`Run` 버튼을 눌러서 터미널 창이 아래 이미지와 같이 보이면 프로젝트가 잘 실행되고 있는 것이다.

![back-end-java](/assets/images/react-study/231112_backend_run.png)

또한 웹 브라우저에 'localhost:8080'을 치면 다음과 같이 에러 페이지가 뜬다.
여기서 에러 페이지가 뜨는 이유는 아직 아무런 페이지도 매핑(mapping)하지 않았기 때문이다.

![back-end-browser](/assets/images/react-study/231112_backend_browser.png)
