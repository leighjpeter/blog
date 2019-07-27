---
title: "SSH免秘登录配置"
date: 2019-07-26T18:05:16+08:00
draft: false

keywords: ["ssh","免密登录","github","gitlab"]
description: "ssh，免密登录"
tags: ["SSH"]
categories: ["Dev"]
author: "leighj"
url: /2019/07/26/ssh免密登录配置.html

weight: 10

---

> Github/Gitlab SSH免密登录配置，多版本仓库配置

<!--more-->

# git 客户端配置（不需要输入密码和多仓库配置）

本操作在linux下进行，**一定要用git协议，不要用http协议来访问仓库。**

## 1.生成密钥
~~~
$ ssh-keygen -t rsa -C "要生成的密钥的说明"
Enter file in which to save the key (/root/.ssh/id_rsa): [输入密钥保存的位置]（这里直接回车，默认保存到当前帐号的home目录的.ssh目录中。如果不用默认的需要设置.ssh/config才能访问git）
Enter passphrase (empty for no passphrase): [回车]（如果输入了密码，使用git时就要密码了，所以直接回车）
Enter same passphrase again: [回车]
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
41:d6:e3:65:2e:68:a0:13:ee:bb:b4:dc:06:1a:74:c1 xxxx....
The key's randomart image is:
+--[ RSA 2048]----+
|   .    o.       |
|    E .o  o o    |
|   . + ..o =     |
|  . =   o.o .    |
| . o . .S  .     |
|  . o            |
|   o.o           |
|  .o.o.          |
|    +o.          |
+-----------------+
~~~
## 2.设置版本仓库帐号的密钥

### 输出公钥内容：

~~~
$ cat ~/.ssh/id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCmKEdUk7dLeHC0ngZUk8bhtQbsmLYa............
~~~
    1. 登录 http://gitlab.leighj.com
    2. 进入页面 Profile Settings -> SSH Keys 将上边输出的公钥内容复制到页面上的 Key 文本框中，保存。

## 3.测试
~~~
$ ssh -T git@gitlab.leighj.com
Welcome to GitLab, leighj!
~~~
成功。

-----

如果输出是：

~~~
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0777 for '/root/.ssh/id_rsa' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
bad permissions: ignore key: /root/.ssh/id_rsa
Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password,keyboard-interactive).
~~~

**则需要将文件: /root/.ssh/id_rsa 的权限设置为 0666**

-----

以上完毕后，可以不用输入密码使用git了

~~~
$ git clone git@gitlab.leighj.com:ERP/aggra.git
~~~

-----
# 多版本仓库的配置

每个仓库需要生成一个密钥，ssh具体要访问哪个仓库，需要给ssh增加一个config配置文件用于指明。

1.生成密钥，要指定名字，否则会将原有默认文件覆盖掉。

~~~
$ ssh-keygen -t rsa -C "要生成的密钥的说明"
Enter file in which to save the key (/root/.ssh/id_rsa):/root/.ssh/aggra (这里指明生成的密钥的文件名)
接下来的密码输入直接回车，过掉。之后会生成两个文件：
aggra
aggra.pub
~~~

2.配置ssh

~~~
$ cd ~/.ssh/
$ vi config (这个文件不存在，需要自己添加)

~~~

config文件内容：


~~~
Host git-aggra (ssh要用到的主机名)
    Port 22 （端口号）
    User xxx （git用户名）
    HostName gitlab.leighj.com (git的真实主机地址)
    PreferredAuthentications publickey （验证方式）
    IdentityFile ~/.ssh/aggra （私钥文件）
~~~


**注意一点，设置了Host之后。只能用Host设置的值进行访问了。**


没有设置config的情况可以这样访问：

~~~
$ git clone git@gitlab.leighj.com:ERP/aggra.git
~~~

设置config后就必须这样访问：

~~~
$ git clone git@git-aggra:ERP/aggra.git
~~~

将本地公钥重定向追加到远程文件authorized_keys的末尾

~~~
ssh user@host 'mkdir -p .ssh && cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub
~~~




