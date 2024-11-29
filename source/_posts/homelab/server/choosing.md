---
title: Homelab Server Choosing
date: 2024-10-25
updated: 2024-10-25
tags:
    - Homelab
    - Server
categories:
    - Homelab
---

Servers are the backbone of any homelab. They provide the computing power needed to run various applications, virtual machines, and services.

<!-- more -->

## Hardware choosing strategy

The choice of hardware depends on the use case. After considering the use case, you can decide the hardware requirements based on the following factors:

### 1. Performance

The server should have enough power to run the applications and services you want. You can ignore future requirements as you can always upgrade the server later and people tends to overestimate their future needs.

For example, if you want to run a simple file server, a Raspberry Pi will be (more than) enough. But if you want to run many virtual machines on the server, you will need one with more CPU cores and RAM.

### 2. Power consumption

Servers run 24/7, so they should consume less power. A 100W server will cost you around $130 per year if you run it all the time. So choose the right hardware to save money on electricity bills.

Thus, when your goal is to build a simple file server, a Raspberry Pi will be preferable over a mini computer with Intel N100. In general, ARM-based servers consume less power than x86-based servers  (May change in the future).

### 3. Noise

Servers are noisy. Most servers are not water-cooled and have multiple high-power fans to keep the hardware cool. Please take this seriously since they are **really** noisy.

When you find the noise annoying, you can replace the fans with quieter ones. But be careful, as the server may overheat if you use fans which can not provide enough cooling. Or just enlarge the heat sink (be careful with the warranty).

### 4. Size

Servers are big. They are not designed to be kept on a desk. They are designed to be kept in a server rack. So make sure you have enough space or even a room for the server.

For reused computers, they are usually smaller and can be kept on a desk. But they are not as powerful as servers.

Server racks are the rigs that you normally wouldn't change quite often. So make sure you have enough space for your servers. I recommend you to measure the dimensions of your free space and your desired servers before buying a server rack.

### 5.Warranty

If you are buying a new server, make sure it has a warranty. Servers are expensive and you don't want to spend more money on repairs. However, as a homelabber, you can always buy used servers and repair them yourself, which is what you guys are good at.

Don't treat the enterprise-grade hardware as consumer-grade hardware. They are costly especially when they are included with support.

### 6.Upgradeability

Make sure the server is upgradeable. I promise that you will upgrade the server very often.

Therefore, when you are considering a server, unless you are sure that you will not upgrade the server, make sure the server has enough PCIe slots, RAM slots, and CPU sockets. Also, make sure the chassis has enough space for the upgrades.

### 7.Budget

New servers are expensive. They are not designed for home use. So make sure you have enough budget to buy the server and the required accessories. But what I recommend is to buy used servers. They are cheap and you can get a lot of performance for the price. Or just use your old computer that still packs a punch.

Many of the used servers are just retired from data centers. They can even look like the new ones. They usually sell with a chassis, motherboard, PSU. So you need to buy the CPU, RAM, and storage separately. That actually gives you more flexibility to choose the hardware you want.

!!! note ""

    All in all, do some research before buying a server. Make sure you know what you want and what you need. And make sure you have enough budget to buy the server and the required accessories.
