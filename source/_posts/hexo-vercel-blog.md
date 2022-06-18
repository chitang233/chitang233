---
title: 使用 Hexo 与 Vercel 零成本打造你的个人博客
tags:
  - Hexo
  - Vercel
  - Blog
  - Guide
abbrlink: 58336
date: 2022-06-12 17:42:09
---

## 在开始前，你需要知道的

本文简述了使用 Hexo 搭建个人博客的过程

但手上没有机器，电脑又不支持 Hackintosh，故该教程的内容对于 macOS 不一定适用

在阅读本文前，你需要准备的有:

- 一台电脑
- 能够访问 GitHub 的网络环境
- 一个 Vercel 帐号
- 一个 GitHub 帐号
- 一段时间
- 一双灵活的手
- 初中的英语水平或熟练使用翻译工具

文章包括且不限于:

- 域名的购买与解析
- Node.js 及 npm 的配置
- Hexo 的安装与配置
- Vercel 的使用

同样地，在左侧有文章的目录，可以自行选择观看，我们开始吧

## Node.js & npm

对于 Windows，前往 https://nodejs.org/zh-cn/download/ 下载 64-bit 的 msi 安装包

对于 Ubuntu，执行以下指令

```bash
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs npm
```

对于 Debian，使用 root 用户执行以下指令

```
curl -fsSL https://deb.nodesource.com/setup_16.x | bash -
apt-get install -y nodejs npm
```

对于 Arch Linux，可以直接使用 pacman 安装 `community/nodejs-lts-gallium` 和 `community/npm`

对于 NixOS，你需要安装软件包 `nodejs`
```
  environment.systemPackages = with pkgs; [ nodejs ];
```

其他发行版请自行前往 https://nodejs.org/zh-cn/download/package-manager/ 查看

在安装后，打开终端执行 `node -v` 与 `npm -v`，没有报错即为安装成功

## Hexo

### 安装

首先，执行以下命令来安装 Hexo 本体

```bash
npm install hexo-cli -g
```

<div class="info">

> 如果使用 Linux，可能会有权限不足的报错，在命令前方加入 `sudo` 使用 root 权限执行即可

</div>

<div class="yellow">

> 如果使用 NixOS，由于 Nix Store 是只读的，你还需要在安装之前执行 `npm set prefix ~/.npm-packages`，然后在 ~/.bashrc 或 ~/.zshrc 中添加
>  
>  ```shell
>  export PATH=~/.npm-packages/bin:$PATH
>  export NODE_PATH=~/.npm-packages/lib/node_modules
>  ```
>  
>  这会将 npm 的 PATH 变更到你的家目录。

</div>

在你的硬盘中建立一个文件夹，这个文件夹在下面的部分会被叫做**工作目录**

使用这条命令初始化 Hexo

```bash
hexo init
```

之后，使用这几条命令运行一个本地服务器

```bash
hexo clean
hexo g
hexo s
```

没有意外的话，使用浏览器打开 http://localhost:4000 就能够看到 Hexo 的默认首页了

![Hexo 默认首页](/images/hexo-vercel-blog/hexo-default.png)

### 配置

在工作目录下，能够看到新生成的 `_config.json`，这是 Hexo 的配置文件

#### Site

这是 Hexo 的站点配置，其中包括站点名称、描述、作者、语言、时区等

```yaml
# Site
title: Hexo # 博客标题
subtitle: ''  # 博客副标题
description: '' # 博客描述
keywords: # 博客关键字(用于 SEO，部分主题可能不支持)
author: John Doe  # 作者
language: en  # 语言
timezone: ''  # 时区
```

作为参考，这是本站的配置

```yaml
# Site
title: Chi_Tang's Blog
subtitle: '池塘的博客'
description: '只是一个普通的初中生罢了'
keywords:
author: Chi_Tang
language: zh-CN
timezone: 'Asia/Shanghai'
```

#### URL

这是 Hexo 的 URL 配置，其中包括博客的域名、链接各式等

```yaml
# URL
## Set your site url here. For example, if you use GitHub Page, set url as 'https://username.github.io/project'
url: http://example.com # 博客 URL
permalink: :year/:month/:day/:title/  # 文章链接格式
permalink_defaults:
pretty_urls:
  trailing_index: true # Set to false to remove trailing 'index.html' from permalinks
  trailing_html: true # Set to false to remove trailing '.html' from permalinks
```

同样，这是本站的配置

```yaml
# URL
## Set your site url here. For example, if you use GitHub Page, set url as 'https://username.github.io/project'
url: https://chitang.tech
permalink: posts/:title/
permalink_defaults:
pretty_urls:
  trailing_index: true # Set to false to remove trailing 'index.html' from permalinks
  trailing_html: true # Set to false to remove trailing '.html' from permalinks
```

#### Extensions

这是 Hexo 的扩展配置，其中包括插件、主题、外部插件等

