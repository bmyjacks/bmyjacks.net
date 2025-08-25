# Systemd-boot 折腾

由于 PMem 的 block device 需要在系统启动后一段时间才能被识别，而 GRUB 在启动时就需要加载配置文件，因此无法使用 GRUB 来引导系统（也有可能是我太菜了）。另外，作为 systemd 全家桶用户，systemd-boot 的配置和使用都比较简单直观，且与 systemd 的其他组件集成良好，能够更好地支持我的系统需求。

因此，使用 systemd-boot 来引导系统。

## 安装 systemd-boot 到 ESP 分区上

使用 `bootctl` 命令会自动识别 ESP 分区的位置，如 `/efi`，`/boot` 或 `/boot/efi` 等。如果 ESP 分区并不在常见目录，可以通过 `--esp-path=esp` 来指示相应的目录。

```bash
bootctl install
```

## 配置 systemd-boot

systemd-boot 支持新兴的 Unified kernel image(UKI) 格式进行引导。为了生成 UKI(.efi) 文件，采用 mkinitcpio 与 systemd-ukify 工具。

## 参考资料

1. [systemd-boot - ArchWiki](https://wiki.archlinux.org/title/Systemd-boot)
2. [Unified kernel image - ArchWiki](https://wiki.archlinux.org/title/Unified_kernel_image)
