---
title: Arch Linux 折腾指南
categories: 折腾
tags:
  - Arch
  - Linux
  - 教程
abbrlink: 4357
date: 2021-12-06 19:01:57
---

<div class="warning">

> 本文仅为 [Arch Linux](https://www.archlinux.org/) 的个人安装 & 配置过程，不代表 Arch Linux 官方
> Arch Linux 是一个滚动更新的发行版，需要经常通过 `sudo pacman -Syu` 来更新软件包，否则可能会导致滚挂，如果不经常使用电脑但也想用 Arch Linux，请在[选择内核](#内核的选择)时采用 `linux-lts`
> 本文命令行中文本编辑均使用 `vim`，你可以换成任何你喜欢的其他编辑器
> 本文图形界面的配置仅介绍了 `Xorg` 且美化部分仅有 `KDE Plasma`(甚至还没写)，如果要使用其他的环境，请自行查找其他文章

</div>

目录在左侧(移动端为左上角菜单中)，可通过点击目录标题进入相应内容

To-Do List:

- [ ] 图形界面美化
- [ ] 补全教程图片
- [ ] 制作视频教程 *咕咕咕*

# 正式安装前的准备

## 下载

Arch Linux 有许多镜像源，你可以在[官方的下载链接](https://archlinux.org/download/)中寻找一个离自己最近的镜像源进行下载

在中国大陆，我推荐[北京外国语大学开源软件镜像站](https://mirrors.bfsu.edu.cn/)，在右侧的 “获取下载链接” 处即可找到 Arch Linux 的最新 ISO 文件

![bfsu](https://chitangcos.zyglq.cn/images/arch-guide/bfsu.png)

## 刻录

### Ventoy(推荐)

在一个已经安装好 [Ventoy](https://ventoy.net/) 的 U 盘上，直接把 ISO 文件放进去即可生效

### Linux DD

<div class="danger">

> 在进行 dd 操作前，请确保驱动器内的数据都已经备份，否则会丢失数据

</div>

```bash
dd if=/path/to/archlinux-*.iso of=/dev/sdX status=progress
```

将 `/path/to/archlinux-*.iso` 替换为你的 ISO 文件的位置

将 `/dev/sdX` 替换为你的 U 盘设备名

![Linux DD](https://chitang-main-1256617490.cos.ap-shanghai.myqcloud.com/images/arch-guide/linux-dd.png)

### Windows Rufus

在 Windows 上，我更加推荐使用 [Rufus](https://rufus.ie/) 进行刻录

<div class="danger">

> 同样地，在进行刻录前，请确保驱动器内的数据都已经备份，否则会丢失数据

</div>

## 准备磁盘分区

最好在进入 Arch Linux LiveCD 前先编辑分区，为 Arch Linux 留出足够的空间，但**不要**在这时对分区进行格式化，否则可能会出现不可预料的问题

## BIOS 设置

### 强制 UEFI 启动

如果没有特殊需求(如需要与采用 Legacy 为引导方式的 Windows 共存)，请前往主板的设置界面关闭 `CSM` 以强制以 UEFI 方式启动

具体的设置方式请自行前往互联网寻找答案或在评论区询问

### 关闭安全启动

Arch Linux 的 LiveCD 是不带安全启动签名的，如果启用安全启动会导致无法进入系统

具体的设置方式也请自行前往互联网寻找答案或在评论区询问

## 进入 LiveCD

在[刻录](#刻录)步骤中，我们已经将 ISO 文件刻录到了 U 盘，现在就应设置为 U 盘启动进入 Arch Linux LiveCD 了

具体的设置方式请自行前往互联网寻找答案或在评论区询问

<div class="success">

> 至此，正式安装前的准备已完成，请进入 Arch Linux LiveCD 并进行安装

</div>

Arch Linux ISO 现已加入 `archinstall` 工具，可以在启动 LiveCD 后运行 `archinstall` 进行引导式安装

但根据我几个朋友的反馈，这个工具的 bug 和怪问题还是不少的，所以不推荐使用这种方法安装

如果你通过 `archinstall` 安装，那么你可以跳过 [LiveCD 部分](#LiveCD)和[图形界面部分](#图形界面)

# LiveCD

## 联网

### 有线连接到路由器

如果采用这种方法连接到互联网，那么默认情况下无需其他配置

如果无法连接到互联网，那么可以尝试重新启动 `dhcpcd` 服务

```bash
systemctl restart dhcpcd
```

### WiFi 连接

想要在 LiveCD 中使用 WiFi 会稍微有些麻烦，可以使用 `iwctl` 命令来进行 WiFi 的配置，在下面会列出 `iwctl` 的简单使用方法

```
[archlinux@archlinux ~]$ iwctl  # 进入 iwd 的交互提示符
[iwd]# device list  # 列出所有 WiFi 设备
[iwd]# station <device> scan  # 扫描 WiFi 网络
[iwd]# station <device> connect <ssid>  # 连接到 WiFi 网络
```

或者可以改成命令行参数的形式

```bash
iwctl --passphrase <passphrase> station <device> connect <SSID>
```

### 检查网络连接

```bash
ping -c 3 archlinux.org
```

如果没有报错则说明网络连接正常

## 更新系统时间

```bash
timedatectl set-ntp true
```

## 分区

首先，通过 `lsblk` 或是 `fdisk -l` 确定需要安装 Arch Linux 的硬盘

在这里，我们只需要确定硬盘的设备名，例如 `/dev/sda`

在 LiveCD 中分区可以通过 `parted` `fdisk` `cfdisk` 等工具完成，本文章会详细介绍 `fdisk` 的使用方法

```bash
fdisk /dev/sdX   # 进入 fdisk 交互提示符
```

| 命令  | 描述                |
| --- | ----------------- |
| `m` | 获取帮助              |
| `p` | 显示当前分区表           |
| `n` | 新建分区              |
| `o` | 新建 MBR 分区表(会丢失数据) |
| `g` | 新建 GPT 分区表(会丢失数据) |
| `w` | 保存更改              |
| `q` | 不保存更改退出           |
| `d` | 删除分区              |

在新建分区时

`Partition number` 保持默认，并请记住它，以便下一步操作

`First sector` 保持默认

`Last sector` 填写分区大小，以 `+` 开头，以单位结尾，例如 `+512M` 为 512MiB 的分区

MBR 推荐分区:

| 挂载点   | 文件系统 | 分区推荐大小(以 120GiB 硬盘为例) | 文章中所使用的分区号 |
| ----- | ---- | --------------------- | ---------- |
| /     | ext4 | 50GiB                 | /dev/sda1  |
| /home | ext4 | 70GiB                 | /dev/sda2  |

GPT 推荐分区:

| 挂载点   | 文件系统  | 分区推荐大小(以 120GiB 硬盘为例) | 文章中所使用的分区号 |
| ----- | ----- | --------------------- | ---------- |
| /boot | FAT32 | 300MiB                | /dev/sda1  |
| /     | ext4  | 50GiB                 | /dev/sda2  |
| /home | ext4  | 69.7GiB               | /dev/sda3  |

`/home` 分区是可选的，如果不需要 `/home` 分区，可以将所用空间全部分配到 `/` 分区

## 格式化

```bash
mkfs.ext4 /dev/sdXY # 格式化 /dev/sdXY 为 ext4 文件系统
mkfs.fat -F32 /dev/sdXY # 格式化 /dev/sdXY 为 FAT32 文件系统
```

请自行替换 `/dev/sdXY` 到对应的块设备

## 挂载

### GPT

```bash
mount /dev/sda2 /mnt  # 挂载 /dev/sda2 到 /mnt( / 分区)
mkdir /mnt/boot /mnt/home # 创建 /mnt/boot 和 /mnt/home 文件夹
mount /dev/sda1 /mnt/boot # 挂载 /dev/sda1 到 /mnt/boot( /boot 分区)
mount /dev/sda3 /mnt/home # 挂载 /dev/sda3 到 /mnt/home( /home 分区)
```

### MBR

```bash
mount /dev/sda1 /mnt  # 挂载 /dev/sda1 到 /mnt( / 分区)
mkdir /mnt/home # 创建 /mnt/home 文件夹
mount /dev/sda2 /mnt/home # 挂载 /dev/sda2 到 /mnt/home( /home 分区)
```

## 换源

与[下载 Arch Linux](#下载)一样，在中国大陆地区，默认的镜像源是极慢的

可以通过编辑 `/etc/pacman.d/mirrorlist` 的方式来更改镜像源

```bash
vim /etc/pacman.d/mirrorlist
```

在文件的开头添加类似 `Server = https://mirrors.bfsu.edu.cn/archlinux/$repo/os/$arch` 的内容，保存并退出

## 安装 Arch Linux 到硬盘

### 内核的选择

在这里列出了官方源中所存在的部分内核，可以根据自己的需求选择

| 内核          | 特点                      |
| ----------- | ----------------------- |
| `linux`     | 官方的默认内核                 |
| `linux-lts` | 官方的长期支持内核，版本较低，但相对不容易滚挂 |
| `linux-zen` | 社区制作的更适合日常使用的内核         |

### 安装

```bash
pacstrap /mnt base base-devel linux-firmware <linux-kernel> <linux-kernel>-headers grub vim dhcpcd iwd os-prober efibootmgr [other packages you want]
```

将 `<linux-kernel>` 替换为你所选定的内核

## 生成分区表

```bash
genfstab -U /mnt > /mnt/etc/fstab
```

最好再自行检查一下 `/mnt/etc/fstab` 以确保信息正确

## 设置时区

```bash
arch-chroot /mnt  # 切换到新的系统
ln -sf /usr/share/zoneinfo/<Region>/<City> /etc/localtime
hwclock --systohc
```

`<Region>` 和 `<City>` 分别是你所在的地区和城市，例如 `Asia/Shanghai`

<div class="warning">

> **如果在之后没有执行 `exit` 命令退出 arch-chroot 环境，在后面的操作中就不需要再次执行 `arch-chroot /mnt`**

</div>

## 设置网络相关

### 计算机名

```bash
arch-chroot /mnt
vim /etc/hostname
```

在文件中写入你的计算机名，例如 `chitang-pc`

保存并退出

### hosts 文件

```bash
arch-chroot /mnt
vim /etc/hosts
```

在文件中写入下面的内容

```
127.0.0.1 localhost
::1       localhost
127.0.1.1 <hostname>.localdomain  <hostname>
```

将 `<hostname>` 替换为你的计算机名，保存并退出

## 配置本地化

### locale.gen

```bash
arch-chroot /mnt
vim /etc/locale.gen
```

在文件中找到 `en_US.UTF-8 UTF-8` 及 `zh_CN.UTF-8 UTF-8`，删除前面的 `#` 以取消注释

同样地，在这里也可以设置其他语言的的 `locale`，例如 `ja_JP.UTF-8 UTF-8`

```bash
arch-chroot /mnt
locale-gen  # 生成本地化文件
```

### locale.conf

```bash
arch-chroot /mnt
vim /etc/locale.conf
```

在文件中加入 `LANG=en_US.UTF-8`，保存并退出

<div class="warning">

> 请不要在这里设置任何其他的语言，否则极易导致 tty 乱码

</div>

## 允许 os-prober 检测 Windows

<div class="success">

> 可选，如果不需要启用 GRUB 对 Windows Boot Manager 的支持，可以跳过这一步

</div>

```bash
arch-chroot /mnt
vim /etc/default/grub
```

在文件任意位置加入 `GRUB_DISABLE_OS_PROBER="false"`，保存并退出

## 安装引导

### UEFI + GPT

```bash
arch-chroot /mnt
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub # 在 /boot 安装引导
grub-mkconfig -o /boot/grub/grub.cfg  # 生成 GRUB 配置
```

### Legacy + MBR

```bash
arch-chroot /mnt
grub-install --target=i386-pc /dev/sdX  # 在 /dev/sdX 安装引导，不要加分区号
grub-mkconfig -o /boot/grub/grub.cfg  # 生成 GRUB 配置
```

## 重启前要做的事

```bash
arch-chroot /mnt
systemctl enable dhcpcd # 启用 dhcpcd 服务以便进入系统后自动联网
passwd  # 设置 root 密码
```

## 重启

<div class="success">

> 至此，在 LiveCD 部分的安装已经完成，你现在可以拔掉启动介质，重启，然后进入 Arch Linux 的 tty

</div>

# 配置

## 创建新用户

```bash
useradd -m -G wheel <username>
passwd <username>
EDITOR=vim visudo
```

在打开的文件中找到 `%wheel ALL=(ALL) ALL`，删除前面的 `#` 以取消注释，保存并退出

之后，就可以退出 root 账户(直接 `exit`)，然后使用你自己的账户进行下面的操作

## 交换文件

```bash
sudo dd if=/dev/zero of=/swapfile bs=1M count=<size>  # 在 size 处填写需要的 swap 空间大小(单位 MiB)
sudo mkswap /swapfile
sudo chmod 600 /swapfile
sudo swapon /swapfile
```

然后编辑 `/etc/fstab`，在文件末尾加入以下内容

```
/swapfile       none    swap    defaults        0       0
```

## pacman.conf 的配置

```bash
sudo vim /etc/pacman.conf
```

### Misc options

<div class="success">

> 这部分内容是可选的，且不影响基本操作

</div>

将 `Color` 取消注释，让你的 pacman 有颜色

手动加入 `ILoveCandy`，把 pacman 下载的进度条替换成吃豆人

将 `VerbosePkgLists` 取消注释，让 pacman 以表格显示更详细的信息

### 额外软件源

#### multilib

`multilib` 软件源中包含一些 32 位的依赖包

翻到文件后面，找到

```
#[multilib]
#Include = /etc/pacman.d/mirrorlist
```

去掉这两行前面的 `#` 以取消注释即可启用 `multilib` 软件源

#### Arch Linux CN

`Arch Linux CN` 源中包含许多在国内使用 Linux 常用的软件包，虽然这些都可以在 AUR 中安装，但这里的软件包下载速度比较快

在文件最后面添加下面这两行

```
[archlinuxcn]
Server = https://mirrors.bfsu.edu.cn/archlinuxcn/$arch
```

保存并退出

更新软件源并安装密钥

```bash
sudo pacman -Sy archlinuxcn-keyring
```

#### Chaotic-AUR

`Chaotic-AUR` 中包含一些经过编译的 AUR 软件

导入 `Chaotic-AUR` 的 Pacman 密钥并安装 mirrorlist

```shell
pacman-key --recv-key FBA220DFC880C036 --keyserver keyserver.ubuntu.com
pacman-key --lsign-key FBA220DFC880C036
pacman -U 'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-keyring.pkg.tar.zst' 'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-mirrorlist.pkg.tar.zst'
```

然后在 `/etc/pacman.conf` 最下方加入以下两行

```
[chaotic-aur]
Include = /etc/pacman.d/chaotic-mirrorlist 
```

然后更新软件源

```shell
sudo pacman -Sy
```

如果速度过慢可以尝试使用反代服务

#### Clansty

同样也是一些编译后的 AUR 软件

在 `/etc/pacman.conf` 最下方加入: 

```
[Clansty]
SigLevel = Never
Server = https://repo.lwqwq.com/archlinux/$arch
Server = https://pacman.ltd/archlinux/$arch
Server = https://repo.clansty.com/archlinux/$arch
```

然后更新软件源

```shell
sudo pacman -Sy
```

## 中文

<div class="warning">

> 这部分中文配置仅在桌面环境运行时有效，因为在 tty 中显示中文会导致乱码

</div>

### 设置环境变量

```bash
vim ~/.xprofile
```

添加下面两行:

```
export LANG=zh_CN.UTF-8
export LC_ALL=zh_CN.UTF-8
```

### 安装字体

部分来自 AUR 的字体需要通过 [AUR Tool](#aur-tools) 安装

在下面列出了一些(我推荐的)字体，可以自行安装

```bash
sudo pacman -S wqy-microhei # 文泉驿
sudo pacman -S adobe-source-sans-fonts  # 思源黑体
paru -S harmonyos-sans-git  # 鸿蒙黑体
paru -S ttf-misans  # MiSans
```

## Zsh

[Zsh](https://zsh.sourceforge.io/) 是一个 Shell 程序

### 安装

```bash
sudo pacman -S zsh
```

### 更改默认 Shell

```bash
chsh -s /usr/bin/zsh
```

如果使用图形界面，也请将所使用的终端模拟器默认 Shell 更换为 zsh

### Zim

[Zim](https://zimfw.sh/) 是一个 zsh 的模块化配置框架

#### 安装

```bash
wget -nv -O - https://raw.githubusercontent.com/zimfw/install/master/install.zsh | zsh
```

#### 配置

Zimfw 的配置文件为 `~/.zimrc`

使用任意文本编辑器打开它，配置文件的编写格式为:

```
zmodule [module name] # 模块在 zimfw 官方组织
zmodule [author]/[module name] # 模块在 Github 上
zmodule https://[host]/[author]/[module name].git # 模块在其他 Git 仓库中
```

在添加好模块后，可以通过 `zimfw install` 命令安装，本文同样提供[模块推荐](#模块推荐)

在之后可以通过 `zimfw update` 更新模块，通过 `zimfw upgrade` 更新 Zim 本体

#### 模块推荐

部分内容来自 [Zim 官方网站](https://zimfw.sh/docs/modules/)

##### 实用工具

| 模块名                                    | 作用                               |
| -------------------------------------- | -------------------------------- |
| archive                                | 快速操作压缩包                          |
| exa                                    | 添加 exa 的 alias 以便能够在 ls 中显示图片和图标 |
| pacman                                 | 添加 pacman 的 alias 方便操作           |
| git-info                               | 提供当前 Git repo 的状态信息              |
| Aloxaf/fzf-tab                         | 将 Tab 菜单替换为类似 fzf 的菜单            |
| zsh-users/zsh-autosuggestions          | 增加自动补全功能                         |
| zsh-users/zsh-syntax-highlighting      | 增加语法高亮功能                         |
| zsh-users/zsh-history-substring-search | 增加搜索历史记录功能                       |

##### 主题(挑选一个安装即可)

| 模块名                   | 特点          | 预览                                                                                                                                                                                                                             |
| --------------------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| gitster               | 简洁          | ![gitster](https://camo.githubusercontent.com/7ebba9863fafbf14cf179ef1555d8a53882188eb3d5b125084c8d26f2b008f71/68747470733a2f2f7a696d66772e6769746875622e696f2f696d616765732f70726f6d7074732f6769747374657240322e706e67)       |
| magicmace             | 方便查看 Git 状态 | ![magicmace](https://camo.githubusercontent.com/c8215d78fe82b947668317b16cdee0a16d25ed7b24c462cb198e1ac6c037f3f1/68747470733a2f2f7a696d66772e6769746875622e696f2f696d616765732f70726f6d7074732f6d616769636d61636540322e706e67) |
| romkatv/powerlevel10k | 美观，可自定义性极强  | ![powerlevel10k](https://raw.githubusercontent.com/romkatv/powerlevel10k-media/master/prompt-styles-high-contrast.png)                                                                                                         |

# 图形界面

## 安装

### 显卡驱动

<div class="info">

> 部分驱动需要启用 [32 位源](#multilib)
> 带有 <sup>AUR</sup> 上标的软件包是来自 AUR 的，需要使用 [AUR Tool](#aur-tools) 安装

</div>

#### Intel 显卡

```bash
sudo pacman -S --needed lib32-mesa vulkan-intel lib32-vulkan-intel vulkan-icd-loader lib32-vulkan-icd-loader intel-media-driver libvdpau-va-gl
```

#### NVIDIA 显卡

##### 专有驱动

本体:

|           | GeForce 930(NV110 及更新) | GeForce 630-920(NVE0)           | GeForce 400/500/600(NVCx & NVDx) | GeForce 8/9(NV5x, NV8x, NV9x, NVAx) 不推荐安装专有驱动 | GeForce 7 及以下 |
| --------- | ---------------------- | ------------------------------- | -------------------------------- | --------------------------------------------- | ------------- |
| linux     | nvidia                 | nvidia-470xx-dkms<sup>AUR</sup> | nvidia-390xx-dkms<sup>AUR</sup>  | nvidia-340xx-dkms<sup>AUR</sup>               | 不支持           |
| linux-lts | nvidia-lts             | nvidia-470xx-dkms<sup>AUR</sup> | nvidia-390xx-dkms<sup>AUR</sup>  | nvidia-340xx-dkms<sup>AUR</sup>               | 不支持           |
| 其他        | nvidia-dkms            | nvidia-470xx-dkms<sup>AUR</sup> | nvidia-390xx-dkms<sup>AUR</sup>  | nvidia-340xx-dkms<sup>AUR</sup>               | 不支持           |

```bash
sudo pacman -S <driver> # 驱动本体
sudo pacman -S <driver>-utils # OpenGL 支持
sudo pacman -S lib32-<driver>-utils # 32 位 OpenGL 支持
sudo pacman -S opencl-nvidia  # OpenCL 支持
```

##### 开源驱动

```bash
sudo pacman -S xf86-video-nouveau # 驱动本体
sudo pacman -S mesa # OpenGL 支持
sudo pacman -S lib32-mesa # 32 位 OpenGL 支持
```

#### AMD / ATI 显卡

##### 开源驱动

| 架构                                              | 驱动                | OpenGL | 32 位 OpenGL | Vulkan        | 32 位 Vulkan         |
| ----------------------------------------------- | ----------------- | ------ | ----------- | ------------- | ------------------- |
| RDNA, RDNA 2, GCN 1, GCN 2, GCN 3, GCN 4, GCN 5 | xf86-video-amdgpu | mesa   | lib32-mesa  | amdvlk        | lib32-amdvlk        |
| GCN 1, GCN 2, TeraScale 或更老                     | xf86-video-ati    | mesa   | lib32-mesa  | vulkan-radeon | lib32-vulkan-radeon |

```bash
sudo pacman -S opencl-mesa  # OpenCL 支持
```

##### 闭源驱动

AMD 闭源驱动仅支持 `RDNA`, `RDNA 2`, `GCN 3`, `GCN 4`, `GCN 5` 架构的显卡

```bash
sudo pacman -S xf86-video-amdgpu # 驱动本体
paru -S amdgpu-pro-libgl  # OpenGL 支持
paru -S opencl-amd  # OpenCL 支持
paru -S vulkan-amdgpu-pro # Vulkan 支持
```

#### 其他显卡

```bash
sudo pacman -S xorg-drivers
sudo pacman -S mesa # OpenGL 支持
sudo pacman -S lib32-mesa # 32 位 OpenGL 支持
sudo pacman -S pocl # OpenCL 支持
```

### DE / WM

| 名称                        | 官方预览图                                                                        |
| ------------------------- | ---------------------------------------------------------------------------- |
| [KDE Plasma](#kde-plasma) | ![KDE Plasma](https://kde.org/content/plasma-desktop/plasma-widgets.png)     |
| [GNOME](#gnome)           | ![GNOME](https://www.gnome.org/wp-content/uploads/2021/03/wgo-splash-40.png) |
| [Xfce 4](#xfce)           | ![Xfce 4](https://cdn.xfce.org/about/screenshots/4.16-1.png)                 |
| [DDE](#dde)               | ![DDE](https://distrowatch.com/images/ktyxqzobhgijab/deepin.png)             |

#### KDE Plasma

推荐与 [SDDM](#sddm) 配合使用

```bash
sudo pacman -S plasma kde-applications
```

`kde-applications` 是可选的，其中包含 KDE 的其他应用

#### GNOME

```bash
sudo pacman -S gnome gnome-extra
```

`gnome-extra` 是可选的，其中包含 GNOME 的其他应用

#### XFCE

```bash
sudo pacman -S xfce4 xfce4-goodies
```

`xfce4-goodies` 是可选的，其中包含 XFCE 的其他应用

#### DDE

```bash
sudo pacman -S deepin deepin-extra
```

`deepin-extra` 是可选的，其中包含 deepin 的其他应用

### 图形化登录管理器

#### SDDM

推荐与 [KDE Plasma](#kde-plasma) 配合使用

```bash
sudo pacman -S sddm
sudo systemctl enable sddm
```

#### GDM

推荐与 [GNOME](#gnome) 配合使用

```bash
sudo pacman -S gdm
sudo systemctl enable gdm
```

#### LXDM

```bash
sudo pacman -S lxdm
sudo systemctl enable lxdm
```

#### LightDM

```bash
sudo pacman -S lightdm
sudo systemctl enable lightdm
```

# 常用工具 / 软件

<div class="info">

> 带有 <sup>AUR</sup> 上标的软件包是来自 AUR 的，需要使用 [AUR Tool](#aur-tools) 安装

</div>

## AUR Tools

用于安装来自 AUR 的软件包的工具

推荐 `paru` 和 `yay`

paru:

```bash
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
cd ..
rm -rf paru
```

yay:

```bash
git clone https://aur.archlinux.org/yay.git
cd git
makepkg -si
cd ..
rm -rf yay
```

## 浏览器

| 名称                                                | 包名                                        | 备注                                       |
| ------------------------------------------------- | ----------------------------------------- | ---------------------------------------- |
| [Chromium](https://www.chromium.org)              | `chromium`                                |                                          |
| [Firefox](https://www.mozilla.org/zh-CN/firefox/) | `firefox`                                 | 全局菜单需要安装 `firefox-appmenu`<sup>AUR</sup> |
| [Google Chrome](https://google.com/chrome)        | `google-chrome`<sup>AUR</sup>             |                                          |
| [Microsoft Edge](https://microsoft.com/edge)      | `microsoft-edge-stable-bin`<sup>AUR</sup> |                                          |

## 文本编辑器 / IDE

| 名称                                                                 | 包名                                             |
| ------------------------------------------------------------------ | ---------------------------------------------- |
| [Visual Studio Code](https://code.visualstudio.com/)               | `visual-studio-code-bin`<sup>AUR</sup>         |
| [IntelliJ IDEA Community Edition](https://www.jetbrains.com/idea/) | `intellij-idea-community-edition`              |
| [IntelliJ IDEA Ultimate Edition](https://www.jetbrains.com/idea/)  | `intellij-idea-ultimate-edition`<sup>AUR</sup> |
| [PyCharm Community Edition](https://www.jetbrains.com/pycharm/)    | `pycharm-community-edition`                    |
| [PyCharm Professional](https://www.jetbrains.com/pycharm/)         | `pycharm-professional`<sup>AUR</sup>           |

## 办公套件

| 名称          | 包名                            |
| ----------- | ----------------------------- |
| LibreOffice | `libreoffice-fresh`           |
| WPS Office  | `wps-office-cn`<sup>AUR</sup> |

![又摸了一天好累哦](https://chitangcos.zyglq.cn/images/stickers/touch-fish.jpg)_**Working in progress...**_