```yaml
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: landscape
```

这两个链接分别是 Hexo 的插件和主题链接，可自行查看及安装

主题和插件一般都会有自己的文档，本文不再提供详细的配置方式

## 部署

首先，安装 [GitHub CLI](https://github.com/cli/cli#installation) 和 [Git](https://git-scm.com/downloads)

在终端中执行

```shell
gh auth login
```

来登录到 GitHub

然后，在工作目录下执行

```shell
git init
git add .
git commit -m "Initial commit"
gh repo create
```

根据提示，创建一个新的 GitHub 仓库并将你的 Hexo 源文件上传到该仓库(可以选择 Private)

在 https://vercel.com/account/login-connections 连接你的 GitHub 账户后，前往 https://vercel.com/new 导入你的 Hexo 仓库

![添加项目](/images/hexo-vercel-blog/vercel-add.png)

如果没有正确识别出框架的话，在 **FRAMEWORK PRESENT** 中手动选择 **Hexo**

没有其他问题的话，点击 **Deploy** 即可

之后，在 https://vercel.com 里点击你的项目，就能够 **Visit** 你的博客了

## 域名

### 获取

#### Vercel

![.vercel.app 域名](/images/hexo-vercel-blog/vercel-app-domain.png)

在 Vercel 项目设置中可以自行添加 `.vercel.app` 结尾的免费二级域名

#### 免费

[Freenom](https://www.freenom.com/zh) 和 [nic.eu.org](https://nic.eu.org/) 可以申请到免费的域名，申请过程网上已经烂大街了，本文中不再赘述

但需要注意的是，Freenom 域名因为滥用严重，无法使用 Cloudflare 的 API，同时也可能遭到部分广告屏蔽列表屏蔽

而 nic.eu.org 的域名有报告说 HTTP 协议会被阻断（不过不用担心，Vercel 可以自动配置 SSL 证书）

#### 付费

购买域名十分简单，在网上搜索域名注册商，找到心仪的域名和价格进行付费即可

如果您愿意支持我的话，可以通过[此链接](https://www.namesilo.com/?rid=e858391zj)注册 namesilo 并购买域名，我能够从中获取一些佣金以支持域名费用

但如果后续考虑绑定国内服务器，我会推荐你从国内的注册商购买，备案会方便些

### 解析

关于域名解析，我只推荐使用 [Cloudflare](https://cloudflare.com)（不是别的不好，是没用过（逃

前往 https://dash.cloudflare.com 注册并登录帐号

在仪表盘中点击**添加站点**

![添加站点](/images/hexo-vercel-blog/cf-add-website.png)

在里面输入你注册的域名，如果出现 `your.domain is not a registered domain` 的提示建议等待半小时左右再试

计划选择 Free 足够

![计划选择](/images/hexo-vercel-blog/cf-plan.png)

在下一页的 DNS 记录中，可能会有注册商自带的一些记录，我的推荐是全部删除

![DNS 记录](/images/hexo-vercel-blog/cf-dns.jpg)

弹出的警告直接确认

![警告](/images/hexo-vercel-blog/cf-warn.png)

在下一页会要求将域名的名称服务器(即 Nameserver)更改为 Cloudflare 提供给你个人的服务器

![Nameserver](/images/hexo-vercel-blog/cf-nameserver.png)

这步操作因注册商而异，也请自行搜索

更改名称服务器需要花费一些时间，我的大部分域名在半小时左右设置成功

然后，在 Vercel 项目设置中添加你的域名

需要注意的是，如果直接使用顶级域名，Vercel 会询问你 www 的处理方式，我个人推荐将 www 跳转到根域(即第二个选项)

![顶级域名](/images/hexo-vercel-blog/vercel-domain.png)

然后，前往 [Cloudflare 仪表盘](https://dash.cloudflare.com)，选择你的域名，进入左侧的 **DNS** 页面

点击**添加记录**

**类型**根据 Vercel 的提示选择 `CNAME` 或 `A`

**名称**是域名的前缀，比如 `blog` 就会解析到 `blog.your.domain`，需要注意的是，根域应该使用 `@` 作为名称

**IPv4 地址** 或 **目标** 按照 Vercel 的要求输入

**代理状态**控制解析需不需要走 Cloudflare 的 CDN，但因为 Cloudflare CDN 在国内速度极为缓慢(cfcdn是减速器.webp)，所以我推荐你把它关掉

**TTL** 没有需要的话保持默认即可

在添加完成后，它应该会像是这样

![DNS 记录](/images/hexo-vercel-blog/cf-dns-record.png)

如果配置没问题的话，回到 Vercel 项目设置中就能够看到 Valid Configuration 了

这时就已经可以通过域名来访问你的博客了

如果在访问时提示不安全，请等待 Vercel 配置好 SSL 证书后再进行访问
