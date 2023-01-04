# archlinux笔记



## 禁用 reflector

---

```shell
systemctl stop reflector.service
```

## 更新系统时钟

---

```shell
timedatectl set-ntp true
timedatectl status
```

## 分区

---

| 分区    | 大小   |
|:-----:|:----:|
| /efi  | 1G   |
| /     | 100G |
| /home | ~    |

## 格式化

---

```shell
mkfs.ext4 /dev/sda*  # /|/home
mkfs.vfat /dev/sda*  # /efi
```

## 挂载

---

```shell
mount /dev/sdax  /mnt
mkdir /mnt/efi            #创建efi目录
mount /dev/sdax /mnt/efi
mkdir /mnt/home           #创建home目录
mount /dev/sdax /mnt/home
```

## 添加镜像源

---

编辑 /etc/pacman.d/mirrorlist

```shell
vim /etc/pacman.d/mirrorlist
```

在文件的最顶端添加

```shell
Server = https://mirrors.bfsu.edu.cn/archlinux/$repo/os/$arch
```

编辑 /etc/pacman.conf 将 `SigLevel = Required DatabaseOptional` 修改成 `SigLeve = Never` 

```shell
vim /etc/pacman.conf
```

## 安装系统和功能性软件

---

```shell
pacstrap /mnt base base-devel linux-zen linux-zen-headers linux-firmware 
pacstrap /mnt dhcpcd iwd vim bash-completion sudo
```

## 生成 fstab

---

```shell
genfstab -U /mnt >> /mnt/etc/fstab
```

检查一下

```shell
cat /mnt/etc/fstab
```

## change root

---

```shell
arch-chroot /mnt
```

## 设置时区

---

```shell
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

硬件时间设置

```shell
hwclock --systohc
```

## 设置 Locale 本地化

---

vim 编辑 /etc/locale.gen 去掉 `en_US.UTF-8` `zh_CH.UTF-8` 的注释符 `#`

```shell
vim /etc/locale.gen
```

生成 locale

```shell
locake-gen
```

向 /etc/locale.conf 导入

```shell
echo 'LANG=zh_CN.UTF-8'  > /etc/locale.conf
```

## 设置主机

---

在 /etc/hostname 设置主机名

```shell
vim /etc/hostname # 我这里设置的是ArchLinux
```

设置 /etc/hosts

```shell
vim /etc/hosts
```

输入

```shell
127.0.0.1   localhost
::1         localhost
127.0.1.1   ArchLinux
```

## 设置 root 密码

---

```shell
passwd root
```

## 安装引导和微码

---

```shell
pacman -S grub efibootmgr os-prober dosfstools intel-ucode
grub-install --efi-directory=/efi
```

## 打开多系统引导

---

编辑 /etc/default/grub

去掉 `GRUB_DISABLE_OS_PROBER=false` 前的注释符 `#`

```shell
vim /etc/default/grub
```

生成 grub 配置文件

```shell
grub-mkconfig -o /boot/grub/grub.cfg
```

## 重启

---

```shell
exit
reboot
```

## 启动网络

---

```shell
systemctl enable --now dhcpcd
```

## 建立非 root 用户

---

```shell
useradd -m -G wheel -s /bin/bash user # user 改成你的用户名
```

设置用户密码

```shell
passwd user # user 改成你的用户名
```

编辑 sudoers

```shell
EDITOR=vim visudo
```

去掉 `%wheel` 前面的 `#`

```shell
#%whell ALL=(ALL:ALL) ALL # 去掉#
```

## 打开 32 位库 添加 cn 源

---

编辑 /etc/pacman.conf

```、shell
vim /etc/pacman.conf
```

去掉[multilib]一节中两行的注释

在底部添加

```shell
[archlinuxcn]
Server = https://mirrors.bfsu.edu.cn/archlinuxcn/$arch
SigLeve = Never
```

## 安装 helper

---

我这里选的是 paru



```shell
sudo pacman -S paru
```

## 安装基础功能包

---

```shell
sudo pacman -S pipewire-pulse pipewire-alsa pipewire-jack
sudo pacman -S lib32-pipewire lib32-pipewire-jack
sudo pacman -S easyeffects
sudo pacman -S sof-firmware alsa-firmware alsa-ucm-conf
sudo pacman -S ntfs-3g
sudo pacman -S adobe-source-han-serif-cn-fonts wqy-zenhei
sudo pacman -S chromium
sudo pacman -S noto-fonts-cjk noto-fonts-emoji noto-fonts-extra
sudo pacman -S ark
sudo pacman -S p7zip unrar unarchiver lzop lrzip
sudo pacman -S git wget curl
```

## 安装登陆管理器

---

```shell
sudo pacman -S lightdm
paru -S lightdm-webkit-theme-aether
```

## 启用登陆管理器

---

```shell
systemctl enable lightdm.service
```

## 安装 dwm

---

详见 [GitHub - xingji-27/dwm: xingji-dwm](https://github.com/xingji-27/dwm)

## 安装 st

---

详见 [GitHub - xingji-27/st](https://github.com/xingji-27/st)
