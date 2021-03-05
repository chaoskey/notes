---
title: "安卓手机上的Git服务器"
date: 2021-03-05T10:32:20+08:00
draft: true
categories: ["杂事"]
tags: ["git", "termux"]
---

## 缘由

今天，收到阿里云的密钥泄露通知短信：密钥被公开到Github代码库中。　可我明明是私有库呀！！　　吓得我立刻将Github和Gitee的私有库删除，　私有库不安全呀。

通过一番网上搜索，我选择了用手机充当Git服务器的方案，对私有库而言，好处就不用我多说了。

此外，此方案具有一般性，本文仅仅以Ｇｉｔ为例，其实完全可以充当其他特定功能的服务器。

<!--more-->

![[202103031_ali.jpg]]


## 第一步：安装安卓终端模拟器Termux

官网下载：https://termux.com/　，然后安装到手机。

> 可以选择：F-Droid下载，但无需下载F-Droid，后面有termux的直接下载链接。

## 第二步：Termux 镜像设置

清华源：https://mirrors.tuna.tsinghua.edu.cn/help/termux/

```shell
# 手机上termux中执行
sed -i 's@^\(deb.*stable main\)$@#\1\ndeb https://mirrors.tuna.tsinghua.edu.cn/termux/termux-packages-24 stable main@' $PREFIX/etc/apt/sources.list
sed -i 's@^\(deb.*games stable\)$@#\1\ndeb https://mirrors.tuna.tsinghua.edu.cn/termux/game-packages-24 games stable@' $PREFIX/etc/apt/sources.list.d/game.list
sed -i 's@^\(deb.*science stable\)$@#\1\ndeb https://mirrors.tuna.tsinghua.edu.cn/termux/science-packages-24 science stable@' $PREFIX/etc/apt/sources.list.d/science.list

apt update && apt upgrade
```

## 第三步：安装Git及必要软件

```shell
# 手机上termux中执行
pkg install git openssh vim termux-auth
```

## 第四步：访问外置SD卡或内部存储授权

```shell
# 手机上termux中执行
termux-setup-storage
```

说明：　这个命令会在home目录下，生成一个storage目录（如图），此目录中的子目录`external-1`对应外置SD卡中的一个特定目录。就我的手机而言， 这个特定目录是：　`外置SD卡/Android/data/com.termux/files/`。　

然后，在home目录，创建软链接指向你外置SD卡中的Git库。【避免路径过长】

![[20210303_termux_dir.jpg]]


## 第五步：手机Git的服务仓库

```shell
# 创建Git服务仓库（建议外置存储卡上，具体位置参考第四步的图）
git init --bare  private.git

# 在home目录中，创建目录软链接
ln -s <全路径>/private.git  private.git
```

## 第六步：配置sshd服务

PC 端生成公私钥，

```shell
# PC中执行
ssh-keygen
```

把生成的公钥`~\.ssh\id_rsa.pub` 拷贝到手机的`~\.ssh` 文件夹中。然后，

```shell
# 手机上termux中执行
cat id_rsa.pub > authorized_keys

# 修改为：PasswordAuthentication no
vim $PREFIX/etc/ssh/sshd_config
```

获取必要的信息

```shell
# 查看用户名（记住）
whoami

# 查看ip地址（记住）
ifconfig
```

## 第七步：sshd服务启动、停止

```shell

# 指定端口启动
sshd -p 9876

# 停止 ssh 服务，只需要 kill 杀掉进程即可
pkill sshd

# 查看sshd的日志
logcat -s 'syslog:*'
```


## 第八步：在ＰＣ端克隆

```shell
# u0_a133 前面查询到的用户名
# 192.168.1.102 前面查询到的ip
# 9876 前面指定的端口
# ~ 代表手机termux中的home目录
git clone ssh://u0_a133@192.168.1.102:9876/~/private.git
```

## 写在后面

- 为安全起见，建议临时用临时启动sshd。
- IP可能会变，clone & pull & push 前可能要修改Git客户端配置。
- 可以从ＰＣ登陆手机终端

```shell
# 如下图
ssh u0_a133@192.168.1.102 -p 9876

```

![[20210303_powershell.png]]
