# Polaris-OS

**Remote:** `git@github.com:soccentric-polaris/Polaris-OS.git`
**Parent project:** [Polaris](../Polaris.md) (target hardware: **Renesas RZ/G2L** 64-bit Arm MPU, R9A07G044L.)
**Leaf type:** `<Project>-OS`
**Status:** REAL — actively built out

Yocto-based embedded Linux OS for the **Renesas RZ/G2L** platform. Built with
`kas` + Docker against the `meta-renesas` (`scarthgap/rz`) layer set, with A/B
OTA via `swupdate`.

**Board:** default `polaris` (machine `smarc-rzg2l`, RZ/G2L SMARC Evaluation
Kit). Platform customization lives in `meta/meta-polaris`, a nested clone of its
own upstream `soccentric-polaris/meta-polaris` (fetched by kas);
upstream layers (`poky`, `meta-arm`, `meta-openembedded`, `meta-renesas`,
`meta-swupdate`) are checked out by kas into a **gitignored** `layers/`
directory and must never be hand-edited.

**Targets:** `make build`, `make image`, `make shell [board]`, `make swu`,
`make sdk`/`esdk`, `make flash`, `make deploy-*`, `make clean`/`clean-all`.

**Boot flow (RZ/G2L SMARC):**
```
Flash Writer → U-Boot SPL → U-Boot → Linux (Image + renesas/r9a07g044l2-smarc.dtb)
             → systemd → Polaris-HAL, Polaris services
```

**A/B OTA layout:** `boot` (FAT32), `rootfs-a`, `rootfs-b` (ext4, switched by
swupdate via filesystem label + U-Boot env), `data` (`/home`, persistent).
See `meta/meta-polaris/wic/sdimage-rzg2l.wks`.

**Local layer:** `meta/meta-polaris` is a nested clone with its own upstream
at `soccentric-polaris/meta-polaris.git` (see `.gitignore`).

---
Part of the `Polaris` project in the `project-managers` fleet. See
`docs/project-managers.md` for the full 9-project / 8-leaf hierarchy and shared
conventions.
