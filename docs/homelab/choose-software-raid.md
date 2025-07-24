# 选择软件 RAID 方案

!!! note
    如果您考虑使用硬件 RAID 卡，请查看 [选择硬 RAID 卡](choose-hardware-raid.md) 页面。

软件 RAID 是一种使用操作系统或软件来管理磁盘阵列的方式。它通常比硬件 RAID 更灵活且成本更低，但可能需要更多的配置与维护知识。

常见的软件 RAID 解决方案（其实是我尝试过的方案 :nerd: :point_up:）包括：

| 名称 | 描述 | 适用 OS | 场景 |
| ---- | ---- | ------- | ---- |
| mdadm | Linux 下的标准软件 RAID 管理工具 | Linux | 需要灵活性和自定义的 RAID 解决方案 |
| [ZFS](https://openzfs.org/wiki/Main_Page) | 文件系统，支持更强大的 RAID 功能（RAID-Z/dRAID-Z），还拥有数据完整性校验，快照，去重等高级特性 | Linux(OpenZFS), FreeBSD | 需要高级数据保护和管理功能 |
| [Btrfs](https://btrfs.readthedocs.io/en/latest/) | 文件系统，支持内置 RAID 功能和卷管理，适合需要灵活性和易用性的场景 | Linux | 需要易用性和灵活性的 RAID 解决方案 |
| LVM | 逻辑卷管理器，虽然主要用于卷管理，但也可以实现部分 RAID 功能 | Linux | 需要卷管理和简单 RAID 功能 |
| Windows Storage Spaces | Windows 系统下的存储池和虚拟磁盘管理工具，支持多种 RAID 级别 | Windows | 需要在 Windows 环境下实现 RAID 功能 |
