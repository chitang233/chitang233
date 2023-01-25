---
title: 常用的 DNS 服务器
tags:
  - DNS
  - DoH
  - DoT
  - DoQ
  - DNS64
abbrlink: 29556
date: 2022-09-27 20:31:36
---

## [Cloudflare 1.1.1.1](https://1.1.1.1/dns/)

```
1.1.1.1
```

```
1.0.0.1
```

```
2606:4700:4700::1111
```

```
2606:4700:4700::1001
```

### DoH

```
https://1.1.1.1/dns-query
```

```
https://1.0.0.1/dns-query
```

## [DNSPod Public DNS](https://www.dnspod.cn/Products/publicdns)

```
119.29.29.29
```

```
2402:4e00::
```

### DoH

```
https://doh.pub/dns-query
```

```
https://1.12.12.12/dns-query
```

```
https://120.53.53.53/dns-query
```

### DoT

```
dot.pub
```

```
1.12.12.12
```

```
120.53.53.53
```

## [Google Public DNS](https://dns.google)

```
8.8.8.8
```

```
8.8.4.4
```

```
2001:4860:4860::8888
```

```
2001:4860:4860::8844
```

### DoH

```
https://dns.google/dns-query
```

### DoT

```
dns.google
```

### DNS64

```
2001:4860:4860::6464
```

```
2001:4860:4860::64
```

#### DoH

```
https://dns64.dns.google/dns-query
```

#### DoT

```
dns64.dns.google
```

## [AliDNS](https://alidns.com/)

```
223.5.5.5
```

```
223.6.6.6
```

```
2400:3200::1
```

```
2400:3200:baba::1
```

### DoH

```
https://dns.alidns.com/dns-query
```

### DoT

```
dns.alidns.com
```

## [AdGuard DNS](https://adguard-dns.io/zh_cn/public-dns.html)

### 默认

> AdGuard DNS 拦截广告和跟踪器。

```
94.140.14.14
```

```
94.140.15.15
```

```
2a10:50c0::ad1:ff
```

```
2a10:50c0::ad2:ff
```

#### DoH

```
https://dns.adguard-dns.com/dns-query
```

#### DoT

```
dns.adguard-dns.com
```

#### DoQ

```
quic://dns.adguard-dns.com
```

### 无过滤

> AdGuard DNS 不拦截广告、跟踪器或其他任何 DNS 请求。

```
94.140.14.140
```

```
94.140.14.141
```

```
2a10:50c0::1:ff
```

```
2a10:50c0::2:ff
```

#### DoH

```
https://unfiltered.adguard-dns.com/dns-query
```

#### DoT

```
unfiltered.adguard-dns.com
```

#### DoQ

```
quic://unfiltered.adguard-dns.com
```

### 家庭保护

> AdGuard DNS 拦截广告、跟踪器、成人内容，并在可能的情况下启用安全搜索和安全模式。

```
94.140.14.15
```

```
94.140.15.16
```

```
2a10:50c0::bad1:ff
```

```
2a10:50c0::bad2:ff
```

#### DoH

```
https://family.adguard-dns.com/dns-query
```

#### DoT

```
family.adguard-dns.com
```

#### DoQ

```
quic://family.adguard-dns.com
```
