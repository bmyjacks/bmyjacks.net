# 安装 iDRAC Service Module (iSM)

最近也是捡了一批垃圾回来折腾，其中包含了 R630 (13G) 与 R640 (14G) 两台服务器。在熟悉了 iDRAC 的基本功能后，发现主机名等 OS 内容无法同步到 iDRAC 上，这就需要安装 iSM 来解决。<del>两台服务器运行的系统均为 Proxmox VE 8，基于 Debian 12 Bookworm，运行的内核版本为 6.8.12。</del>

已经在下列系统下验证 iSM for iDRAC 9 14G：

- [RHEL  (AlmaLinux, Rocky Linux) 9/10](#rhel-910)
- [Debian 12](#debian-12)
- Archlinux

## 调整 OS to iDRAC Pass-through 设置

为了让系统中的 iSM 能与 iDRAC 进行通信，需要调整 iDRAC 的 OS to iDRAC Pass-through 设置。

=== "iDRAC 8 13G"

    在 iDRAC 8 13G 中，这项设置在

    Overview -> iDRAC Settings - > Network -> OS to iDRAC Pass-through

    ![iDRAC 8 13G OS to iDRAC Pass-through page](https://assets.bmyjacks.net/img/490129b6.avif)

=== "iDRAC 9 14G"

    在 iDRAC 9 14G 中，这项设置在

    iDRAC Settings - > Connectivity -> OS to iDRAC Pass-through

    ![iDRAC 9 14G OS to iDRAC Pass-through page](https://assets.bmyjacks.net/img/7b45ad4d.avif)

设置为 USB NIC 模式后，系统中会出现名为 `idrac` 的网络接口，MAC 地址前缀与 iDRAC 的 MAC 地址前缀相同。

## 获取 iSM 安装包

=== "iDRAC 8 13G"
    进入 [Dell Support](https://www.dell.com/support/home/en-us) 网站，搜索 **iDRAC Service Module for Linux, v4**

    寻找 [v4.3.0.0](https://www.dell.com/support/home/en-us/drivers/driversdetails?driverId=2PK7T) 版本，下载 [OM-iSM-Dell-Web-LX-4300-2781_A00.tar.gz](https://dl.dell.com/FOLDER08657945M/1/OM-iSM-Dell-Web-LX-4300-2781_A00.tar.gz).

=== "iDRAC 9 14G"
    进入 [Dell Support](https://www.dell.com/support/home/en-us) 网站，搜索 **iDRAC Service Module for Linux, v5**
    === "RHEL 9/10"
        寻找 [v5.4.1.1](https://www.dell.com/support/home/en-ly/drivers/driversdetails?driverId=HMT6D) 版本，下载 [OM-iSM-Dell-Web-LX-5411-3973.tar.gz](https://dl.dell.com/FOLDER13421811M/1/OM-iSM-Dell-Web-LX-5411-3973.tar.gz).
    === "Debian 12"
        寻找 [v5.3.1.0](https://www.dell.com/support/home/en-us/drivers/driversdetails?driverId=MMVDG) 版本，最新的 v5.4.0.0 版本由于 debian 12 缺少部分库 (libcurl4t64) 并且无法手动下载安装导致无法运行。下载 [OM-iSM-Dell-Web-LX-5310-3503_A00.tar.gz](https://dl.dell.com/FOLDER11789279M/1/OM-iSM-Dell-Web-LX-5310-3503_A00.tar.gz).

将下载的 `tar.gz` 文件 SFTP 到服务器上，直接在服务器上下载会显示 403 Forbidden :cry:.

## 安装 iSM

解压下载的安装包:

```bash
# 创建用于存放 iSM 的目录
mkdir ~/ism

# 解压安装包
tar -xvf OM-iSM-Dell-Web-LX-xxxx-xxxx.tar.gz -C ~/ism

# 进入解压后的目录
cd ~/ism
```

=== "iDRAC 8 13G"
    1. 安装依赖: `apt install ./OSC/dcism-osc-6.3.0.0-124.ubuntu20.deb`
    2. 安装 iSM: `apt install ./UBUNTU20/x86_64/dcism-4.3.0.0-2781.ubuntu20.deb`，此时系统会报错找不到 `libcrypto.so.1.1`
    3. 安装 `libssl1.1`: 前往 [packages.debian.org](https://packages.debian.org/bullseye/libssl1.1) 下载 [安装包](http://security.debian.org/debian-security/pool/updates/main/o/openssl/libssl1.1_1.1.1w-0+deb11u3_amd64.deb) (可以直接在系统内 `wget`，不会 403 了 :clap:)，然后执行 `apt install ./libssl1.1_1.1.1w-0+deb11u3_amd64.deb`

=== "iDRAC 9 14G"
    === "RHEL 9/10"
        1. 安装依赖: `dnf install ./OSC/dcism-osc-7.4.1.1-214.rpm`
        2. 安装 iSM: `dnf install ./RHEL9/x86_64/dcism-5.4.1.1-3973.el9.x86_64.rpm`

    === "Debian 12"
        1. 安装依赖: `apt install ./OSC/dcism-osc-7.3.1.0-153.ubuntu22.deb`
        2. 安装 iSM: `apt install ./UBUNTU22/x86_64/dcism-5.3.1.0-3503.ubuntu22.deb`

最后在 OS 内重启及查看 iSM 的状态：

```bash
systemctl restart dcismeng

systemctl status dcismeng
```

输出内容包含 `The iDRAC Service Module has successfully started communication with iDRAC.` 即安装完成。

在 iDRAC 中也可以查看更多的 OS 信息了：

=== "iDRAC 8 13G"
    ![iDRAC 8 13G system info](https://assets.bmyjacks.net/img/1510eb28.avif)

=== "iDRAC 9 14G"
    === "RHEL 9/10"
        ![iDRAC 9 14G system info](https://assets.bmyjacks.net/img/2025_07_30_16_42_38.avif)
    === "Debian 12"
        ![iDRAC 9 14G system info](https://assets.bmyjacks.net/img/1f69340d.avif)
