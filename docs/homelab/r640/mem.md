# 内存折腾日记

## 持久内存

Dell 14G 服务器只支持第一代 Intel Optane 持久内存（PMem），而要使用持久内存，CPU 必须为第二代 Xeon Scalable 处理器（Cascade Lake）的金牌或铂金系列，我的 6230 正好在这个范围内，因此购入 12 根 NMA1XXD128GPS 模块。

### 安装插槽

根据 [Dell 的手册](https://www.dell.com/support/manuals/en-hk/poweredge-r640/dcpmm_ug_pub/pmem-recommended-topologies)，12 x 128GB PMem + 12 x 16GB RDIMM 的配置需要将 RDIMM 安装至白色插槽（两侧白色卡扣），PMem 安装至黑色插槽上。

### BIOS 设置

安装好后，第一次启动时，可能会提示 PMem 的配置错误（卖家通常不会逐条检查模式），所以需要我们手动进行一次配置切换。

开机后按下 F2 进入 BIOS 设置界面（我建议直接使用 iDRAC 的 vconsole 来选择启动到 BIOS）。

进入 `Memory Settings` -> `Persistent Memory` -> `Intel Persistent Memory` -> `Region Configuration` -> `Create Goal Config`，选择 Persistent 的比例，0 为内存模式（此时 PMem 作为系统主内存，DIMM作为 PMem 的缓存，[LLC](https://en.wikichip.org/wiki/last_level_cache) 有 192GB! :nerd: ），100 为 App Direct 模式。我选择了 100，毕竟 192 GB 的内存已经非常够用了。

当选择 App Direct 模式时，可以选择每个 socket 下的 PMem 是否启用 interleave，启用交错时每个 socket 下的所有 PMem会被视为同一个 Region，不启用时则每一条 PMem 单独作为一个 Region。而之后每个 Namespace 都必须从一个 Region 中划分出来，因此我为了方便选择了启用交错。但是请注意，此模式下 PMem 类似于 RAID0，任何一条 PMem 出问题都会导致整个 Region 的数据丢失。你可能会说“我为什么不用 btrfs 或者 ZFS 来组软件 RAID 呢？” —— 请看[这里](#存储配置)。

重新启动系统后，建议进行一次 Crypto erase，擦除 PMem 上先前的数据（在使用 3D XPoint 的其他 SSD 上例如 Optane Memory M10 等执行 Crypto erase 时需要用 0 全部复写一遍，但是真正的 Optane PMem 却不用 :D）。

### 存储配置

#### 创建 Namespace

首先查看 Region 数量:

```sh
ndctl list -R
```

分别在每个 Region 上创建 Namespace:

```sh
ndctl create-namespace --region=regionX --mode=fsdax --map=dev
```

由于目前有两个 Region，而 XFS 不支持多设备，我采用以下方案：

| 设备 | 类型 | 大小(lsblk) | 挂载点 | FS |
| ---- | ---- | ---- | ------- | - |
| /dev/sda1 | IDSDM | 29.7G | /efi | FAT32 |
| /dev/pmem0 | PMem | 744.2G | / | XFS |
| /dev/pmem1 | PMem | 744.2G | /home | XFS |

是不是成了“无盘”系统或者“把系统装进内存里”呢 :nerd: :point_up:

而接下来就是常规的安装 OS 的过程了，我使用 Arch Linux，安装教程可以参考 [我的另一篇文章](../../os/archlinux.md)

### 性能测试

### 附言

1. 安装 PMem 后，普通性能风扇的 PWM 下限为 50%。
