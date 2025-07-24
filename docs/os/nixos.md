# NixOS Minimal 安装指南

本文介绍如何安装 NixOS 的命令行版本（Minimal）。NixOS 是一个基于 Nix 包管理器的 Linux 发行版，具有声明式配置等独特特性。

!!! note
    本文使用的 NixOS 版本为 **24.11**。

## 安装前的准备

### 制作可启动的 NixOS U 盘

#### 下载 ISO

前往 [NixOS 官方网站](https://nixos.org/download/#nixos-iso) 下载最新的 NixOS ISO，选择 Minimal 版本。也可以直接使用以下链接下载：

- [NixOS 24.11 Minimal ISO for amd64](https://channels.nixos.org/nixos-24.11/latest-nixos-minimal-x86_64-linux.iso)
- [SHA-256](https://channels.nixos.org/nixos-24.11/latest-nixos-minimal-x86_64-linux.iso.sha256)

#### 制作启动 U 盘

推荐使用 [Ventoy](https://www.ventoy.net/en/index.html) 制作启动 U 盘。Ventoy 支持多系统 ISO，可以将多个 ISO 文件直接拷贝到 U 盘。只需将下载并校验好的 NixOS ISO 拷贝到 Ventoy 制作的 U 盘即可。

## 安装过程

从 U 盘启动电脑，选择 NixOS 进入安装界面。

??? note  "使用 SSH 远程连接"

    由于 `nixos` 和 `root` 用户默认密码均为空，建议先用 `passwd` 命令设置密码后再通过 SSH 连接。你可以在另一台电脑上通过 SSH 连接到 NixOS 安装界面。

    ```sh
    # 为当前用户（默认是 nixos）设置密码
    passwd

    # 然后通过 SSH 连接
    ssh nixos@<ip_address>
    ```

### 检查网络连接

使用 `ping` 命令检查网络是否畅通：

```sh
ping -c 5 ping.ui.com
```

### 磁盘分区

使用 `parted` 命令进行磁盘分区。

!!! danger
    分区前请备份数据，分区操作会清空磁盘上的所有数据。

假设安装 NixOS 的磁盘为 `/dev/nvme0n1`，并希望根目录使用 `btrfs` 文件系统。可按如下命令分区：

```sh
$ sudo parted /dev/nvme0n1

(parted) mklabel gpt # 创建 GPT 分区表
(parted) mkpart "EFI system partition" fat32 1MiB 513MiB # 创建 EFI 分区
(parted) set 1 esp on # 设置 EFI 分区标志
(parted) mkpart "root partition" btrfs 513MiB 100% # 创建根分区
(parted) quit # 退出 parted
```

分区完成后，格式化分区：

```sh
# 格式化 EFI 分区
sudo mkfs.fat -F 32 /dev/nvme0n1p1

# 格式化根分区
sudo mkfs.btrfs /dev/nvme0n1p2
```

由于使用 `btrfs` 文件系统，需要创建子卷。例如创建 `@` 子卷：

```sh
# 挂载根分区
sudo mount /dev/nvme0n1p2 /mnt

# 创建子卷
sudo btrfs subvolume create /mnt/@

# 卸载根分区
sudo umount /mnt
```

### 挂载分区

```sh
# 使用 @ 子卷并启用 zstd:1 压缩挂载根分区到 /mnt
sudo mount -o compress=zstd:1,subvol=@ /dev/nvme0n1p2 /mnt

# 创建 boot 挂载点
sudo mkdir -p /mnt/boot

# 挂载 EFI 分区到 /mnt/boot
sudo mount -o umask=077 /dev/nvme0n1p1 /mnt/boot
```

### 生成并调整 NixOS 配置文件

使用 `sudo nixos-generate-config --root /mnt` 命令生成 NixOS 配置文件。

- `/mnt/etc/nixos/hardware-configuration.nix`：硬件配置文件，包含系统硬件信息。
- `/mnt/etc/nixos/configuration.nix`：系统配置文件，包含基本系统设置。

#### 修改 `hardware-configuration.nix`

在 `/mnt/etc/nixos/hardware-configuration.nix` 中添加如下内容：

修改如下：

```nix
# 根据需要调整根目录挂载选项
options = [ "subvol=@" "compress=zstd:1" "lazytime" ];
```

#### 修改 `configuration.nix`

在 `/mnt/etc/nixos/configuration.nix` 中添加如下内容：

```nix
networking.hostName = "<YOUR_HOSTNAME>"; # 设置主机名
networking.networkmanager.enable = true; # 启用 NetworkManager

time.timeZone = "<YOUR_TIMEZONE>"; # 设置时区，如 Asia/Shanghai、America/Chicago 等

# 设置用户账户
users.users.<YOUR_USERNAME> = {
  isNormalUser = true; # 普通用户
  extraGroups = [ "wheel" ]; # 加入 wheel 组以获得 sudo 权限
  packages = with pkgs; [
    # 添加需要的软件包
    git
    vim
  ];
};

# 网络较慢时可添加镜像源，这里以中科大镜像为例
nix.settings.substituters = [ "https://mirrors.ustc.edu.cn/nix-channels/store" ];

# 设置系统级安装的软件包
environment.systemPackages = with pkgs; [
  vim
  wget
  htop
];

# 启用 SSH 服务
services.openssh.enable = true;

# 将当前配置复制到 /run/current-system/configuration.nix 防止误删
system.copySystemConfiguration = true;
```

### 安装 NixOS

调整配置文件后，使用以下命令开始安装：

```sh
sudo nixos-install # 安装过程中会提示设置 root 密码，使用 --no-root-passwd 可跳过
```

安装完成后，不要忘记为你的账户设置密码：

```sh
nixos-enter --root /mnt -c 'passwd <YOUR_USERNAME>'
```

最后，重启进入新安装的 NixOS 系统：

```sh
sudo reboot
```

```sh
$ nix-shell -p fastfetch

$ fastfetch

          ▗▄▄▄       ▗▄▄▄▄    ▄▄▄▖             <YOUR_USERNAME>@<YOUR_HOSTNAME>
          ▜███▙       ▜███▙  ▟███▛             --------------
           ▜███▙       ▜███▙▟███▛              OS: NixOS 24.11 (Vicuna) x86_64
            ▜███▙       ▜██████▛               Host: <YOUR_DEVICE_NAME>
     ▟█████████████████▙ ▜████▛     ▟▙         Kernel: Linux 6.6.84
    ▟███████████████████▙ ▜███▙    ▟██▙        Uptime: 12 hours, 34 mins
           ▄▄▄▄▖           ▜███▙  ▟███▛        Packages: 567 (nix-system)
          ▟███▛             ▜██▛ ▟███▛         Shell: bash 5.2.37
         ▟███▛               ▜▛ ▟███▛          Terminal: /dev/pts/0
▟███████████▛                  ▟██████████▙    CPU: Intel(R) Core(TM) i3-9100T (4) @ 3.70 GHz
▜██████████▛                  ▟███████████▛    GPU: Intel UHD Graphics 630 @ 1.10 GHz [集成]
      ▟███▛ ▟▙               ▟███▛             Memory: 409.95 MiB / 7.51 GiB (5%)
     ▟███▛ ▟██▙             ▟███▛              Swap: Disabled
    ▟███▛  ▜███▙           ▝▀▀▀▀               Disk (/): 2.41 GiB / 465.26 GiB (1%) - btrfs
    ▜██▛    ▜███▙ ▜██████████████████▛         Local IP (eno1): 192.168.0.2/24
     ▜▛     ▟████▙ ▜████████████████▛          Locale: en_US.UTF-8
           ▟██████▙       ▜███▙
          ▟███▛▜███▙       ▜███▙                                       
         ▟███▛  ▜███▙       ▜███▙                                      
         ▝▀▀▀    ▀▀▀▀▘       ▀▀▀▘
```
