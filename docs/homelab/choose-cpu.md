选择 CPU 是组建服务器时最重要的决策。CPU 决定了主板的选择，而主板又会对内存、PCIe 和存储选项有一定限制。CPU 还决定了服务器部分的功耗和发热量。

<!-- more -->

## 工作负载

根据你想在服务器上运行的任务类型，你需要选择不同的 CPU。以下是一些示例：

### 文件服务器（NAS）

文件服务器的 CPU 选择取决于并发用户数量和你希望同时服务的数据量。

对于只有少量用户的小型文件服务器，像 Intel Celeron 或 AMD Athlon 这样的低端 CPU 就足够了。在家庭环境中，我们通常不会同时传输大量数据，CPU 大部分时间都处于空闲状态。甚至树莓派也可以胜任小型文件服务器。

如果你有特殊需求，以下是一些常见场景：

#### ZFS

ZFS（以前称为 Zettabyte 文件系统）是一种带有卷管理功能的文件系统。[^1]

[^1]: [Wikipedia ZFS](https://en.wikipedia.org/wiki/ZFS)

现在，人们常说的 ZFS 实际上指的是 OpenZFS。OpenZFS 是原始 ZFS 项目的开源实现，支持 Linux、FreeBSD 及其他操作系统。

为 ZFS 文件服务器选择 CPU 稍微复杂一些。ZFS 本身对 CPU 要求不高，但它的一些特性会消耗较多 CPU。

强烈建议 ZFS 配备 ECC 内存，因为 ZFS 会用内存做读写缓存。并不是所有数据都会立即写入磁盘，因此高稳定性的内存非常重要。ECC 内存需要 CPU 和主板都支持。

???- info "查询 CPU ECC 支持"
    | 品牌 | 链接 | 相关栏目 |
    | --- | --- | --- |
    | Intel | [ark.intel.com](https://ark.intel.com/content/www/us/en/ark.html#@Processors) | Memory Specifications |
    | AMD | [amd.com](https://www.amd.com/en) | 找到你的 CPU，然后查看 Connectivity |

#### 高吞吐量网络

如今，10Gbit/s 甚至 100Gbit/s 的网卡已经很便宜。随着越来越多的数据中心淘汰高端网卡，它们进入了二手市场。

对于高吞吐量网卡，CPU 可能成为瓶颈。如果你的网卡无法将部分工作卸载给 CPU，你就需要更高性能的 CPU。

#### 大容量存储池

如果你需要存储大量数据，就需要一颗拥有更多 PCIe 通道的 CPU。通常主板自带的 SATA（SAS）接口数量有限，你需要 HBA（主机总线适配器）或 RAID 卡，这些卡都需要 PCIe 通道。

???- info "查询 CPU PCIe 通道数"
    | 品牌 | 链接 | 相关栏目 |
    | --- | --- | --- |
    | Intel | [ark.intel.com](https://ark.intel.com/content/www/us/en/ark.html#@Processors) | Expansion Options |
    | AMD | [amd.com](https://www.amd.com/en) | 找到你的 CPU，然后查看 Connectivity |

#### 加密

如果你想加密数据，需要一颗支持硬件加密的 CPU，这会大幅提升加密性能。（没人愿意等几个小时来加密数据，对吧？）

不用太担心，大多数 2010 年以后发布的 CPU 都支持硬件加密。

#### 去重

去重是消除重复数据的过程，常用于备份系统以节省存储空间。

如果你需要实时去重，需要一颗高性能 CPU。事后去重对 CPU 要求较低，但仍然需要大量内存。

### 媒体/流媒体服务器

媒体服务器是为其他设备提供多媒体文件的服务器。它可以是带播放器的简单文件服务器，也可以是带转码功能的高级服务器。

大多数流媒体服务对 CPU 要求不高，只需读取文件并发送给客户端。

如果你需要转码媒体文件，则需要 GPU。转码是将媒体文件从一种格式转换为另一种格式的过程，常用于将高质量文件转为低质量以便流式传输（节省带宽和存储空间）。

通常，带集成显卡的 CPU 就足够转码。如果需要同时转码多个文件，则需要独立 GPU。（无需购买高端显卡，低端和高端显卡的解码/编码能力差别不大。）

???- info "查询 GPU 编解码支持"
    | 品牌 | 链接 | 相关栏目 |
    | --- | --- | --- |
    | Intel QSV | [Wikipedia](https://en.wikipedia.org/wiki/Intel_Quick_Sync_Video) | Fixed-function Quick Sync Video format support |
    | Nvidia | [Wikipedia](https://en.wikipedia.org/wiki/Nvidia_NVDEC) | GPU support |
    | AMD | 通常很少用 AMD 做转码 | |

### 虚拟化服务器

虚拟化服务器用于运行多个虚拟机。每个虚拟机有自己的操作系统和应用，虚拟化服务器负责管理虚拟机的资源。通常建议选择多核心 CPU。

最重要的特性是 CPU 必须支持虚拟化，即 VT-x（Intel）或 AMD-V（AMD）。这个特性让 CPU 更高效地运行虚拟机。大多数现代 CPU 都支持。

如果你想将设备（尤其是 GPU）直通给虚拟机，需要 CPU 支持 IOMMU。IOMMU 允许 CPU 将设备分配给虚拟机，常用于游戏虚拟机。Intel 称为 VT-d，AMD 称为 AMD-Vi。

请确保在 BIOS 中启用这些特性，有些主板默认关闭。

### 其他服务

根据你的经验选择 CPU，或者向社区寻求帮助。

### All in one

!!! warning
    强烈**不推荐** All in one 服务器。一旦某个服务导致系统崩溃，整个服务器都会宕机。最好为不同服务准备多台服务器。至少，主路由器要与服务器分开（否则当你不小心搞坏服务器时，家人会很生气）。

    如果你仍然想要 All in one 服务器，只需将你想运行的所有服务的需求加在一起即可。
