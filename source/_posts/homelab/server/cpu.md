---
title: Homelab Server CPU
date: 2024-10-25
updated: 2024-10-27
tags:
    - Homelab
    - Server
    - CPU
categories:
  - Homelab
---

Choosing a CPU is the most important decision when building a server. The CPU will determine the motherboard. Then the motherboard will have some restrictions in memory, PCIe and storage options. The CPU will also determine parts of the power consumption and heat output of the server.

<!-- more -->

## Workloads

Depending on the tasktypes you want to run on the server, you will need a different CPU. Here are some examples:

### Fileserver (NAS)

The CPU choice for a fileserver varies depending on the number of concurrent users and the amount of data you want to serve at the same time.

A low-end CPU like an Intel Celeron or AMD Athlon is enough for a small fileserver with a few users. In home environments, we normally don't transfer large amounts of data at the same time. The CPU will be idle most of the time. Even a Raspberry Pi can be enough for a small fileserver.

As you may have special requirements, some common use cases are:

#### ZFS

ZFS (previously Zettabyte File System) is a file system with volume management capabilities.[^1]

[^1]: [Wikipedia ZFS](https://en.wikipedia.org/wiki/ZFS)

Today, people often say ZFS when they actually mean OpenZFS. OpenZFS is a open-source implementation of the original ZFS project. OpenZFS is available on Linux, FreeBSD, and other operating systems.

Choosing a CPU for a ZFS fileserver is a bit more complicated. ZFS itself is not CPU intensive. But some of its features can be.

ECC memory is highly recommended for ZFS, since ZFS does use memory for both write amd read caching. Not all data is written to the disk immediately, so a highly stable memory is important. ECC memory needs both a CPU and a motherboard that support it.

???- info "Query CPU ECC support"
    | Brand | Link | Section |
    | --- | --- | --- |
    | Intel | [ark.intel.com](https://ark.intel.com/content/www/us/en/ark.html#@Processors) | Memory Specifications |
    | AMD | [amd.com](https://www.amd.com/en) | Find your CPU, then Connectivity |

#### High throughput network

Today, 10Gbits/s or even 100Gbits/s network cards are quite cheap. As more advanced network cards are retired from datacenters, they become available on the second-hand market.

CPU can be a bottleneck for high throughput network cards. If your network cards cannot offload some of the work from CPU, you will need a more performance CPU.

#### Large pools

If you have a lot of data to store, you will need a CPU with a lot of PCIe lanes. Normally, the motherboard won't provide enough SATA (SAS) ports for all the drives. You will need a HBA (Host Bus Adapter) or a RAID card. These cards require PCIe lanes.

???- info "Query CPU PCIe lanes"
    | Brand | Link | Section |
    | --- | --- | --- |
    | Intel | [ark.intel.com](https://ark.intel.com/content/www/us/en/ark.html#@Processors) | Expansion Options |
    | AMD | [amd.com](https://www.amd.com/en) | Find your CPU, then Connectivity |

#### Encryption

If you want to encrypt your data, you will need a CPU with hardware encryption support. This will drastically increase the performance of the encryption. (You aren't willing to wait for hours to encrypt your data, right?)

Don't worry too much about it, most modern CPUs (2010 or later) have hardware encryption support.

#### Deduplication

Deduplication is a process that eliminates duplicate copies of data. It is often used in backup systems to save storage space.

If you need on-the-fly deduplication, you will need a CPU with a lot of performance. Post deduplication is less CPU intensive, but it will still require a lot of memory.

### Media/streaming server

A media server is a server that serves multimedia files to other devices. It can be a simple fileserver with a media player, or a more advanced server with transcoding capabilities.

Most streaming works are not CPU intensive. They just need to read the file and send it to the client.

If you need to transcoding the media, you will need a GPU. Transcoding is the process of converting media files from one format to another. It is often used to convert a high-quality file to a lower quality file for streaming. (That saves bandwidth and storage space.)

Normally, a CPU with integrated graphics is enough for transcoding. If you need to transcode multiple files at the same time, you will need dedicated GPU. (No need to buy a high-end GPU, decoders and encoders aren't much different between low-end and high-end GPUs.)

???- info "Query GPU de/encoding support"
    | Brand | Link | Section |
    | --- | --- | --- |
    | Intel QSV | [Wikipedia](https://en.wikipedia.org/wiki/Intel_Quick_Sync_Video) | Fixed-function Quick Sync Video format support |
    | Nvidia | [Wikipedia](https://en.wikipedia.org/wiki/Nvidia_NVDEC) | GPU support |
    | AMD | Normally people won't use AMD for transcoding | |

### Virtualization server

A virtualization server is a server that runs multiple virtual machines. Each virtual machine has its own operating system and applications. The virtualization server is responsible for managing the resources of the virtual machines. Normally, a CPU with a bunch of cores is recommended for a virtualization server.

The most important feature that your CPU needs to support is virtualization, or VT-x (Intel) or AMD-V (AMD). This feature allows the CPU to run virtual machines more efficiently. Again, most modern CPUs have this feature.

When you want to passthrough a devices (especially a GPU) to a virtual machine, you will need a CPU with IOMMU support. IOMMU is a feature that allows the CPU to assign a device to a virtual machine. This feature is often used for gaming virtual machines. For Intel, this feature is called VT-d. For AMD, this feature is called AMD-Vi.

Make sure you enable these features in the BIOS. Some motherboards disable these features by default.

### Other services

Choose a CPU according to your experience, or ask the community for help.

### All in one

!!! warning
    All in one server is highly **NOT** recommended. Once a service breaks the system, your whole server will be down. It is better to have multiple servers with different services. At least, separate your main router from the server (otherwise, when you accidentally break the server, some of your family members will be angry).

    If you still want to have an all in one server, just combine all the requirements from the different services you want to run.
