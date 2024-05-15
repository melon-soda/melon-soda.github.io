---
title: GitHub ssh 접속 설정
date: 2024-05-15 15:20:00 +0900
categories: [GitHub]
tags: [github, ssh]
---

## Github SSH?

평소에 GitHub에서 clone하는 경우 https만을 사용하여 진행했었다. 그러던 중 문득 옆에 있는 SSH는 어떻게 사용하는 것인지 궁금해졌다.

## 설정 방법

### SSH key pair 생성

`ssh-keygen`을 이용하여 `Ed25519`형식의 key pair를 생성한다.

{: .prompt-info }

> 아래 key는 예시로, 생성 후 바로 삭제하였다.

```bash
$ ssh-keygen -t ed25519 -C "github_email@github.com"
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/soda/.ssh/id_ed25519): /home/soda/.ssh/github
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/soda/.ssh/github
Your public key has been saved in /home/soda/.ssh/github.pub
The key fingerprint is:
SHA256:3684xHX7sspr2R4f7Ir7UXTkFtQ5tCa2S7cm6JlXGxU github_email@github.com
The key's randomart image is:
+--[ED25519 256]--+
|              o++|
|               E+|
|             o +*|
|            o *.o|
|        S. . + +.|
|         .o.o *o.|
|         ....*o*o|
|          oo*oB+o|
|          .OOB=+.|
+----[SHA256]-----+
```

위에서 출력된 경로에 가면 다음과 같이 2개의 파일을 확인 할 수 있다.

- github
- github.pub

이 중 `github`의 경우 private key에 해당하며, `github.pub`의 경우 public key에 해당한다. 우리는 GitHub에 접속을 위해 public key를 계정에 추가해주면 된다.

{: .prompt-tip}

> MacOS의 경우 `pbcopy`, linux의 경우 `xclip`을 사용하여 클립보드에 복사 가능하다.

### GitHub에 public key 등록

`Settings` > `Access` > `SSH and GPG keys` 메뉴로 들어가면 우측 상단에 `New SSH key`라는 버튼이 존재한다. 클릭해서 들어가면 다음과 같은 화면을 볼 수 있다.

![Add new ssh key](/assets/posts/2024-05-15-GitHub_ssh_setting/add-new-ssh-key.png)

위에서 복사한 public key를 Key 섹션에 붙여넣어준다. 동일한 방식으로 키를 만들었다면 다음과 같은 형식으로 되어있다.

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIF5CspyL3snW9+lyjh5D1lj0L29YUS0/f6qkZ6nxstrN github_email@github.com
```

### ssh-agent에 private key 등록

ssh-agent를 실행한다.

```bash
$ eval "$(ssh-agent -s)"
Agent pid 6613
```

`~/.ssh/config`에 다음 내용을 추가한다.

```
Host github.com
  AddKeysToAgent yes
  IdentityFile ~/.ssh/github
```

마지막으로 private key를 ssh-agent에 추가한다.

```bash
$ ssh-add ~/.ssh/github
Identity added: /Users/user_name/.ssh/github (github_email@github.com)
```

### 접속 테스트

정상적으로 설정이 끝났다면 다음과 같이 결과를 확인할 수 있다.

```bash
$ ssh -T git@github.com
Hi github_username! You've successfully authenticated, but GitHub does not provide shell access.
```

만약 다음과 같이 표시된다면 설정 과정 중 문제가 있거나 `ssh-agent`가 실행중이지 않을 수 있다.

```bash
git@github.com: Permission denied (publickey).
```
