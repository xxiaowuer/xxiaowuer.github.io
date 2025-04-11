---
title: Git clone失败解决方法
date: 2024-03-09 15:28:22 +0800
categories: [CS, Git]
tags: [Git]     # TAG names should always be lowercase
---

## 问题描述

使用`git clone`时出现下面的报错：

``` 
xiaowu@DESKTOP-FE78O6H MINGW64 /e/blog (master)
$ git clone git@github.com:xxiaowuer/xxiaowuer.github.io.git
Cloning into 'xxiaowuer.github.io'...
kex_exchange_identification: Connection closed by remote host
Connection closed by 198.18.0.143 port 22
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

## 原因分析

通过查阅相关解决方法，发现可能是我开了代理的原因

## 解决方法

1. 关闭代理

2. 改用HTTPS协议，走443端口

   在 `~/.ssh/config` 文件中添加下面的配置即可，之后就可以正常使用 `git pull` 等操作了

   ```
   Host github.com
       Hostname ssh.github.com
       Port 443
       User git
   ```

## 参考教程

1. [Github官方教程](https://docs.github.com/zh/authentication/troubleshooting-ssh/using-ssh-over-the-https-port)
2. [大佬博客](https://blog.csdn.net/CoolBoySilverBullet/article/details/135736862) 

