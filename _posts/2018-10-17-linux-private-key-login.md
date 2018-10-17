---
layout: post
title: "linux中使用私钥免密登录"
categories: ["运维经验"]
tags: ["命令行"]
---

![linux](http://qiniu.mcgoldfish.com/image/1/fohDrs8IXGG7JVCXn9V4lNvjo1fIR130XkC4gUvG.jpeg)

## 密钥登录原理

> 利用密钥生成器制作一对密钥（公钥和私钥），将公钥添加到目标服务器上的对应账户，然后在源服务器使用密钥登录目标服务器

## 生成密钥对

使用 `ssh-keygen`命令生成密钥对

``` bash
root@ck01:~# ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:We0qOEEYfTkOZsACqxZ5Jx4GvQ1/ikPBpViYKyjWBRg root@ck01
The key's randomart image is:
+---[RSA 2048]----+
|.EB+=+   .       |
| =*=o+= +  .     |
|o+oBO+.+ .. .    |
|=o=+++ ..o .     |
|=.... + S   .    |
|.  o . o   .     |
|    . o . .      |
|       . .       |
|                 |
+----[SHA256]-----+

```

1. 可以指定私钥存放的文件和目录（Enter file in which to save the key）
2. 可以给私钥文件设置密码 （Enter passphrase / Enter same passphrase again）


## 将公钥复制到目标服务器

使用 `scp`命令远程复制

``` bash
scp /root/.ssh/id_rsa.pub root@host:/tmp/  # 复制到临时目录
```

## 登录到目标服务器，进行设置

### 密钥文件

``` bash
mkdir ~/.ssh   # 如果不存在.ssh目录，则新建一个
cd ~/.ssh
cat id_rsa.pub >> authorized_keys # 将公钥写入指定文件
chmod 600 authorized_keys   # 给予相应权限
chmod 700 ~/.ssh
```

### 打开 /etc/ssh/sshd_config 设置密钥登录

```
RSAAuthentication yes
PubkeyAuthentication yes
PermitRootLogin yes
PasswordAuthentication no  # 切记，当密钥登录成功了，再设置禁止密码登录
```

### 重启ssh

``` bash
service sshd restart
```


## 结束

使用密钥登录服务器，不仅让我们的服务器更加安全了，因为密码登录，有被暴力破解的风险，而且在一些自动化的运维场景，也都是使用密钥免密登录的