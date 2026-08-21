# Polaris-OS

## Project Summary

- Polaris-OS is a containerized Yocto/OpenEmbedded build repository for
  producing the Polaris embedded Linux image. KAS configuration under
  `kas/` defines shared settings, a Polaris board, and external layer
  repositories including Poky, meta-arm, meta-openembedded, meta-Renesas,
  meta-SWUpdate, and meta-Polaris. Docker makefiles under `docker/yocto`,
  `docker/sdk`, and `docker/esdk` provide isolated build environments for
  the image, SDK, and extensible SDK. The root Makefile is the primary
  interface for building and deploying these artifacts.

- The checked-in configuration and documentation describe an A/B update
  design using SWUpdate, board-specific image generation, SDK and eSDK
  packaging, shell access, flashing, and deployment helpers.
  `feature/feature.txt` and `feature/layout.txt` record the current
  feature/layout intent, while `scripts/generate-swu-keys.sh` supports
  creation of update-signing material. `REPO_MAP.md`, `CLAUDE.md`, and
  `CHANGELOG.md` provide repository guidance and history. This repository
  is principally build/configuration material: it does not contain a
  conventional application source tree or an automated test directory.

- Declared targets include `make image`, `build`, `rebuild-image`, `shell`,
  `sdk`, `esdk`, `swu`, `flash`, deployment targets, cleanup, and help.
  Because Yocto builds depend on Docker, network-fetched layers, host
  permissions, board configuration, and target media, the presence of KAS
  entries is not proof that a complete image or update has been produced.
  Build reproducibility, boot, flashing, A/B rollback, and signed update
  installation remain manual or environment-dependent checks.

## Feature Inventory

| # | Name / Description | Implemented? | Test Coverage? | Manual Test |
|---:|---|---|---|---|
| 1 | KAS shared configuration (`kas/common.yaml`) | Implemented | No automated test suite found | [ ] Manual verification required |
| 2 | Polaris board KAS configuration | Implemented | No automated test suite found | [ ] Manual verification required |
| 3 | External Yocto layer declarations | Implemented | Configuration only; fetch/build unverified | [ ] Manual verification required |
| 4 | Dockerized Yocto build environment | Implemented | Make targets declared; image unverified | [ ] Manual verification required |
| 5 | Target image build workflow | Implemented | `make image`/`make build` declared | [ ] Manual verification required |
| 6 | Host SDK build workflow | Implemented | `make sdk` declared; artifact unverified | [ ] Manual verification required |
| 7 | Extensible SDK workflow | Implemented | `make esdk` declared; artifact unverified | [ ] Manual verification required |
| 8 | SWUpdate A/B OTA package workflow | Partial | `make swu` declared; no tests found | [ ] Manual verification required |
| 9 | SWUpdate signing-key generation helper | Implemented | Script present; no automated test found | [ ] Manual verification required |
| 10 | Image flashing to block device | Implemented | `make flash` declared; no test found | [ ] Manual verification required |
| 11 | Image and SDK deployment helpers | Partial | Targets declared; environment unverified | [ ] Manual verification required |
| 12 | Build cleanup and shell/completion tooling | Implemented | No automated tests found | [ ] Manual verification required |

## Validation

```bash
make help
make image polaris
make sdk polaris
make swu
```

Use a disposable target and explicitly verify boot, flash media, signed updates, rollback, and recovery before deployment.
