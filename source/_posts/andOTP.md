---
title: 'andOTP - 开源, 多协议的手机验证器'
tags:
  - Android
  - Open Source
  - Softwares
abbrlink: 25688
date: 2022-04-16 13:38:50
---

## 介绍

> andOTP is a two-factor authentication App for Android 5.1+.
> 
> It implements Time-based One-time Passwords (TOTP) and HMAC-Based One-Time Passwords (HOTP).
> Simply scan the QR code and login with the generated 6-digit code.

andOTP 使用 MIT 协议开源，代码托管在 [GitHub](https://github.com/andOTP/andOTP) 上

## 安装

andOTP 是一个开源软件，可以前往 [GitHub Releases](https://github.com/andOTP/andOTP/releases) 下载，官方也上架了 [F-Droid](https://f-droid.org/packages/org.shadowice.flocke.andotp/) 和 [Google Play](https://play.google.com/store/apps/details?id=org.shadowice.flocke.andotp) (我个人推荐 F-Droid)。

## 添加

### TOTP, HOTP 以及 MOTP

在主界面右下角的加号中提供了三种添加方式

大部分网站会提供一个二维码供扫描，根据需要选择 扫描二维码 和 相册中选择二维码 就好

如果只有提供密钥的话就需要选择 输入详细信息 了

类型选择对应的

签发方及名称可随意，比如我的 Cloudflare 这样填写

![Cloudflare 详细信息](https://chitang-main-1256617490.cos.ap-shanghai.myqcloud.com/images/andOTP/Cloudflare1.jpg)

在主界面看起来会像这样

![Cloudflare Home](https://chitang-main-1256617490.cos.ap-shanghai.myqcloud.com/images/andOTP/Cloudflare2.jpg)

密钥就填网站给的

标签可自己设置，便于区分

高级选项中有周期, 数字, 算法

算法很好理解，周期是指验证码的刷新时间，数字是验证码的位数

如果没有需要最好不要动

### Steam

Steam 验证器比较特殊，只能通过手动输入信息来填写

密钥获取方式也比较特殊

而且手机需要经过 root

使用任何能够访问 `/data` 目录的工具找到这个文件

```
/data/data/com.valvesoftware.android.steam.community/files/Steamguard-<STEAMID>
```

使用任何能够访问它的工具打开，在文件里面能够找到这样一部分

```
"uri":"otpauth:\/\/totp\/Steam:USERNAME?secret=################################&issuer=Steam"
```

在 `secret=` 后面`&issuer` 前面的就是我们所需要的密钥了，将它保存好，然后回到 andOTP

在右下角的添加中选择 输入详细信息

类型选择 `STEAM`

签发方和名称随意

密钥就填写刚刚获取到的

标签随意

高级选项不要动~~你也动不了~~

推荐这样填写

![Steam 详细信息](https://chitang-main-1256617490.cos.ap-shanghai.myqcloud.com/images/andOTP/Steam1.jpg)

在主页就可以正常显示验证码了

![Steam Home](https://chitang-main-1256617490.cos.ap-shanghai.myqcloud.com/images/andOTP/Steam2.jpg)

## 推荐的设置项

andOTP 的设置在右上角的更多菜单中，这一部分会讲一下我个人比较推荐的设置项目

### 设备验证

可以选择 无, 密码, PIN, 设备凭据

前三个都非常好理解，是在 andOTP 单独设置的验证方法

设备凭据是你设备本身的登录方式

选择这个可以调用 Android 系统的密码，也可以通过指纹验证(如果有)

### 数据库加密

可以选择 Android 密钥库 或 密码/PIN

如果想要使用设备凭据验证则只能选择 Android 密钥库，没什么好说的

但是需要注意的一点是

如果选择 Android 密钥库，SwiftBackup, 手机自带备份, adb backup 都无法备份 andOTP 内的密钥数据（别问我怎么知道的）

**Android 密钥库 用户一定要记得备份**

### 单击 & 双击

用来设置单击/双击条目时进行的操作

我个人习惯将 单击 设置为 显示/隐藏

双击 设置为 复制

### 允许截图

在关于页面中点击点击八次 "版本"，在弹出的对话框中点击 确定

回到设置就能在最下方看到 启用屏幕截图 的选项了

打开后就可以用其他应用在 andOTP 中进行屏幕录制或截图了

## 备份

在更多菜单中点击 备份

备份类型可以根据自己的需要来选择，推荐选择已加密

然后点击 创建备份

如果选择已加密，在弹出的窗口中为备份文件设置密码

如果需要恢复备份，在下面点击 还原备份

选择之前的备份文件，如果有密码的话输入密码就可以还原了
