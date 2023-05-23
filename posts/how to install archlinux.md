---
title: "How to Install Archlinux"
date: 2023-04-30T08:13:43+08:00
draft: false
tags: [linux]
categories: [linux]
---

## 安装

### 进入安装界面

![](../images/archinstall/01.png)

### 连接网络

![](../images/archinstall/02.png)
STD-wifi 是 wifi 名字,如果不知道 wifi 名字的可以用命令`station wlan0 scan` 和 `station wlan0 get-network`来查看可以连接的 wifi

> 进入`iwctl`后可以使用命令`device list`来查看设备名称

### 更新 mirrorlist

```
reflector --country China --sort rate --save /etc/pacman.d/mirrorlist
pacman -Syy
```

### 分区

`lsblk`查看磁盘信息
![](../images/archinstall/03.png)

使用`fdisk`分区
![](../images/archinstall/04.png)
n 代表新建一块分区，这里先建立第一块和第三块分区，分别作为 efi 和 swap 分区使用,这样做的好处是剩下的一个分区作为根目录可以不用计算大小直接使用默认值即可

> 按 p 可查看目前分区表情况
> 按 w 保存更改写入磁盘

### 格式化分区

使用 mkfs(make file system)工具

> **mkfs.+tab**查看可以格式化的格式

![](../images/archinstall/05.png)

```
mkfs.vfat /dev/sda1
mkfs.ext4 /dev/sda2
mkswap /dev/sda3
```

> 第一块分区作为 efi 必须使用 vfat 格式  
> 第二块分区可以使用 ext4 和 xfs。但是大部分 linux 系统使用是 ext4,也可以使用较新的 xfs(专门针对大文件的存储和读写)  
> 第三块直接使用 mkswap 即可

全部完成后`lsblk -f`查看是否成功

![](../images/archinstall/06.png)

### 挂载

![](../images/archinstall/07.png)

```
mount /dev/sda2 /mnt
mkdir -p /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi
swapon /dev/sda3
lsblk
```

### 安装系统

`pacstrap /mnt linux linux-firmware linux-headers base base-devel vim bash-completion`

> 相当于把新系统装到 mnt 挂在的第二个分区里面

### 生成表文件

```
genfstab -U /mnt
genfstab -U /mnt >> /mnt/etc/fstab
```

### 进入系统并安装包

```
arch-chroot /mnt
pacman -Syy
pacman -S grub efibootmgr efivar networkmanager amd-ucode
```

> 如果是 intel 处理器 将 amd-ucode 换成 intel-ucode

### 安装 grub 并配置

- 安装命令`grub-install /dev/sda`
- 修改配置`vim /etc/default/grub`

```
# GRUB boot loader configuration

GRUB_DEFAULT=0
GRUB_TIMEOUT=2
GRUB_DISTRIBUTOR="Arch"
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3"
GRUB_CMDLINE_LINUX=""

# Preload both GPT and MBR modules so that they are not missed
GRUB_PRELOAD_MODULES="part_gpt part_msdos"

# Uncomment to enable booting from LUKS encrypted devices
#GRUB_ENABLE_CRYPTODISK=y

# Set to 'countdown' or 'hidden' to change timeout behavior,
# press ESC key to display menu.
GRUB_TIMEOUT_STYLE=menu

# Uncomment to use basic console
GRUB_TERMINAL_INPUT=console

# Uncomment to disable graphical terminal
#GRUB_TERMINAL_OUTPUT=console

# The resolution used on graphical terminal
# note that you can use only modes which your graphic card supports via VBE
# you can see them in real GRUB with the command `videoinfo'
GRUB_GFXMODE=auto

# Uncomment to allow the kernel use the same resolution used by grub
GRUB_GFXPAYLOAD_LINUX=keep

# Uncomment if you want GRUB to pass to the Linux kernel the old parameter
# format "root=/dev/xxx" instead of "root=/dev/disk/by-uuid/xxx"
#GRUB_DISABLE_LINUX_UUID=true

# Uncomment to disable generation of recovery mode menu entries
GRUB_DISABLE_RECOVERY=true

# Uncomment and set to the desired menu colors.  Used by normal and wallpaper
# modes only.  Entries specified as foreground/background.
#GRUB_COLOR_NORMAL="light-blue/black"
#GRUB_COLOR_HIGHLIGHT="light-cyan/blue"

