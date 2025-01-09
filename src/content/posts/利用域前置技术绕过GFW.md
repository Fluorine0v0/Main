---
title: 利用域前置技术绕过GFW
published: 2025-01-02
description: '在一些特殊的时期，你的科学上Internet服务不好用时，但又要下一些github上的东西或想上Pixiv时，这篇文章或许对你有所帮助。'
image: ''
tags: [机房适用,网站,网络]
category: 'Network'
draft: false 
---
# 利用域前置技术绕过 GFW

2020-05-31
Personal Notes

## 文章目录

----

*   [前言](#前言)
*   [原理](#原理)
*   [一些已经完整实现的开源项目](#一些已经完整实现的开源项目)
*   [教程（以Bypass-GFW-SNI + DNSCrypt-proxy 为例）](#教程以bypass-gfw-sni--dnscrypt-proxy-为例)
*   [注意事项](#注意事项)
*   [附录](#附录)

## 前言

在一些特殊的时期，你的科学上网服务不好用时，但又要下一些 github 上的东西或想上 Pixiv 时，这篇文章或许对你有所帮助。

## 原理

HTTPS（更准确来讲是 TLS）有一个扩展，使得在 Client Hello 阶段，浏览器会向服务器明文发送要访问网站的域名。而 GFW 根据这一原理对特定的 SNI 进行阻断，这就是俗称的 SNI 阻断。更多的可以去看：[维基百科](https://zh.wikipedia.org/wiki/%E6%9C%8D%E5%8B%99%E5%99%A8%E5%90%8D%E7%A7%B0%E6%8C%87%E7%A4%BA)。

而域前置 (Domain Fronting) 技术，就是利用有的服务器不验证 SNI 的这一特点，给 GFW 和服务器一个假的 SNI。因为 GFW 没有私钥，只能通过 SNI 和 IP 判断你要访问的网站，而假的 SNI 就能欺骗 GFW。但服务器本身有私钥，解开后，服务器是通过 HTTP 请求头判断你要访问哪个网站的。（但注意，特殊的比如虚拟主机、Cloudflare 免费版等能在一个 IP 上部署多个证书的，是一定要 SNI 的，否则会出错。这种唯一的绕过办法就是 ESNI，如果有空的话，以后再讲）因此可以通过这种方式绕过 GFW 的 SNI 阻断。

## 一些已经完整实现的开源项目

-------------
::github{repo="bypass-GFW-SNI/main"}
::github{repo="bypass-GFW-SNI/proxy"}
::github{repo="SeaHOH/GotoX"}
::github{repo="SpaceTimee/Sheas-Cealer"}

## 注意事项
----
有的网站比如 Twitter、Google 这类大型网站大多为 IP 黑洞，上述方式是没有用的。通常被 SNI 阻断的网站，也有 DNS 污染。请配合下面的其中一个：DNSCrypt、DNS over HTTPS (DoH)、DNS Over TLS (DoT) 使用。个人比较喜欢 `DNSCrypt-proxy` 和 `AdGuardHome`。其中 `Adguardhome` 对小白来说比较友好。
