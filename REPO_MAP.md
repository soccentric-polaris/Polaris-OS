# Polaris-OS

**Remote:** `git@github.com:SiliconCentric/Polaris-OS.git`
**Parent project:** [Polaris](../Polaris.md)
**Leaf type:** `<Project>-OS`
**Status:** REAL — feature-complete generic x86-64 build template

Hardware-neutral Yocto/OpenEmbedded build system targeting `genericx86-64`
(EFI/GRUB boot) by default. Board/SoC/vendor-BSP specifics are supplied by a
target configuration in `kas/boards/*.yaml`; the repo builds out of the box
with no vendor BSP layer. A/B OTA via `swupdate`.

**Board:** default `polaris` (machine `genericx86-64`). Platform customization
lives in `meta/meta-polaris` (committed locally); upstream layers (`poky`,
`meta-openembedded`, `meta-swupdate`) are checked out by kas into a
**gitignored** `layers/` directory and must never be hand-edited.

**Targets:** `make build`, `make image`, `make shell [board]`, `make swu`,
`make sdk`/`esdk`, `make flash`, `make deploy-*`, `make clean`/`clean-all`.

**A/B OTA layout:** EFI GPT — `boot` (FAT32), `rootfs-a`, `rootfs-b`
(switched by swupdate via filesystem label), `data` (`/home`, persistent).
See `meta/meta-polaris/wic/sdimage-x86.wks`.

**Local layer:** `meta/meta-polaris` is committed in-repo (see `.gitignore`),
matching the Acadia/Arches/Zion/Joshua/Pinnacle/Sequoia convention.

---
Part of the `Polaris` project in the `project-managers` fleet. See
`docs/project-managers.md` for the full 9-project / 8-leaf hierarchy and shared
conventions.
