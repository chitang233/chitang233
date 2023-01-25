---
title: acme.sh 使用教程
abbrlink: 1304
date: 2022-10-30 18:04:41
tags: 
    - acme.sh
    - SSL
    - 证书
    - 教程
---

## 准备

- 一台服务器 (用于自动续期)
- 一个邮箱
- 一个 Google 帐号 (如果准备使用 Google Trust Service)
- 或许需要一个代理
- 脑子

## 安装

<div class="info">

> 在下文所出现的所有代码示例中，均将 `my@example.com` 替换为你自己的邮箱

</div>

```shell
curl https://get.acme.sh | sh -s email=my@example.com
```

重启终端，即可使用 `acme.sh` 命令

## CA 提供商

### Let's Encrypt

Let's Encrypt 是 acme.sh 的默认 CA 提供商，无需额外配置即可使用

从其他 CA 切换到 Let's Encrypt:

```shell
acme.sh --set-default-ca --server letsencrypt
```

### ZeroSSL

[注册](https://app.zerossl.com/signup)一个 ZeroSSL 帐号

前往 https://app.zerossl.com/developer ，点击 <u>EAB Credentials for ACME Clients</u> 下方的 <u>Generate</u>，并保存好 `EAB KID` 和 `EAB HMAC Key`

最后，使用 acme.sh 注册并将默认 CA 切换到 `zerossl`：

```shell
acme.sh --register-account --server zerossl \
    --eab-kid xxxxxxxxxxxx \
    --eab-hmac-key xxxxxxxxx
acme.sh --set-default-ca --server zerossl
```

### Google Trust Service

<div class="info">

> 在本段中，分别将示例代码中的 `example-project` 替换为你自己所想要的项目名称，`example@gmail.com` 替换为你的 Google 帐号

</div>

使用任意方法安装 [Google Cloud CLI](https://cloud.google.com/sdk/docs/install)

登录 Google Account

```shell
gcloud auth login
```

创建一个 Google Cloud 项目并切换到该项目(如果没有)

```shell
gcloud projects create example-project
gcloud config set project example-project
```

给予自己权限

```shell
gcloud projects add-iam-policy-binding example-project \
  --member=user:example@gmail.com \
  --role=roles/publicca.externalAccountKeyCreator
```

启用 Public CA API

```shell
gcloud services enable publicca.googleapis.com
```

获取 EAB ID 和 HMAC

```shell
gcloud beta publicca external-account-keys create
```

保存好 `b64MacKey` 和 `keyId`，它们分别对应 `EAB HMAC Key` 和 `EAB KID`

最后，使用 acme.sh 注册并将默认 CA 切换到 `google`：

```shell
acme.sh --register-account -m my@example.com --server google \
    --eab-kid xxxxxxx \
    --eab-hmac-key xxxxxxx
```

## 域名所有权验证方式

<div class="warning">

> 本节内的示例代码均不能直接运行，而是一个范本，用于介绍验证相关的参数，配合下文的申请部分使用

</div>

### DNS API

<div class="info">

> 本文仅有 Cloudflare 的用法
> 同时，来自 Freenom 的域名无法使用 Cloudflare API，故无法通过该方式验证

</div>

前往 [Cloudflare API 令牌](https://dash.cloudflare.com/profile/api-tokens)页面

点击 <u>创建令牌</u> 并选择 <u>编辑区域 DNS</u> 右边的 <u>使用模板</u>

将 <u>区域资源</u> 改为 <u>所有区域</u>

剩余选项保持默认，创建令牌

保存好刚创建的令牌，在服务器中配置环境变量

```shell
export CF_Key="xxxxxxxxxxx"
export CF_Email="mycf@example.com"
acme.sh --issue --dns dns_cf
```

将 `xxxxxxxxxxx` 替换为刚刚创建好的令牌，`mycf@example.com` 替换为你的 Cloudflare 登录邮箱

### HTTP API

<div class="info">

> 此方法需要服务器对公网开放 TCP 80 端口

</div>

#### Webroot

<div class="info">

> 此方法适用于已有 Web 服务器的用户

</div>

```shell
acme.sh --issue -w /srv/www/example.com
```

将 `/srv/www/example.com` 替换为你自己的 Web 根目录

#### Standalone

让 acme.sh 自己作为一个临时的 Web 服务器用于验证

```shell
acme.sh --issue --standalone
```

## 申请证书

<div class="info">

> 在示例代码的 `--issue` 后面加上你的所有权验证方式的参数，例如 `--dns dns_cf` 或 `--standalone`

</div>

申请单个域名:

```shell
acme.sh --issue -d example.com
```

申请多个域名:

```shell
acme.sh --issue -d example1.com \
    -d example2.com \
    -d example3.com
```

申请通配符域名:

```shell
acme.sh --issue -d example.com \
    -d "*.example.com"
```

加入 `--keylength ec-384` 可以使用 ECC 证书

## 安装证书

```shell
acme.sh --install-cert -d example.com \
    --fullchain-file   /path/to/cert/cert.pem \
    --key-file          /path/to/cert/key.pem \
```

加入 `--ecc` 参数可以使用 ECC 证书
