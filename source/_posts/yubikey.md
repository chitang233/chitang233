---
title: 年轻人的第一个 YubiKey
categories: 日常
tags:
  - YubiKey
  - Linux
  - 日常
abbrlink: 22725
date: 2022-11-28 21:35:12
---

国庆买的 YubiKey 5 NFC 前两天终于到货了，不过貌似没几个中文 Linux 用户给 YubiKey 写文章

恰好这段时间也遇到了一些奇怪的问题，折腾了一些有意思的功能，就顺手写篇文章记录下吧

<!-- more -->

本文均以 Arch Linux 作为目标系统编写

## 配置

安装 `yubikey-manager` 和 `yubikey-manager-qt` 两个包，前者是命令行工具，后者是图形化管理工具

插入 YubiKey 后，可以通过 `ykman info` 查看 YubiKey 的基础信息，不出意外的话应该是类似下面这样:

```
Device type: YubiKey 5 NFC
Serial number: 20114514
Firmware version: 5.4.3
Form factor: Keychain (USB-A)
Enabled USB interfaces: OTP, FIDO, CCID
NFC transport is enabled.

Applications    USB     NFC    
FIDO2           Enabled Enabled
OTP             Enabled Enabled
FIDO U2F        Enabled Enabled
OATH            Enabled Enabled
YubiHSM Auth    Enabled Enabled
OpenPGP         Enabled Enabled
PIV             Enabled Enabled
```

若出现

```
WARNING: PC/SC not available. Smart card protocols will not function.
```

你还需要安装 `pcsclite` 和 `pcsc-tools`，并且启用 `pcscd.service` 来启动智能卡服务

在 YubiKey Manager 中的 Applications 里有三个菜单

### OTP

打开 OTP 页面，能够看到两个槽位(Slot)，建议使用 Slot 1 作为 Yubico OTP

在 Slot 1 下方点击 Configure，选择 Yubico OTP

Public ID 使用序列号(勾上旁边的 Use serial)

Private ID 和 Secret Key 可以点旁边的 Generate 随机生成

最后，勾选 Upload 将信息上传至 YubiCloud 以供验证

在打开的网页中激活 OTP from YubiKey 输入框，点击 YubiKey 上的按钮，在完成 reCaptcha 后点击 Upload，Yubico OTP 就配置完成了

Slot 2 可以自行配置，我选择 Static password 并设置为我的电脑登录密码

### FIDO2

唯一的作用就是设置 / 更改使用 FIDO 时的 PIN，自己设置就好

Reset FIDO 不要乱点，否则会导致之前设置的验证器均无法使用

### PIV

喜报：我只知道 Configure PINs 是干啥的

打开 Configure PINs 页面，里面分别是 PIN, PUK, Management Key

分别进行设置，PIN, PUK, Management Key 的默认值分别是 `123456`, `12345678`, `010203040506070801020304050607080102030405060708`

自己设置的 PIN 一定要记住，否则无法恢复

## FIDO2 / WebAuthn

(也许是)作为安全密钥最基础的功能

可能需要的软件包: `libfido2`

几乎无需配置，在 Firefox 和 Chromium 上均能够做到开箱即用

## OpenPGP 智能卡

> 本段部分参考自 [ZH 小涵的日常～](https://blog.eden-official.co.uk/p/yubikey-first-tried/)，故使用 CC BY-NC-SA 4.0 协议授权

执行 `gpg --card-status`，应该会输出类似这样的信息

```
Reader ...........: Yubico YubiKey OTP FIDO CCID 00 00
Application ID ...: D2760001240103040006206024114514
Application type .: OpenPGP
Version ..........: 3.4
Manufacturer .....: Yubico
Serial number ....: 20114514
Name of cardholder: Chi_Tang  
Language prefs ...: zh
Salutation .......: 
URL of public key : https://keys.openpgp.org/vks/v1/by-fingerprint/AEDF1BB86A40BB8225BDF751DF94909C825CF811
Login data .......: [未设定]
Signature PIN ....: 非强制Key attributes ...: ed25519 cv25519 ed25519
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 0 3
Signature counter : 0
KDF setting ......: off
Signature key ....: AEDF 1BB8 6A40 BB82 25BD  F751 DF94 909C 825C F811
      created ....: 2022-11-29 04:18:48
Encryption key....: 0185 7730 0E11 05EF 573D  B07D 7FB7 6FEB 6E8D 3E0E
      created ....: 2022-11-29 04:18:48
Authentication key: 2891 E10B 22CF 1C8F CFA3  73F9 1957 2629 AA87 CCC4
      created ....: 2022-11-29 03:55:03
General key info..: pub  ed25519/DF94909C825CF811 2022-11-29 Chi_Tang <me@chitang.dev>
sec>  ed25519/DF94909C825CF811  创建于：2022-11-29  有效至：永不     
                                卡号： 0006 20602406
ssb>  cv25519/7FB76FEB6E8D3E0E  创建于：2022-11-29  有效至：永不     
                                卡号： 0006 20602406
```

不过我这里是设置好之后的，新的 YubiKey 输出的内容应该会更少

执行 `gpg --edit-card`

输入 `admin` 允许使用管理员命令

`help` 中列出了能够更改的信息，可根据自己需要进行设置

使用 `passwd` Unblock PIN，然后更改 PIN 和 Admin PIN，它们默认分别为 `123456` 和 `12345678`

在设置完成后使用 `quit` 退出

执行 `gpg --edit-key "your@email.com"`

输入 `key 1` 选择子密钥，然后执行 `keytocard` 将子密钥导入到智能卡

完成后输入 `key 1` 取消选择，再执行 `keytocard` 将主密钥导入到智能卡

最后，执行 `save` 保存并退出

## 验证 SSH 密钥

> 该方法需要重新生成一个新的 SSH 密钥对

```shell
ssh-keygen -t ed25519-sk
```

然后就没有然后了，当作正常的 ED25519 密钥对使用即可

验证时需要点击 YubiKey 的按钮以确认

## 用作 PAM 模块

> 本段内容参考自 [ArchWiki](https://wiki.archlinux.org/title/Universal_2nd_Factor#Authentication_for_Arch_Linux)

安装 `pam-u2f` 并执行

```shell
pamu2fcfg -o pam://hostname -i pam://hostname > ~/.config/Yubico/u2f_keys
```

然后点击 YubiKey 的按钮，完成后检查 `~/.config/Yubico/u2f_keys` 中是否有内容

接下来就可以编辑 pam 配置文件了

在 `/etc/pam.d` 里找到你想要配置的文件，比如 `sudo`，用任意文本编辑器打开，添加以下内容

```
auth            sufficient      pam_u2f.so cue origin=pam://hostname appid=pam://hostname
```

其他也同理，比如 `system-login` `system-auth` `sddm` `kde`

需要注意的是，SDDM 需要先点击一次登录按钮(或按下回车)再点击 YubiKey 的按钮才能验证

## 一些别的废话

YubiKey 能做到的事远比这篇文章所写的要多，比如直接用作 SSH 密钥、配合 LUKS 加密硬盘~~，但很明显这些我都不会，官方的文档着实看不太明白~~

有没有会折腾的来教教我——
