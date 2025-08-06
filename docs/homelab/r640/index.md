# R640 概述

由于 Summer 25 的周期实在太长（5月底到8月底），而[主包](/#fn:1)在这酷热的夏天既不上暑课也不实习，而是呆在家里享清福，决定继续折腾 homelab。

在这个时间节点戴尔 14G 的价格已经降到了一个比较合适的水平，同时 LGA3647 的 CPU 相比上一代的 LGA2011 多出许多功能（主包看中的是AVX512），于是决定购入 R640 服务器。

## 配置

由于是二手/十八手/翻新的服务器，购入准系统后主包可以自行选择搭配的硬件，截止到目前为止，配置如下：

- **机箱/主板/硬盘背板**: [PowerEdge R640](https://www.dell.com/support/product-details/en-us/product/poweredge-r640/resources/search)
- **CPU**: 2x [Intel Xeon Gold 6230](https://www.intel.com/content/www/us/en/products/sku/192437/intel-xeon-gold-6230-processor-27-5m-cache-2-10-ghz/specifications.html)
- **内存**: 12x [Samsung M393A2G40DB1-CRC](https://semiconductor.samsung.com/dram/module/rdimm/m393a2g40db1-crc/)，也就是 16GB 2400Mbps 的 DDR4 RDIMM。
- **持久内存**: 12x [Intel Optane PMem NMA1XXD128GPS](https://www.intel.com/content/www/us/en/products/sku/190348/intel-optane-persistent-memory-100-series-128gb-module/specifications.html)
- **硬盘**: 2x [Intel S3500](https://www.intel.com/content/dam/www/public/us/en/documents/product-specifications/ssd-dc-s3500-spec.pdf) 240GB，2x [Intel S4500](https://tiscom.ru/sites/default/files/additional/ssd-dc-s4500-s4600-brief.pdf) 240GB，4x [Samsung PM853T](https://ftp.hydata.com/Downloads/scsitools/scsitools.Lite/Resources/LOD_FactoryMadeLOD-Files/Samsung/MZ7GE/PM853T-SATA-Product-Manual.pdf) 240GB。
- **RAID卡**: [PERC H740p Mini](https://objects.icecat.biz/objects/mmo_38074103_1553678750_5018_8435.pdf)
- **NDC/NIC**: [Intel i350-T4](https://www.intel.com/content/www/us/en/products/sku/184824/intel-ethernet-network-adapter-i350t4-for-ocp-3-0/specifications.html)，很经典的千兆网卡，4x 1GbE RJ45 接口。
- **PSU**: Delta 750W + Artesyn 750W。
- **IDSDM**: 2x [SanDisk MAX ENDURANCE microSD™ Card - 32GB](https://shop.sandisk.com/products/memory-cards/microsd-cards/sandisk-max-endurance-uhs-i-microsd?sku=SDSQQVR-032G-GN6IA)

## 折腾经历

请使用左侧导航栏查看主包踩坑的心酸历史。
