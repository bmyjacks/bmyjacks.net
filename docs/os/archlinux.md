# Archlinux 安装指南

## 获取 Archlinux ISO 镜像

可以从 [Archlinux 官网](https://archlinux.org/download/) 下载最新的 ISO 镜像。这个页面的顶部包含了 BitTorrent 下载链接，需要往下滚动才能使用 HTTP(S) 下载链接。

??? note "详细下载步骤"
    1. 访问 [Archlinux 下载页面](https://archlinux.org/download/)。
    2. 向下滚动寻找适合您的镜像站，例如 [China 列表里的](https://archlinux.org/download/#:~:text=hnd.cl-,China,-aliyun.com)，点击链接。
    3. 在镜像站的页面中，寻找以 `archlinux-YYYY.MM.DD-x86_64.iso` 命名的文件，点击下载。

### 校验 ISO 镜像

下载完成后，建议校验 ISO 镜像的完整性。可以使用 `sha256sum` 命令来计算 SHA-256 校验和，并与 [Archlinux 官方提供的校验和](https://archlinux.org/download/#:~:text=PGP%20fingerprint%3A%200x54449A5C-,SHA256) 进行对比。

```bash
sha256sum archlinux-YYYY.MM.DD-x86_64.iso
```

## 制作启动盘

使用 [Ventoy](https://github.com/ventoy/Ventoy) 制作启动盘是一个简单的选择。Ventoy 支持多种操作系统的 ISO 镜像，可以直接将下载的 Archlinux ISO 镜像复制到 Ventoy 启动盘中。

如果 Ventoy 太花哨，也可以使用 [Balena Etcher](https://github.com/balena-io/etcher)，[Rufus](https://rufus.ie/) 或者 `dd` 命令来制作启动盘。

## 启动 archiso

将制作好的启动盘插入电脑，重启并进入 One-Time Boot Menu (通常可以通过按 {% button #, F12 %}、{% button #, F11 %}、{% button #, F10 %}、{% button #, Del %} 或 {% button #, Esc %} 键进入，请查看对应主板的说明书或在线查找)，选择 USB 启动。

## 安装

!!! note
    可以使用 SSH 连接到 Archlinux 安装环境，方便在安装过程中查看文档或进行其他操作。

    需要先在 archiso 环境中更改 `root` 用户的密码：

    ```sh
    passwd
    ```

    输入两次更改后的密码（输入时不会显示，并且此密码仅用于当前会话，换句话说，重启后、安装后**都会失效**，所以可以使用弱密码如`123`等）。 **请不要使用空密码**，否则 SSH 连接会失败。

    **请不要在安装后使用弱密码**。

### 格式化磁盘并挂载分区

使用 `lsblk` 命令查看磁盘信息，找到要安装 Archlinux 的磁盘（对于 SATA 磁盘通常是 `/dev/sda`，对于 NVMe 磁盘通常是 `/dev/nvme0n1`）。下文以 `/dev/nvme0n1` 为例，使用 [BTRFS](https://btrfs.readthedocs.io/en/latest/) 文件系统。

!!! danger
    **请确保您选择了正确的磁盘！** 格式化磁盘会删除对应的硬盘中所有数据。

#### 格式化磁盘

```sh
wipefs -af /dev/nvme0n1
```

如果磁盘支持 Memory cell clearing，请参阅 [Solid_state_drive/Memory_cell_clearing#NVMe_drive](https://wiki.archlinux.org/title/Solid_state_drive/Memory_cell_clearing#NVMe_drive) 进行操作。

#### 分区磁盘

使用 `parted` 工具分区磁盘：

```sh
parted /dev/nvme0n1
```

在 `parted` 命令行（以 `(parted)` 提示符开始）中输入以下命令：

```sh
# 创建 GPT 分区表
mklabel gpt

# 创建 EFI 系统分区
mkpart "EFI system partition" fat32 1MiB 513MiB

# 设置第一个分区为 ESP 分区
set 1 esp on 

# 创建根分区
mkpart "root partition" btrfs 513MiB 100% 

# 检查分区信息
print

# 退出 parted
quit
```

#### 格式化分区

```sh
# 格式化 EFI 系统分区
mkfs.fat -F 32 /dev/nvme0n1p1 

# 格式化根分区
mkfs.btrfs -f /dev/nvme0n1p2 
```

#### 创建 subvolume

```sh
# 挂载根分区
mount /dev/nvme0n1p2 /mnt

# 创建根分区的 subvolume
btrfs subvolume create /mnt/@

# 创建 home 分区的 subvolume
btrfs subvolume create /mnt/@home

# 卸载根分区
umount /mnt
```

#### 挂载分区

启用 [BTRFS 的压缩功能](https://btrfs.readthedocs.io/en/latest/Compression.html)。

```sh
# 挂载根分区
mount -o compress=zstd,subvol=@ /dev/nvme0n1p2 /mnt

# 挂载 home 分区
mkdir -p /mnt/home
mount -o compress=zstd,subvol=@home /dev/nvme0n1p2 /mnt/home

# 挂载 EFI 系统分区
mkdir -p /mnt/efi
mount /dev/nvme0n1p1 /mnt/efi
```

### 安装基本系统

#### 更换镜像源

如果网络状况不佳，可以更换镜像源。编辑 `/etc/pacman.d/mirrorlist` 文件。（此更改会在安装后保留）

```sh
vim /etc/pacman.d/mirrorlist
```

之后按下 {% button #, ggdG %} 删除所有内容，然后按下 {% button #, i %} 进入插入模式，粘贴最近的镜像源，例如 [USTC 的镜像源](https://mirrors.ustc.edu.cn/help/archlinux.html)：

```plaintext
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
```

按下 {% button #, Esc %} 键退出插入模式，然后输入 {% button #, :wq %} 保存并退出。

#### 使用 `pacstrap` 安装基本系统

```sh
pacstrap -K /mnt base base-devel linux-lts linux-firmware git btrfs-progs grub efibootmgr grub-btrfs inotify-tools vim openssh man sudo
```

#### 生成 fstab 文件

```sh
# 生成 fstab 文件
genfstab -U /mnt >> /mnt/etc/fstab

# 检查文件内容
cat /mnt/etc/fstab
```

## 配置新系统

### 切换到新系统

```sh
arch-chroot /mnt
```

### 设置时间

```sh
# 设置时区为 Asia/Shanghai
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# 同步硬件时钟
hwclock --systohc
```

### 设置语言

编辑 `/etc/locale.gen` 文件，取消注释所需要的语言行：

```sh
vim /etc/locale.gen
```

取消注释以下行（按下 {% button #, gg %} 进入文件开头， {% button #, / %} 搜索 `en_US.UTF-8`，按下 {% button #, Enter %} 后，使用 {% button #, X %} 删除行首的 `#`，最后 {% button #, ESC %} 退出插入模式， {% button #, :wq %} 保存并退出）：

```plaintext
en_US.UTF-8 UTF-8
```

接着生成语言文件：

```sh
locale-gen
```

最后设置 `LANG` 环境变量：

```sh
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

### 网络配置

#### 设置主机名

```sh
echo "archlinux" > /etc/hostname
```

#### 设置网络端口

编辑 `/etc/systemd/network/20-wired.network` 文件，添加：

```plaintext
[Match]
Name=en*

[Link]
RequiredForOnline=routable

[Network]
DHCP=yes
```

接着启用对应的服务：

```sh
systemctl enable systemd-networkd.service
systemctl enable systemd-resolved.service
```

关于 `systemd-networkd` 的更多配置（如配置静态IP等），请参考 [systemd-networkd](https://wiki.archlinux.org/title/Systemd-networkd)。

如果不想使用 `systemd-networkd`，可以使用 [NetworkManager](https://wiki.archlinux.org/title/NetworkManager) 或者 [dhcpcd](https://wiki.archlinux.org/title/Dhcpcd) 等，他们的区别详见 [Network_configuration#Network_managers](https://wiki.archlinux.org/title/Network_configuration#Network_managers)

{% note info %}

如果后续使用时，遇到 `127.0.0.1:53` 的 DNS 错误，请使用 [adguardhome 的 docker镜像](https://hub.docker.com/r/adguard/adguardhome#resolved-daemon) 中提到的方法解决。

{% endnote %}

### 设置密码

如果需要设置 `root` 用户的密码，可以使用以下命令：

```sh
passwd
```

### 创建新用户

创建一个新的用户（例如 `user`）并设置密码：

```sh
# 创建用户并添加到 wheel 组
useradd -mG wheel user

# 设置用户密码
passwd user
```

### 配置 sudo

使用 `visudo` 命令编辑 sudoers 文件：

```sh
EDITOR=vim visudo
```

查找以下行：

```plaintext
# %wheel ALL=(ALL) ALL
```

取消注释（删除行首的 `#`），**请保留%符号**，这样匹配的是 `wheel` 组的用户而不是`wheel`这个用户：

```plaintext
%wheel ALL=(ALL) ALL
```

保存并退出。

### 安装引导加载程序

#### 安装 GRUB

```sh
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=GRUB
```

如果您的电脑使用非标准UEFI（例如[Dell Wyse 3040](https://www.dell.com/support/product-details/en-hk/product/wyse-3040-thin-client/overview)），请添加 `--removable` 选项：

```sh
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=GRUB --removable
```

#### 配置 GRUB

```sh
grub-mkconfig -o /boot/grub/grub.cfg
```

### 启用 SSH 服务

```sh
systemctl enable sshd.service
```

## 卸载磁盘并重新启动

```sh
# 退出 chroot 环境
exit

# 卸载所有挂载的分区
umount -R /mnt

# 重新启动系统
reboot
```

之后就可以使用新安装的 Archlinux 系统了。
