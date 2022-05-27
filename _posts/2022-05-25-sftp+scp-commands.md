---
title: "[Linux] sftp, scp 파일 명령어"
subtitle: "파일 및 폴더(디렉토리) 다운로드, 업로드"
categories: linux
tags: linux
toc: true
toc_sticky: true
---

sftp와 scp 명령어를 알아보자

## sftp 기본

원격 호스트로 로그인 먼저 해놓기:

```console
$ sftp [-P 포트번호] user@원격IP
```

## scp 기본

기본 명령어 틀:

```console
$ scp [OPTION] [user@]SRC_IP:]SRC_ROUTE [user@]DST_IP:]DST_ROUTE
```

### Options

|Option|Description|
|:--:|:------------------------------|
|`-P`|원격 호스트의 포트번호 (대문자임에 유의)|
|`-p`|파일 수정 및 액세스 시간 보존|
|`-r`|파일을 재귀적으로 복사 (디렉토리 전송 시 사용)|

## Download

```console
# sftp
> get [-r] 받을파일

# scp
$ scp [-P 포트번호] [-r] user@원격IP:받을파일 저장할경로
```

## Upload

```console
# sftp
> put [-r] 전송할파일

# scp
$ scp [-P 포트번호] [-r] 전송할파일 user@원격IP:경로
```
