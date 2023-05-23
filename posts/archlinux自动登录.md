---
title: "Archlinux自动登录"
date: 2023-05-03T17:54:39+08:00
draft: false
tags: [linux]
categories: [linux]
toc: false
---

### 自动登录

执行 systemctl edit getty@tty1 或者执行`vim /etc/systemd/system/getty.target.wants/getty@tty1.service`  
把 ExecStart 改成
`ExecStart=-/sbin/agetty -o '-p -f 用户名' -n -a 用户名 --noclear %I $TERM`

记得你进入系统后，改完千万先别重启，先切到 tty2 看下效果。行的话再继续，不行就改回去，这里弄错了麻烦的很

> tips: `ctl+alt+F2` 切换到 tty2

### 自动启动 startx

`nvim ~/.zprofile`

```
if [ -z "${DISPLAY}" ] && [ "${XDG_VTNR}" -eq 1 ]; then
  exec startx
fi
```

<br>

ok, that's all, 你现在拥有闪电般的开机速度了
