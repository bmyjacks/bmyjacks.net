# 安装 NeoForge 服务器端

[NeoForge](https://neoforged.net/) 是一个新兴的 modding 平台，定位类似于高版本的 Forge。相比 Fabric 提供更多的功能，因此许多 mod 都提供了此平台的版本。

本文以 Minecraft 1.21.1 为例，介绍如何安装 NeoForge 服务器端。

## 下载 NeoForge

前往 [NeoForge 官网](https://neoforged.net/) 的下载页面，选择对应的 Minecraft 和 NeoForge 版本，下载最新的 NeoForge 服务器端安装包。

![alt text](<Screenshot 2025-08-01 at 21.31.32.png>)

下载后得到 `neoforge-xx.x.yyy-installer.jar` 文件，其中 `xx.x` 是 Minecraft 版本，`xx.x.yyy` 是 NeoForge 版本。

??? "如果下载到本地计算机而不是服务器"
    使用 `scp` 命令将安装包从本地计算机上传到服务器（如果是 Windows 服务器应该直接下载到本地，应该没人用纯 CLI 的 Windows 吧）：

    ```bash
    scp neoforge-xx.x.yyy-installer.jar user@your-server-ip:/path/to/your/folder
    ```

## 安装 NeoForge

在服务器上，使用以下命令运行 NeoForge 安装程序，这会将服务端安装到当前文件夹下，建议单独使用一个文件夹：

```bash
java -jar neoforge-xx.x.yyy-installer.jar --installServer
```
