---
title: 우분투 SSH key 로그인
author: Hana Lee
categories: [System, Ubuntu]
tags: [system,ubuntu,linux,ssh,auth]
math: false
mermaid: false
pin: true
toc: true
image:
  src: \assets\img\posts\2021-04-22-linux-pub-key-auth\cyberark_ssh_-thumbnail.png
---

## 개요
---
Ubuntu 혹은 CentOS, ReadHat 같은 리눅스 시스템에 ssh 를 이용하여 접속 할 일이 자주 있다.

매번 접속 할 때 마다 리눅스 설치시 설정한 비밀번호를 입력하여 접속을 하게된다.

이때 매번 접속시 비밀번호가 너무 길거나, 매번 입력하는게 불편하게 느껴질 수 있다.

이를 좀 더 편하게 해주는게 ssh-keygen 으로 생성한 public key 와 private key 를 사용하는 방법이다.

리눅스 서버에 public key를 등록하고 쌍을 이루는 private key를 이용하여 접속하게 하는것이다.

아래에서 설정 방법에 대해 설명을 한다.

## ssh key 생성
---
1. 터미널이나 cmd 혹은 powershell 을 실행한다.
1. 아래의 내용을 복사 붙여넣기 한다. `your_email@example.com` 에 자신의 이메일 주소로 변경해준다.
```shell
# 리눅스에서 생성시
# -t 는 타입. (dsa | ecdsa | ed25519 | rsa | rsa1) 중 하나를 선택 할 수 있다. 기본값은 rsa
# -b 는 bit 수. 최소 768 비트, 기본값은 2048 비트이다.
# -C 는 코멘트.
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
```powershell
# 윈도우즈에서 생성시
ssh-keygen.exe -t rsa -b 4096 -C "your_email@example.com"
```
1. 아래와 같은 메시지가 나오면 엔터를 입력하여 기본 디렉토리에 저장되도록 한다.
```
Enter a file in which to save the key (/c/Users/you/.ssh/id_ed25519):[Press enter]
```
1. 이후 생성될 public key 와 private key 의 암호를 입력하게 되는데 필수는 아니다.
```
Enter passphrase (empty for no passphrase): [Type a passphrase]
Enter same passphrase again: [Type passphrase again]
```
1. 이후 유저의 홈디렉토리로 이동하면 .ssh 디렉토리가 생성되고 내부에 `id_rsa`, `id_rsa.pub` 두개의 파일이 생성된다. 기존에 이미 해당 파일이 존재 하고 있다면 덮어 씌우기가 되므로 주의를 한다.
1. 리눅스 시스템에서는 해당 public key 와 private key 의 permission 을 수정해줘야 한다.
```shell
chmod 600 id_rsa*
```

## 리눅스 시스템 설정
---
1. 위의 과정에서 생성된 `id_rsa.pub` 파일을 편집기를 이용하여 내부의 내용을 복사 한다.
```
#파일 내용은 아래처럼 생겼다.
ssh-rsa AAAAB3NzaC1yc2EAAAADA...생략
```
1. 리눅스 시스템에 비밀번호를 이용하여 접속한다.
1. 리눅스 시스템의 홈 디렉토리 아래에 `.ssh` 디렉토리로 이동한다.
```shell
cd ~/.ssh
```
1. 없을 경우 `.ssh` 디렉토리를 만들어준다.
```shell
mkdir ~/.ssh
```
1. .ssh 디렉토리 내부에 `authorized_keys` 파일이 없다면 새로 만들어주고 존재 한다면 `vim` 또는 `nano` 와 같은 편집기로 해당 파일을 열어준다.
1. 위에서 복사한 `id_rsa.pub` 파일의 내용을 붙여넣기한다.
1. 저장 후 ssh 서비스를 재시작한다.
   ```shell
   sudo service ssh restart
   # 또는
   sudo systemctl reload ssh
   ```
1. 기본적인 서버의 준비는 완료되었다. 이제 내 피시에서 접속해보자.
   ```shell
   # .ssh 디렉토리로 이동한다.
   ssh -i id_rsa ubuntu@server_ip
   ```
1. 비밀번호 입력 없이 접속이 되는것을 확인 할 수 있다. 
   
   추가로 -i 옵션을 제외하면 비밀번호를 이용하여 접속도 할 수 있다. 추후 private key 분실시를 대비하여 비밀번호 옵션을 수정하지 않았다.
