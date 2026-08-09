# Polaris-OS — Yocto embedded Linux (generic x86-64)

## Overview

Polaris-OS is the Yocto/OpenEmbedded build system for the **Polaris** platform.
This repository is a **hardware-neutral** build template targeting
`genericx86-64` (EFI/GRUB boot) by default, so it builds out of the box with
no vendor BSP layer. Board, processor, carrier, boot firmware, storage,
kernel, and vendor BSP details are meant to be supplied by a target
configuration (see `kas/boards/*.yaml`). Built with
[kas](https://kas.readthedocs.io/) + Docker, with A/B OTA updates via
[swupdate](https://sbabic.github.io/swupdate/).

## Quick Start

```bash
make kas-image            # Build the KAS Docker image (once)
make image polaris           # Build the Yocto image (genericx86-64)
```

## Build Targets

```
make image  polaris           Build Yocto SD card image
make build  polaris           Alias for image
make rebuild-image polaris    cleansstate image recipe, then rebuild
make sdk    polaris           Cross-toolchain SDK installer → deploy/sdk/
make esdk   polaris           Extensible SDK installer     → deploy/esdk/
make shell  polaris           Interactive kas shell
make swu                    Build .swu OTA package
make flash  polaris DEVICE=/dev/sdX   Flash image to SD card
```

## A/B OTA Layout

Partition table (`meta/meta-polaris/wic/sdimage-x86.wks`, EFI GPT):
```
  p1  /boot   EFI FAT32   Image, grub.cfg
  p2  /       rootfs-a (active initially)
  p3  standby rootfs-b (OTA target)
  p4  /home   persistent, grown on first boot by polaris-data-init
```

SWUpdate scripts switch slots by filesystem label (`rootfs-a`/`rootfs-b`).
`make swu` builds an `.swu` package; signing via
`scripts/generate-swu-keys.sh` (RSA-4096).

## Layer Structure

| Layer               | Source                        | Purpose                              |
|---------------------|-------------------------------|--------------------------------------|
| `poky`              | poky (scarthgap)              | OE core + genericx86-64 machine     |
| `meta-oe`           | meta-openembedded (scarthgap) | Package set                          |
| `meta-swupdate`     | sbabic/meta-swupdate (scarthgap) | swupdate OTA framework           |
| `meta-polaris`         | SiliconCentric/meta-polaris (local, in `meta/`) | Polaris image, HAL, swu config |

## Repo Notes

- `build/`, `cache/`, `sstate-cache/`, `layers/` are gitignored bind-mounted
  host dirs.
- `meta/meta-polaris` is committed **locally** in this repo (see `.gitignore`);
  upstream layers are cloned by kas into `layers/` and must never be
  hand-edited.
- Hardware-neutral: add a board config (`kas/boards/<board>.yaml`) and a
  vendor BSP layer to target a specific platform.
