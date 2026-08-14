# Polaris-OS — Yocto embedded Linux for Renesas RZ

## Overview

Polaris-OS is the Yocto/OpenEmbedded build system for the **Polaris** platform —
the **Renesas RZ/G2L** 64-bit Arm MPU (R9A07G044L). Default board:
**RZ/G2L SMARC Evaluation Kit** (machine `smarc-rzg2l`). Built with
[kas](https://kas.readthedocs.io/) + Docker against the `meta-renesas`
(`scarthgap/rz`) layer set, with A/B OTA updates via
[swupdate](https://sbabic.github.io/swupdate/).

## Quick Start

```bash
make kas-image            # Build the KAS Docker image (once)
make image polaris        # Build the Yocto image (RZ/G2L SMARC)
```

## Build Targets

```
make image  polaris        Build Yocto SD card image
make build  polaris        Alias for image
make rebuild-image polaris cleansstate image recipe, then rebuild
make sdk    polaris        Cross-toolchain SDK installer → deploy/sdk/
make esdk   polaris        Extensible SDK installer     → deploy/esdk/
make shell  polaris        Interactive kas shell
make swu                    Build .swu OTA package
make flash  polaris DEVICE=/dev/sdX   Flash image to SD card
```

## Boot Flow (RZ/G2L SMARC)

```
Flash Writer → U-Boot SPL → U-Boot → Linux (Image + renesas/r9a07g044l2-smarc.dtb)
             → systemd → Polaris-HAL, Polaris services
```

## A/B OTA Layout

Partition table (`meta/meta-polaris/wic/sdimage-rzg2l.wks`):
```
  p1  /boot   FAT32   Image, DTB, extlinux
  p2  /       rootfs-a (active initially)
  p3  standby rootfs-b (OTA target)
  p4  /home   persistent, grown on first boot by polaris-data-init
```

SWUpdate scripts switch slots by filesystem label and update extlinux.conf +
U-Boot env (upgrade_available/bootcount/bootlimit) for firmware-level
rollback. `make swu` builds an `.swu` package; signing via
`scripts/generate-swu-keys.sh` (RSA-4096).

## Layer Structure

| Layer               | Source                        | Purpose                              |
|---------------------|-------------------------------|--------------------------------------|
| `poky`              | poky (scarthgap)              | OE core                              |
| `meta-arm`          | meta-arm (scarthgap)          | ARM BSP / ATF                        |
| `meta-oe`           | meta-openembedded (scarthgap) | Package set                          |
| `meta-renesas`      | renesas-rz/meta-renesas (`scarthgap/rz`) | Renesas RZ BSP        |
| `meta-swupdate`     | sbabic/meta-swupdate (scarthgap) | swupdate OTA framework           |
| `meta-polaris`      | soccentric-polaris/meta-polaris (local, in `meta/`) | Polaris image, HAL, swu config |

## Repo Notes

- `build/`, `cache/`, `sstate-cache/`, `layers/` are gitignored bind-mounted
  host dirs.
- `meta/meta-polaris` is committed **locally** in this repo (see
  `.gitignore`); upstream layers are cloned by kas into `layers/` and must
  never be hand-edited.
- RZ/G2L proprietary multimedia/graphics drivers require `meta-rz-features`
  (available from Renesas Customer Service); the base BSP builds without them.