# Uncomment one of them for the gfx desired, a image background or a gfxtheme
#GRUB_BACKGROUND="/path/to/wallpaper"
#GRUB_THEME="/path/to/gfxtheme"

# Uncomment to get a beep at GRUB start
#GRUB_INIT_TUNE="480 440 1"

# Uncomment to make GRUB remember the last selection. This requires
# setting 'GRUB_DEFAULT=saved' above.
#GRUB_SAVEDEFAULT=true

# Uncomment to disable submenus in boot menu
#GRUB_DISABLE_SUBMENU=y

# Probing for other operating systems is disabled for security reasons. Read
# documentation on GRUB_DISABLE_OS_PROBER, if still want to enable this
# functionality install os-prober and uncomment to detect and include other
# operating systems.
#GRUB_DISABLE_OS_PROBER=false
```

> - `GRUB_TIMEOUT=2` 减少等待时间
> - `GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3"` 生成日志
> - `GRUB_GFXMODE=1920*1080` 分辨率

生成配置文件  
`grub-mkconfig -o /boot/grub/grub.cfg`

### 其他

- 网络配置`systemctl enable NetworkManager`

- 为 root 创建密码`passwd`,如果忘记了插入 usb 重新用 arch-chroot 进入系统后设置

- 退出新系统`exit`后，卸载挂载`umount -R /mnt`

### 完成

![](../images/archinstall/08.png)

现在一个新的 arlinux 系统就已经装好了！！！

## 配置

### 给 arch 取名

`vim /etc/hostname` 直接输入名字即可,重启后生效

### host 配置

`vim /etc/hosts`

```
# Static table lookup for hostnames.
# See hosts(5) for details.
127.0.0.1	localhost

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

### 同步时间

```
timedatectl set-timezone Asia/Shanghai
timedatectl set-ntp true
```

### 添加用户

#### 添加用户

```bash
# 用户名为 ***
useradd --create-home ***
passwd ***
usermod -aG wheel,users,storage,power,lp,adm,optical ***
```

> `id ***` 查看是否添加成功

#### 让\*\*\*能用 sudo

```
export EDITOR=vim
visudo
```

去掉`%whell ALL=(ALL:ALL) ALL`的注释

### 字体

#### 生成字体

- `sudo vim /etc/locale.gen` 将`en_US.UTF-8 UTF-8`和`zh_CN.UTF-8 UTF-8`的注释取消
- `sudo vim /etc/locale.conf` 写入`LANG=en_US.UTF-8`
- `sudo locale-gen` 生成字体

#### 安装字体

```
sudo pacman -S ttf-hack-nerd ttf-jetbrains-mono
sudo pacman -S ttf-hannom noto-fonts noto-fonts-extra noto-fonts-emoji noto-fonts-cjk adobe-source-sans-fonts adobe-source-serif-fonts adobe-source-han-sans-cn-fonts adobe-source-han-sans-hk-fonts adobe-source-han-sans-tw-fonts adobe-source-han-serif-cn-fonts
sudo pacman -S wqy-zenhei wqy-microhei
```

#### 字体引擎

- 安装 freetype `sudo pacman -S freetype2`
- `sudo vim /etc/profile.d/freetype2.sh`
- 将 export 的注释取消

### 显卡驱动

#### amd

`sudo pacman -S xf86-video-amdgpu xf86-video-ati mesa vulkan-radeon`

#### intel

`sudo pacman -S xf86-video-intel vulkan-intel mesa`

#### nvidia

`sudo pacman -S nvidia nvidia-settings nvidia-utils`

> linux 上尽量使用 amd 和 intel

### 声音

`sudo pacman -S alsa-utils`

### MAKEPKG

- `sudo vim /etc/makepkg.conf`
- 修改`MAKEFLAGS="-j$(nproc)"` 且取消注释

### AUR HELPER

```
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
```

### Xorg

- `sudo pacman -S xorg-server xorg-xinit feh udisks2 udiskie pcmanfm`
- `cp /etc/X11/xinit/xinitrc ~/.Xinitrc`
  > 删除最后几行没有用到的 exec 。
