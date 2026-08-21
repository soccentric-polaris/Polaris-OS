# Polaris-OS Build and Deployment

## Purpose and scope

Polaris-OS is a Dockerized Yocto/OpenEmbedded build for the Renesas RZ/G2L Polaris board. KAS configuration is under `kas/`; Docker build contexts are under `docker/yocto`, `docker/sdk`, and `docker/esdk`. The root Makefile builds images, SDKs/eSDKs, SWUpdate packages, and flash media.

## 1. Prerequisites

- Linux, GNU Make, Docker, curl, `bmaptool`, `openssl`, `cpio`, `gzip`, and a block device for flashing.
- Network access for the pinned kas-container 4.5 script and Yocto layers on first build; enough disk space for `build/`, `cache/downloads`, and `cache/sstate-cache`.
- Board configuration `kas/boards/polaris.yaml` for the default `BOARD=polaris` / RZ/G2L SMARC machine.
- A private SWUpdate key only when signed OTA output is required. The default key path is `meta/meta-polaris/swupdate/keys/swupdate-private.pem`.

## 2. Setup

```bash
make kas-container-script
make kas-image
make help
```

The Makefile caches `kas-container` and uses `polaris-kas:latest` unless `KAS_IMAGE` is overridden. Select boards positionally, for example `make image polaris`.

## 3. Build and compile

```bash
make image polaris          # alias for make build
make build polaris          # Yocto image and deploy/images staging
make rebuild-image polaris  # cleansstate image recipe, then rebuilds
make shell polaris          # interactive KAS shell
```

Build output is produced by KAS/BitBake in `build/tmp/deploy/images/$(MACHINE)`. The build is network, Docker, disk, and board-configuration dependent.

## 4. Test and lint

The Makefile declares no automated test or lint targets. Use `make help`, inspect KAS/BitBake output, and perform manual image boot, peripheral, update, and rollback checks on a disposable board.

## 5. Packaging and artifacts

```bash
make sdk polaris            # SDK installer and deploy/sdk staging
make esdk polaris           # extensible SDK and deploy/esdk staging
make sdk-image polaris      # SDK installer plus Docker image
make esdk-image polaris     # eSDK installer plus Docker image
make swu polaris            # OTA .swu; override SWU_VERSION=1.2.3 if desired
```

`deploy/images/`, `deploy/sdk/`, `deploy/esdk/`, and `deploy/swu/` receive timestamped directories. `make swu` packages rootfs, optional `Image`/DTB, pre/post install scripts, and optionally an RSA signature. Missing keys produce an explicitly unsigned package.

## 6. Deployment, install, and run

```bash
make flash polaris DEVICE=/dev/sdX
```

`flash` requires a real block device, locates the latest `.wic.bz2`, and runs `bmaptool`; verify `DEVICE` carefully because this can overwrite removable media. `deploy-images`, `deploy-sdk`, `deploy-esdk`, and `deploy-swu` copy artifacts into local deploy directories; they are staging operations, not remote/cloud deployment. Installing images, SDKs, or SWUpdate packages on hardware is a manual, board-specific operation.

## 7. Cleanup

```bash
make clean                  # remove build/
make clean-docker           # remove KAS, SDK, and eSDK images
make clean-all              # build plus downloads and sstate cache
```

`clean-all` removes large shared caches and forces future downloads/rebuilds.

## 8. Verification caveats

The board name, machine, KAS layers, media, signing keys, and target boot chain are environment-dependent. Verify image boot, SD/eMMC flashing, A/B SWUpdate installation, signature policy, rollback/recovery, and peripheral behavior on physical RZ/G2L hardware. No cloud deployment or credential management is defined.