---
title: "How to Solve Cannot Access to Github"
date: 2023-04-29T10:56:15+08:00
draft: false
tags: [git]
categories: [git]
---

## 什么是 DNS 污染呢？

本来很多域名对应的 IP 地址都是由上游可信赖的服务器提供的，这样可以降低网络上的流量压力 但是对于有些攻击来说，
他就去污染可信赖服务器里数据包，这样我一请求 github，都给我登上了错误的服务器或服务器的网址

## github 无法访问解决办法

### 加速器

网上有各种各样的加速器来解决，但是大部分都先体验再收费

### 修改本地 host

既然上游服务器的域名地址被修改了，那我们直接在本地 hosts 文件了指定好域名对应的 IP 地址不就好了嘛 hosts 文件在每个系统的位置不一，详情如下：

- Windows 系统：C:\Windows\System32\drivers\etc\hosts
- Linux 系统：/etc/hosts
- Mac（苹果电脑）系统：/etc/hosts
- Android（安卓）系统：/system/etc/hosts
- iPhone（iOS）系统：/etc/hosts 然后将下面这些数据拷贝到自己电脑的 hosts 文件，再激活生效就可以了

[https://github.com/521xueweihan/GitHub520](https://github.com/521xueweihan/GitHub520)最新的 github dns

```
140.82.114.25                 alive.github.com
140.82.112.25                 live.github.com
185.199.108.154               github.githubassets.com
140.82.112.22                 central.github.com
185.199.108.133               desktop.githubusercontent.com
185.199.108.153               assets-cdn.github.com
185.199.108.133               camo.githubusercontent.com
185.199.108.133               github.map.fastly.net
199.232.69.194                github.global.ssl.fastly.net
140.82.112.4                  gist.github.com
185.199.108.153               github.io
140.82.114.4                  github.com
192.0.66.2                    github.blog
140.82.112.6                  api.github.com
185.199.108.133               raw.githubusercontent.com
185.199.108.133               user-images.githubusercontent.com
185.199.108.133               favicons.githubusercontent.com
185.199.108.133               avatars5.githubusercontent.com
185.199.108.133               avatars4.githubusercontent.com
185.199.108.133               avatars3.githubusercontent.com
185.199.108.133               avatars2.githubusercontent.com
185.199.108.133               avatars1.githubusercontent.com
185.199.108.133               avatars0.githubusercontent.com
185.199.108.133               avatars.githubusercontent.com
140.82.112.10                 codeload.github.com
52.217.223.17                 github-cloud.s3.amazonaws.com
52.217.199.41                 github-com.s3.amazonaws.com
52.217.93.164                 github-production-release-asset-2e65be.s3.amazonaws.com
52.217.174.129                github-production-user-asset-6210df.s3.amazonaws.com
52.217.129.153                github-production-repository-file-5c1aeb.s3.amazonaws.com
185.199.108.153               githubstatus.com
64.71.144.202                 github.community
23.100.27.125                 github.dev
185.199.108.133               media.githubusercontent.com
```

大部分情况下是直接生效，如未生效可尝试下面的办法，刷新 DNS：

- Windows：在 CMD 窗口输入：ipconfig /flushdns
- Linux 命令：sudo nscd restart，如报错则须安装：sudo apt install nscd 或 sudo /etc/init.d/nscd restart
- Mac 命令：sudo killall -HUP mDNSResponder 如果还没有生效的话，可以尝试重启一下电脑，毕竟这步操作可以解决 95 % 的电脑 bug
- 当然还有一些自动修改 hosts 文件的软件，但是有一定的风险性，所以如果手动修改 hosts 也没用的话，建议再参考一下镜像通道

### 镜像通道

如果手动修改 hosts 还不行的话，可以尝试使用镜像通道
例如原始用法是：
`git clone https://github.com/PaddlePaddle/PaddleDetection`

那加速通道就是：  
`git clone https://hub.fastgit.org/PaddlePaddle/PaddleDetection.git`

也就是 github clone 加速的时候，前面的域名用`https://hub.fastgit.org/来替换掉https://github.com`就行啦!
当然，对于镜像通道也可以使用 fastgit 的开源软件：`https://github.com/xljiulang/FastGithub`
