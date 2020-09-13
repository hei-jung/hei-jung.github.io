---
title: "Linux Basics"
categories: linux raspberry-pi
toc: true
#toc_sticky: true
header:
  overlay_image : /assets/images/header_linux.jpg
  overlay_filter: 0.3
---

# 리눅스 기본

## 1. 리눅스 부팅 과정

참고. [[Linux] Booting Sequence](https://talkingaboutme.tistory.com/entry/Linux-Booting-Sequence)<br>
참고. [6 Stages of Linux Boot Process (Startup Sequence)](https://www.thegeekstuff.com/2011/02/linux-boot-process/)<br>
참고. [The Booting Process](https://www.vskills.in/certification/tutorial/it-support/the-booting-process/)<br>
참고. [The Five Steps of the Boot Sequence](https://www.techwalla.com/articles/the-five-steps-of-the-boot-sequence)<br>

```
(1) 부트 코드 실행: 부팅. OS 초기화.
(2) 전력공급
(3) 메모리 체크(무결성 검사)
(4) 스타터 코드(CPU가 higher level language를 알아들을 수 있도록 하는 코드)로 jump
(5) 부트로더(bootloader)를 통해서 메모리 상에 커널을 올림
```

## 2. 터미널 명령어

명령어|용도
-----|-----
su 아이디|사용자를 아이디로 변경
su|사용자를 root로 변경
sudo 명령어|root 권한으로 명령어 실행
파일명 일부 + `Tab`키|자동완성
ls|디렉토리 내부에 있는 파일 목록(dir 비슷한 역할)
rm|파일을 삭제(cf. 중복파일 .swp가 생기면 sudo rm .파일명.swp 해서 삭제)
`-f` 붙이면|강제
cd|디렉터리 이동
`~`|home 디렉토리를 의미
`/`|root 디렉토리를 의미
bin|명령어 실행 프로그램(ls, rm, etc.)을 저장해둔 디렉토리
echo|네트워크 잘 되는지 안 되는지 확인하는 데 쓸 수 있음
tmp|임시 파일이 저장되는 디렉토리
mnt|다른 파일 시스템을 갖다붙임 (cf. 파티션을 나누는 이유: 하나의 OS에 서로 다른 파일 시스템을 두고 쓰기 위함)
boot|OS를 부팅시키는 파일 모음
mkdir|디렉토리 생성
mv 파일명1 파일명2|파일1을 파일2로 이동
cp 파일명1 파일명2|파일1을 파일2로 복사
cat 파일명|파일 안에 있는 내용을 터미널에 출력

## 3. vi 명령어

명령어|용도
-----|-----
vi 파일명|파일 열어줌 (없으면 새로 생성)
sudo vi 파일명|앞에 sudo를 붙여줘야 저장이 됨
`i` 또는 `a`|입력 모드 (차이: i는 현재 커서 위치에서 입력모드로 전환, a는 현재 커서 다음 위치에서 입력모드로 전환)
`Esc`키|입력 모드에서 나감
`:`|명령 모드로 돌아감
`:w`|저장
`:q`|나감
`:wq`|저장하고 나감
`:q`|현재 파일에서 나감. 내용이 변경되지 않았을 때 그냥 나감
`:q!`|내용이 변경되었지만 저장 안 하고 나감
x|현재 커서에 있는 한 글자 삭제
dd|현재 커서에 있는 한 줄 삭제
10dd|현재 커서에서부터 10줄 삭제 (ndd는 n줄 삭제)
yy|현재 커서에 있는 한 줄을 복사 (Ctrl+C 역할)
10yy|현재 커서에서부터 10줄 복사 (nyy는 n줄 복사)
p|커서 위치에 yy로 복사해둔 내용을 붙여넣음 (Ctrl+V 역할)
u|undo (Ctrl+Z 역할)
r|redo

## 4. 리눅스 구조

```
(상위)
app
system call
kernel
H/W
(하위)
```

- `system call` 은 커널과 애플리케이션 사이를 잇는 연결 다리 같은 것으로, 커널을 요청하는
함수들의 묶음이다.<br>
  → 소프트웨어 interrupt에 해당!
- `커널`에서:: OS에서 프로세스는 현재 실행 중인 프로그램을 의미함.
  - PM은 프로세스를 관리, MM은 메모리를 관리<br>
    → 가상의 논리 주소와 물리 주소를 mapping 해서 관리.
- vfs: 작성 파일 시스템.
  - 리눅스의 큰 장점! 파일의 포맷에 상관없이 모든 파일을
표준화시켜서 작업할 수 있음. 어떤 파일이든 일반 파일처럼 쓸 수 있다.
  - cf. 리눅스의 또다른 장점: OS가 실행중일 때도 모듈 업로드가 가능하다. 리눅스 커널을 처음부터 다시 컴파일할
필요가 없어서 매우 편리!
  - 단, 문제점은 재부팅하면 파일이 없음. 프로그램을 실행 중일 때 유효하다.)
  
## 5. 터미널에서 프로그래밍하기

### Java

> 자바 설치

```
java apt-get install openjdk-8-jdk # jdk 설치
```

> 자바 버전 확인

```
java -version # 버전이 뜨면 잘 설치 된 것.
```

### C

> C 버전 확인

: 자바와 마찬가지로 버전이 뜨면 잘 설치 된 것.

![1](https://user-images.githubusercontent.com/40985307/93011701-7353d580-f5d3-11ea-9dcf-63b3784de0ba.png)

> 컴파일 및 실행 명령어

![2](https://user-images.githubusercontent.com/40985307/93011702-75b62f80-f5d3-11ea-904e-ef6b45143531.png)

![3](https://user-images.githubusercontent.com/40985307/93011704-76e75c80-f5d3-11ea-8b53-0bed94e36072.png)
