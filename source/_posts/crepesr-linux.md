---
title: '在 Linux 上搭建 CrepeSR (崩坏: 星穹铁道 社区服务器)'
hide: false
tags:
  - CrepeSR
  - 星穹铁道
  - Linux
  - 私服
  - Guide
abbrlink: 57251
date: 2022-08-04 17:40:21
---

在 Linux 上搭建 CrepeSR (崩坏: 星穹铁道 社区服务器)

<div class="warning">

> 本文仅适用于基于 Arch Linux 和 Debian 的发行版 (当然, 如果你有其他发行版的安装方式也欢迎 PR), Windows 服务器的搭建可参考 [CrepeSR 官方 Wiki](https://github.com/Crepe-Inc/CrepeSR/wiki/%E4%B8%AD%E6%96%87%E7%89%88%E6%95%99%E7%A8%8B) 或 [TomyJan 的教程](https://blog.tomys.top/2022-08/starrailtj/)
> 
> 如遇问题请前往 [@genkitCN_chat](https://t.me/genkitCN_chat) (人机验证过不了建议滚回高中)
> 
> 由于 Node.js 的特性, 使用 root 权限或用户运行服务器会出现问题, 因此请参考您发行版的 Wiki 或在网络上查阅, 建立一个带有特权的新用户再进行操作

</div>

## 环境配置
### Git
#### Arch Linux
```shell
sudo pacman -S git --needed
```

#### Debian
```shell
sudo apt install git
```

### Node.js
#### Arch Linux
```shell
sudo pacman -S nodejs-lts-gallium --needed
```

#### Debian
```shell
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt install nodejs
```

### npm
#### Arch Linux
```shell
sudo pacman -S npm --needed
```

#### Debian
```shell
sudo apt install npm
```

### MongoDB
#### Arch Linux
在 Arch Linux 上安装 MongoDB 需要使用一个 AUR Tool, 在本文中以 `paru` 为例. 如果你已经配置好任意一个 AUR Tool, 可以跳过下一代码块

```shell
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
```

注意: 操作 AUR 包及运行相关的命令时, **不可使用 root 权限**

如果运行上述命令出现问题, 你可能需要安装 core 源中的 `base-devel`, 过程不再赘述

然后, 使用 paru 安装 MongoDB

```shell
paru -S mongodb-bin --needed
sudo systemctl enable --now mongodb
```

#### Ubuntu
首先, 导入 MongoDB 的 PGP 公钥

```shell
sudo apt install gnupg
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | apt-key add -
```

然后, 根据你的系统版本选择对应的命令执行

- 20.04 (Focal)
```shell
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
```

- 18.04 (Bionic):
```shell
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
```

- 16.04 (Xenial):
```shell
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
```

最后, 更新软件源并安装

```shell
sudo apt update
sudo apt install mongodb-org
sudo systemctl enable --now mongod
```

#### Debian
```shell
sudo apt install mongodb-org
sudo systemctl enable --now mongod
```

### Python
#### Arch Linux
```shell
sudo pacman -S python --needed
```

#### Debian
```shell
sudo apt install python3
```

### mitmproxy
#### Arch Linux
```shell
sudo pacman -S mitmproxy --needed
```

#### Debian
```shell
sudo apt install mitmproxy
```

### libcap
#### Arch Linux
```shell
sudo pacman -S libcap
```

#### Debian
```shell
sudo apt install libcap2-bin
```

### 检查安装
```shell
node -v
npm -v
mongod --version
python -V
mitmproxy --version
```

如果输出类似下图的版本号而不出现报错则为环境配置完成

![依赖](https://chitangcos.zyglq.cn/images/crepesr-linux/dep.png)

## 安装 CrepeSR
### 拉取项目和资源
```shell
git clone https://github.com/Crepe-Inc/CrepeSR --depth=1
git clone https://github.com/memetrollsXD/CrepeSR-Resources CrepeSR/src/data --depth=1
```

在完成后, `CrepeSR` 文件夹内的文件结构应与下图基本一致

![文件结构](https://chitangcos.zyglq.cn/images/crepesr-linux/crepesr-files.png)

### 安装依赖
<div class="warning">

> 一定**不要**使用 root 权限或账户执行下述指令

</div>

```shell
cd CrepeSR
npm install
```

![完成](https://chitangcos.zyglq.cn/images/crepesr-linux/npm-install.png)

### 端口设置
使 node 允许绑定 80 与 443 端口

```shell
sudo setcap cap_net_bind_service=+eip `readlink -f \`which node\``
```

## 运行服务端
```shell
mitmdump -s proxy.py -k --set block_global=false &
npm run start
```

## 常用命令 (服务端内)
### account
用于账户的操作

用法:

```
account <create|delete> <name> [uid]
```

示例:

```
account create chitang 114514 # 创建一个名为 chitang 且 uid 为 114514 的玩家
account delete 114514         # 删除 uid 为 114514 的玩家
```

### target
用于指定命令的目标, 其他的命令基本都需要先使用该命令确定目标

用法:

```
target <uid>
```

示例:

```
target 114514
```

### avatar
用于操作玩家的角色

用法:

```
avatar <add|remove> <AvatarID>
```

示例:

```
avatar add 1001     # 给指定的玩家三月七
```

<details>
  <summary>AvatarID 对照表</summary>

| AvatarID | 中文名       |
| -------- | --------- |
| 1001     | 三月七       |
| 1002     | 丹恒        |
| 1003     | 姬子        |
| 1004     | 瓦尔特       |
| 1005     | 卡芙卡       |
| 1006     | 布洛妮娅 (银狼) |
| 1008     | 阿兰        |
| 1009     | 艾丝妲       |
| 1013     | 黑塔        |
| 1101     | 布洛妮娅 (成人) |
| 1102     | 希儿        |
| 1103     | 希露瓦       |
| 1104     | 杰帕德       |
| 1105     | 娜塔莎       |
| 1106     | 佩拉        |
| 1107     | 克拉拉       |
| 1108     | 桑博        |
| 1109     | 胡克        |
| 1203     | 罗刹        |
| 1204     | 静渊        |
| 1205     | 刀锋        |
| 1206     | 素裳        |

</details>

### 地图和场景
在 `CrepeSR/src/data/excel` 中的 `MapEntryExcelTable.json` 里可以找到能用来改变场景的 `planeID` 与 `floorID`

在 `CrepeSR/src/server/packets` 中的 `GetCurSceneInfoCsReq.ts` 里编辑 `planeId` 与 `floorId` 来改变场景

### 游戏客户端
在[我的 Cloudreve](https://cloudreve.chitang.dev/s/k5h6) 里有国服启动器, 国服游戏本体, 国际服游戏本体等资源

不过建议使用页面下方来自米哈游官方的下载链接~~（因为 OneDrive 真的很慢）~~
