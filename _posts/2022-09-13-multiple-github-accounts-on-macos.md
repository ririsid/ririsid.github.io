---
layout: post
title: 맥에서 다중 GitHub 계정 사용하기
date: 2022-09-13 22:10:07 +0900
tags: macOS github ssh
---

나는 두 개의 GitHub 계정을 사용한다. 개인 계정과 회사 계정이다.

처음에는 ssh-agent 등록을 통해 구분하려고 했다. 그런데 이게 귀찮고 햇갈리는 일이다.

다른 방법을 찾아보니 SSH _config_ 파일을 이용해 구분하는 방법이 있었다.

여기서는 개인 계정을 기본 계정으로 사용하고, 회사 계정으로 일부 저장소를 사용한다고 가정하겠다.

여기서 모든 SSH 키 사용에 관한 모든 내용을 다루지 않으므로 SSH 키 사용 경험이 없거나 무언가 문제가 생긴다면 GitHub의 문서를 참고하며 진행하자.

[Generating a new SSH key and adding it to the ssh-agent - GitHub Docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

### 기본(개인) SSH 키 등록

우선 기본 SSH 키를 생성한다.

```Bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

기본 설정을 따르면 *id_ed25519*라는 키가 만들어졌을 것이다.

다음은 ssh-agent를 백그라운드로 실행한다.

```Bash
eval "$(ssh-agent -s)"
```

`~/.ssh/config` 파일을 수정(혹은 생성)한다.

```plaintext
Host *
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519
```

**GitHub 문서에서는 위와 같이 되어 있지만 이렇게 하면 모든 호스트(`Host *`)가 기본 SSH 키를 사용하게 되므로 이후 추가할 회사용 SSH 키를 구분할 수 없게 된다.**

따라서 `~/.ssh/config` 파일을 다음과 같이 수정한다.

```plaintext
Host github.com
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519
```

`Host`를 \*에서 *github.com*으로 바꾸었다.

GitHub의 저장소는 호스트가 *github.com*이므로 이렇게 설정하면 모든 GitHub 저장소에 기본 SSH 키가 적용된다.

ssh-agent에 기본 SSH 키를 추가한다.

```Bash
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

다음 문서를 참고해 개인 GitHub 계정에 SSH 키를 추가한다.

[Adding a new SSH key to your GitHub account - GitHub Docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

SSH 연결을 확인한다. [[참고](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection)]

```Bash
ssh -T git@github.com
```

다음과 같은 메시지가 나오면 성공이다.

> Hi _username_! You've successfully authenticated, but GitHub does not provide shell access.

### 회사용 SSH 키 등록

회사용 SSH 키를 생성한다.
여기서는 임의로 _work_id_ed25519라 하겠다._

```Bash
ssh-keygen -t ed25519 -C "your_company_email@example.com" -f "work_id_ed25519"
```

`~/.ssh/config` 파일을 수정한다.

```plaintext
Host github.com
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519

Host example.com
  HostName github.com
  User git
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/work_id_ed25519
```

회사용 설정을 추가했는데 `Host`가 *example.com*이고, `HostName`과 `User`도 추가됐다.

`Host`의 *example.com*은 구분을 위한 임의의 값으로 실제 사용하는 호스트일 필요는 없다. *github.com*만 아니면 된다. 내 경우 회사 도메인을 사용했다.

`HostName`과 `User`는 SSH 연결을 확인할 때 설명하도록 하겠다.

ssh-agent에 회사용 SSH 키를 추가한다.

```Bash
ssh-add --apple-use-keychain ~/.ssh/work_id_ed25519
```

개인 GitHub 계정에 그랬던 것처럼 회사 GitHub 계정에도 SSH 키를 추가한다.

[Adding a new SSH key to your GitHub account - GitHub Docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

이제 SSH 연결을 확인한다.

[Testing your SSH connection - GitHub Docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection)

중요한 건 호스트가 *example.com*이라는 것이다.

```other
ssh -T git@example.com
```

**이렇게 SSH 연결을 요청하면 실제로는 _config_ 파일에서 `Host`가 *example.com*인 설정에 따라 *git@github.com*으로 _work_id_ed25519_ 키를 사용해서 연결하게 된다.**

다음과 같은 메시지가 나오면 성공이다.

> Hi _company_username_! You've successfully authenticated, but GitHub does not provide shell access.

### 회사 저장소 주소 바꾸기

마지막으로 회사 저장소의 원격지(remote) 주소를 바꾸어주어야 한다.

앞서와 마찬가지로 호스트를 *github.com*에서 *example.com*으로 바꾸자.

```Bash
git remote set-url origin git@example.com:YourCompany/repository.git
```

이제 `git fetch`를 해서 오류가 발생하지 않으면 모든 과정이 끝난 것이다.

### 정리

SSH는 _config_ 파일의 `Host` 설정에 따라 적용할 SSH 키를 지정할 수 있고, 임의의 호스트도 설정에 따라 원래의 호스트로 연결할 수 있다.

이를 이용하면 몇 개의 계정을 사용하든 임의의 호스트를 설정해 호스트에 따라 원하는 SSH 키로 GitHub에 연결할 수 있다.

---

## 참고

[How to manage multiple GitHub accounts on a single machine with SSH keys](https://www.freecodecamp.org/news/manage-multiple-github-accounts-the-ssh-way-2dadc30ccaca/)
