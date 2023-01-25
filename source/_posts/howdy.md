---
title: Howdy - Linux 的人脸识别
tags: [Linux, 教程]
abbrlink: 53425
date: 2020-10-02 19:49:33
---

# 前言的前言

嗨呀好久没更新了呢

时逢 8 天长假

懒得更视频就先来更个博客好了

# 前言

这几天逛 Github 的时候看到了个有意思的东西: [Howdy](https://github.com/boltgolt/howdy)

![Howdy on Github](https://s1.ax1x.com/2020/10/02/0lWMAf.png)

# 简介

README 里的介绍是这样的: 

> Howdy provides Windows Hello™ style authentication for Linux. Use your built-in IR emitters and camera in combination with facial recognition to prove who you are.
> 
> Using the central authentication system (PAM), this works everywhere you would otherwise need your password: Login, lock screen, sudo, su, etc.

翻译过来大概是这个意思:

> Howdy 为 Linux 提供了 Windows Hello™ 风格的认证。使用内置的红外发射器和摄像头，结合面部识别来证明你是谁。
> 
> 使用中央认证系统（PAM），这意味着在任何需要密码的地方都可以使用 Howdy。包括登录、锁屏、`sudo`、`su`等。

虽说是需要红外发射器

但实际上并不需要红外发射器

整一个摄像头就可以有用了

红外发射器只是用于夜间使用

# 安装

<div class="warning">

> 本教程似乎仅适用于 Arch Linux

</div>

```shell
yay -S howdy
```

是的没错，Howdy 在 AUR 里面

所以可以直接通过 yay 进行安装

# 配置

## 先前准备

### 硬件

既然是人脸识别

那肯定需要用到摄像头

去 tb 随便找个 30 包邮 USB 摄像头就能用

如果你想要夜间认证那可以选择红外摄像头

### 软件

#### 检测摄像头设备

请确保摄像头正常工作

可以使用 `Cheese` `VLC` 等应用访问摄像头查看工作状况

如果确保摄像头工作正常

那可以确认摄像头的设备名

我这里使用 `v4l2-ctl` 进行确认

```shell
sudo pacman -S v4l-utils        #没有 v4l-utils 先安装
v4l2-ctl --list-devices            #列出所有的摄像头设备
```

![](https://s1.ax1x.com/2020/10/03/0337FK.png)

如果有多个摄像头设备以 `/dev/video0` 为准

#### 修改 howdy config

```shell
sudo howdy -U <username> config            #将<username>更改为你的用户名，以下都这么用
```

默认会使用 `nano` 打开 `config.ini`

![sudo howdy -U chitang config](https://s1.ax1x.com/2020/10/03/03841g.png)

你可以在这里面修改一些设置

所有选项都用注释来说明作用了

我们主要找的这个

![](https://s1.ax1x.com/2020/10/03/038j9U.png)

将 `device_path = ` 后面的内容更改成你的摄像头设备地址

#### 增加面部

通过以下命令即可将面部注册到 Howdy 中

```shell
sudo howdy -U <username> add
```

在正式开始录入面部前会要求输入一个 25 字以内的名字

用于标记面部

![面部录入](https://chitangcos.zyglq.cn/images/howdy/face-add.png)

#### Howdy 用法

| 命令         | 描述              |
| ---------- | --------------- |
| `add`      | 给一个用户增加一个新的面部模型 |
| `clear`    | 给一个用户删除所有的面部模型  |
| `config`   | 在你的默认编辑器中打开配置文件 |
| `disable`  | 禁用或启用 Howdy     |
| `list`     | 列出一个用户的所有面部     |
| `remove`   | 给一个用户移除选定的面部    |
| `snapshot` | 给你的摄像头整个截图      |
| `test`     | 测试摄像头           |
| `version`  | 输出当前版本号         |

## 使 Howdy 能够用于 sudo 的认证

刚才说过了，Howdy 使用 PAM 认证系统

所以我们现在得去设置 PAM

更改 `/etc/pam.d/sudo` 中的文件

在它的第一行加上这么一句

```
auth    sufficient    pam_python.so    /lib/security/howdy/pam.py
```

![我的 /etc/pam.d/sudo](https://chitangcos.zyglq.cn/images/howdy/config.png)

接着最好重启下电脑

尝试一下再用 sudo 干点事

![芜湖起飞](https://chitangcos.zyglq.cn/images/howdy/use.png)
