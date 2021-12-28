---
title: "[Linux] Linux Cheatsheet"
categories: linux
tags: linux ubuntu
toc: true
toc_sticky: true
---

<!--
template
```console
(base) user@device:~$
```
-->

# Linux Cheatsheet

## 7zip (7zr, 7za) command

```console
(base) user@device:~$ sudo apt install p7zip p7zip-full
```

zip:

```console
(base) user@device:~$ 7zr a ZIPPED_FILENAME.7z FILENAME
```

unzip:

```console
(base) user@device:~$ 7zr x ZIPPED_FILENAME.7z
```

zip with password:

```console
(base) user@device:~$ 7za a -pPASSWORD ZIPPED_FILENAME.7z FILENAME
```

unzip with password:

```console
(base) user@device:~$ 7za x -pPASSWORD -y ZIPPED_FILENAME.7z
```


## Find the physical memory available

```console
(base) user@device:~$ grep MemTotal /proc/meminfo
```


## Delete user

check user:

```console
(base) user@device:~$ ls -la /home |grep USERNAME
```

delete only user:

```console
(base) user@device:~$ sudo userdel USERNAME
```

delete user with home directory:

```console
(base) user@device:~$ sudo userdel -rf USERNAME
```

`-r`: delete all data including the home directory when deleting user<br>
`-f`: force option


## Clear bash history

[How to clear bash history completely?](https://askubuntu.com/questions/191999/how-to-clear-bash-history-completely)

```console
(base) user@device:~$ cat /dev/null > ~/.bash_history
```

Other alternate way is to link `~/.bash_history` to `/dev/null`

avoiding memory copy:

```console
(base) user@device:~$ cat /dev/null > ~/.bash_history && history -c && exit
```

