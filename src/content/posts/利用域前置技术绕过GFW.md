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

## 教程（以Bypass-GFW-SNI + DNSCrypt-proxy 为例）

---------------------------------------

> 如果觉得教程困难的话，可以去评论区下载已打包好的配置文件与程序

**Step1.** 去项目的 Release 页面下载已编译好的文件

[Bypass-GFW-SNI](https://github.com/bypass-GFW-SNI/main/releases) 还有 [DNSCrypt-Proxy](https://github.com/DNSCrypt/dnscrypt-proxy/releases)

注：可能最新版本与本文有一点差异，但大致上方法都是相同的。

**Step2.** 配置 DNSCrypt

*   设置 `fallback_resolver`，即回落 DNS 为你运营商的 DNS，比如 `114.114.114.114:53`（：后面的是端口号，一般情况下 DNS 都是用 UDP 53 端口的）
*   设置 `netprobe_address`，即网络检测地址为国内能访问的地址，比如 `114.114.114.114:53`
*   把 `[sources]` 里的全部删掉或在前面加 `#` 号，因为 `raw.githubusercontent.com` 这个域名已经被 SNI 阻断了，这段留着，打开 DNSCrypt 可能会很慢。
*   在 `[static]` 里填上
    ```
    [static.'Cloudflare-DNS-DoH']
    stamp = 'sdns://AgcAAAAAAAAABzEuMC4wLjESZG5zLmNsb3VkZmxhcmUuY29tCi9kbnMtcXVlcnk'
    [static.'Adguard-DNS-DoH']
    stamp = 'sdns://AgMAAAAAAAAADzE3Ni4xMDMuMTMwLjEzMCD5_zfwLmMstzhwJcB-V5CKPTcbfJXYzdA5DeIx7ZQ6Eg9kbnMuYWRndWFyZC5jb20KL2Rucy1xdWVyeQ'
    ```
*   将 `listen_addresses` 改为 `127.0.0.1:5353`（因为 53 端口要给 Bypass-GFW-SNI 用）
*   `server_names` 调整为 `['Adguard-DNS-DoH', 'Cloudflare-DNS-DoH']`
*   保存 `dnscrypt-proxy.toml`，双击 `dnscrypt-proxy.exe` 运行。如果黑色的窗口一直在，就说明没有问题。

**Step3.** 配置 Bypass-GFW-SNI

这个配置十分简单，具体的可以去看看程序自带的帮助，如果是懒人包的话双击 `MITMCA.crt` 先导入证书 (不会的 Google 如何导入根证书或看[这里](https://zhuanlan.zhihu.com/p/37264839)把 `CA.crt` 换成 `MITMCA.crt` 就是了)，同目录下的 `MITMCA.key` 是 CA 的私钥（不信任我的，自己去 Google 一下 OpenSSL 创建 CA），运行 `StartProgram.bat`，把电脑的 DNS 改成首选 `127.0.0.1`，备选为空（不会的也请 Google 如何修改 DNS 或看[这里](https://baijiahao.baidu.com/s?id=1612906041166793439)）。用浏览器打开 `https://www.pixiv.net`，Done。

## 注意事项
----
有的网站比如 Twitter、Google 这类大型网站大多为 IP 黑洞，上述方式是没有用的。通常被 SNI 阻断的网站，也有 DNS 污染。请配合下面的其中一个：DNSCrypt、DNS over HTTPS (DoH)、DNS Over TLS (DoT) 使用。个人比较喜欢 `DNSCrypt-proxy` 和 `AdGuardHome`。其中 `Adguardhome` 对小白来说比较友好。
