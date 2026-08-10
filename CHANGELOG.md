# Changelog

## [Unreleased]

### Added — feature-complete generic x86-64 OS build

- **Full build orchestration**: `Makefile` (`image`/`build`/`rebuild-image`/
  `sdk`/`esdk`/`shell`/`swu`/`flash`/`deploy-*`/`clean*`/`completion`),
  `docker/{yocto,sdk,esdk}` build containers, `scripts/generate-swu-keys.sh`,
  `scripts/kas-container`, `polaris-make-completion.bash`.
- **`kas/`** — `common.yaml`, `boards/polaris.yaml` (RZ/G2L SMARC), and repos
  (`poky`, `meta-arm`, `meta-openembedded`, `meta-renesas`, `meta-swupdate`, `meta-polaris`).
- **Complete `meta/meta-polaris` Yocto layer** (68 files):
  - `recipes-core/images/polaris-image.bb` + `polaris-image-common.inc` + 20
    per-domain image variants
  - `recipes-core/polaris-data-init`, `polaris-services`, `systemd`, `os-release`
  - `recipes-core/packagegroup-polaris-middleware` / `-cloud`
  - `recipes-connectivity/polaris-connectivity`, `polaris-timesync`
  - `recipes-bsp/polaris-hal`, `recipes-support/expand-rootfs`, `swupdate`
  - `recipes-swupdate/polaris-swupdate-config` (A/B OTA runtime)
  - `wic/sdimage-x86.wks`, `swupdate/sw-description` + pre/post-install
- **`CLAUDE.md`**, **`REPO_MAP.md`**, **`CHANGELOG.md`**, `.gitignore`.

### Notes

- Polaris-OS is a hardware-neutral generic x86-64 template, following the shared
  Acadia/Arches template. Add a board config + vendor BSP to target a specific
  platform.

### Retarget: generic x86-64 → Renesas RZ

- **Polaris-OS is now the Renesas RZ build** (RZ/G2L, machine `smarc-rzg2l`).
- Added `meta-renesas` (`scarthgap/rz`) + `meta-arm` layers.
- Boot: Flash Writer → U-Boot SPL → U-Boot → Linux
  (Image + renesas/r9a07g044l2-smarc.dtb).
- A/B OTA: `sdimage-rzg2l.wks`, extlinux.conf + U-Boot env
  (upgrade_available/bootcount/bootlimit) rollback; kernel + DTB in `.swu`.
