---
title: "Linux外接屏亮度调节"
date: 2023-05-02T00:17:23+08:00
draft: false
tags: [linux]
categories: [linux]
toc: false
---

#### Brightness Controller

下载[Brightness Controller](https://github.com/lordamit/Brightness) 即可调节

<br>

**Ubuntu and derivatives**

```bash
sudo add-apt-repository ppa:apandada1/brightness-controller
sudo apt update
sudo apt install brightness-controller
```

**Archlinux and derivatives**  
`paru -S brightness-controller-git`

![BrightnessController](../images/BrightnessController.png)

#### xrandr

使用`xrandr | grep -v disconnected | grep connected`来查看连接的设备  
调节亮度 `xrandr --output *** --brightness 0.5`
