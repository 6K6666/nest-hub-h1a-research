# Google Nest Hub H1A local-control research

The retail Google Nest Hub H1A still boots Fuchsia in our tests. The target is a local Home Assistant dashboard on its display, followed by a wake phrase such as “Hey Brutus” routed to Home Assistant or a local AI service.

No retail H1A in this project has booted a custom OS. We've confirmed one read-only Cast status exchange & checked Google's Astro U-Boot source. A demo-unit owner reports postmarketOS, but hasn't published the files or transcript needed to reproduce it.

Research snapshot: 2026-10-22. At the last read-only check, the retail Hub was in normal Fuchsia mode. That check sent no boot, unlock, flash, staging, slot-change, recovery, playback, or volume command.

## Current evidence

| Status | Finding | Limit |
| --- | --- | --- |
| Observed on retail hardware | A normal boot exposes Fuchsia over USB CDC networking. A read-only Cast `GET_STATUS` request succeeded. | This proves a status exchange. It doesn't prove local app control, a custom dashboard, or voice control. |
| Observed in earlier Fastboot reads | The unit reported board `astro-b4`, production build, and `vx-locked=yes`. It also printed `vx-unlockable=ephemeral`. | That value didn't unlock the unit or establish an unlock path. |
| Observed in earlier BootROM reads | Amlogic IDENTIFY reported that a password was required. Protected reads returned `BAD PASSWORD`. | No password was recovered or bypassed. |
| Source-backed | The public Astro U-Boot tag registers `zbi_boot` in Estelle's default boot environment. `main.c` calls `cli_loop` only under `CONFIG_CLI_ENABLED`; no inspected build file sets it, and board config sets `CONFIG_BOOTDELAY=-2`. | The source supports production inclusion, but we haven't acquired or hashed the installed retail binary. External build flags remain unknown. |
| Source-backed | Production Fastboot uses a fixed callback table, not a generic shell. Its normal boot path passes the preloaded image through `zircon_vboot_preloaded_img_load` before booting. | This is source evidence. No modified image was tested on retail hardware. |
| Not found | The project has no authentic retail H1A OTA, update manifest, ZBI, vbmeta, or product boot-argument dump. | The product-bundle search covers specific public snapshots. It doesn't rule out private or archived images. |
| Not found in one public snapshot | The Fuchsia product-bundle catalog for `33.20260913.4.1` lists nine bundles, with no `astro` or `estelle` entry. | This result covers that snapshot. It doesn't rule out private or archived images. |
| Reported, unverified | A Reddit user says a demo H1A booted postmarketOS from internal storage with its touchscreen & Wi-Fi working. | The public thread has no transcript, boot files, hashes, or repeatable procedure. Another commenter reported no retail success. |

## The retail boot barrier

The retail bootloader is marked locked, & the normal Fastboot path validates its preloaded image. A 2021 H1A owner also reported that a custom recovery image failed the signed-descriptor hash check. No retail payload or successful unsigned boot has been demonstrated.

The `zbi_boot` handler is a lead, not a verified entry point. The source doesn't prove the installed binary contains it, a console can reach it, or it accepts an image without the verification used by normal Fastboot. Those are separate questions.

Google's firmware page lists versions & release notes. It doesn't provide a retail H1A image URL or package manifest. The checked Fuchsia product-bundle snapshot `33.20260913.4.1` lists nine bundles and contains no `astro` or `estelle` entry. An authentic retail update would let contributors inspect the signed system offline; it wouldn't make a modified image trusted by the Hub.

## The demo-unit report

The [Reddit post](https://www.reddit.com/r/homelab/comments/1wgepn7/i_turned_a_google_home_hub_h1a_demo_unit_into_a/) says its author used no hardware modifications and got postmarketOS, the touchscreen, and Wi-Fi working from internal storage. The post summary calls the entry mode “Amlogic recovery.” A later comment describes holding both volume buttons, entering Fastboot, running commands over USB, and bootstrapping code in RAM. The author says the display-script explanation is a guess.

Those accounts don't describe one reproducible boot chain. Other commenters asked for the Codex transcript, loader files, kernel, DTB, root filesystem, commands & hashes. A commenter who tried a retail H1A reported no success. Nothing in the thread establishes whether the demo unit used a different boot mode, provisioning, password state, or signed loader.

The thread establishes that a claim was made. It doesn't establish a working retail procedure.

## Earlier H1A attempts and the H2 distinction

In a [2021 LibreELEC thread](https://forum.libreelec.tv/thread/24581-hacking-the-google-nest-hub/?postID=161040) & a related [Reddit thread](https://www.reddit.com/r/googlehome/comments/puru6r/hacking_the_google_nest_hub_firmware/), owners described failed Fastboot unlocking, signature rejection of custom recovery, password-protected Amlogic USB access, and UART output without an interactive prompt. Those are user reports, not an audit of every boot path.

The public [`chipicopwn` project](https://github.com/frederic/chipicopwn) targets the second-generation Nest Hub, code-named Elaine. It isn't an H1A exploit and doesn't solve this boot chain.

## Open research tasks

1. Obtain the demo-unit author's full Codex transcript, exact host commands & tool versions, loader files and hashes, kernel/DTB/rootfs, and a statement about any eMMC writes.
2. Find an authentic retail H1A OTA or capture its genuine update metadata. A firmware version string isn't an image.
3. Compare an acquired retail bootloader binary with the public Astro U-Boot source. Check command inclusion, console reachability, and image verification separately.
4. Identify the retail OS consumer for `ZBI_BOOTLOADER_FILE`. Source exposes a Fastboot staging handler; this project hasn't found the H1A startup component that reads the staged file.
5. After an alternate boot is proven, test display, touch, Wi-Fi, microphone input, and wake-word handling as separate hardware tasks.

Keep retail tests read-only until a proposed step has a known effect, rollback, and independent review. Don't unlock, flash, stage files, alter boot slots, enter recovery, or issue undocumented OEM commands as part of this research.

## Contributing

Contributions should separate direct observation, upstream-source evidence, third-party reports, hypotheses, and unknowns. Include the device class (demo or retail), build, capture date, host OS, tool version, exact read-only command, and sanitized output. Remove serial numbers, MAC addresses, tokens, account identifiers, and private network details before opening an issue or pull request.

Don't upload proprietary firmware, private transcripts, or another person's files without permission. Link upstream sources & record hashes for artifacts that can be shared legally. See [CONTRIBUTING.md](CONTRIBUTING.md) for the evidence format.

## Sources

- [Astro U-Boot source tag for build 31.20260429.103.8712900](https://third-party-mirror.googlesource.com/u-boot/+/refs/tags/astro/31.20260429.103.8712900)
- [Estelle board configuration](https://third-party-mirror.googlesource.com/u-boot/+/refs/tags/astro/31.20260429.103.8712900/board/amlogic/configs/g12a_estelle_bx.h)
- [U-Boot main loop](https://third-party-mirror.googlesource.com/u-boot/+/refs/tags/astro/31.20260429.103.8712900/common/main.c)
- [U-Boot autoboot logic](https://third-party-mirror.googlesource.com/u-boot/+/refs/tags/astro/31.20260429.103.8712900/common/autoboot.c)
- [Astro Zircon boot command](https://third-party-mirror.googlesource.com/u-boot/+/refs/tags/astro/31.20260429.103.8712900/common/cmd_zircon.c)
- [Astro verified-boot path](https://third-party-mirror.googlesource.com/u-boot/+/refs/tags/astro/31.20260429.103.8712900/common/zircon-vboot.c)
- [Astro Fastboot command table](https://third-party-mirror.googlesource.com/u-boot/+/refs/tags/astro/31.20260429.103.8712900/drivers/usb/gadget/f_fastboot.c)
- [Astro U-Boot production and test build selection](https://third-party-mirror.googlesource.com/u-boot/+/refs/tags/astro/31.20260429.103.8712900/Makefile)
- [Fuchsia OTA guide](https://fuchsia.googlesource.com/fuchsia/+/89111e6e54b1051fc7fb01f45828761dd9d87b83/docs/concepts/packages/ota.md)
- [Fuchsia update-package contents](https://fuchsia.googlesource.com/fuchsia/+/89111e6e54b1051fc7fb01f45828761dd9d87b83/docs/concepts/packages/update_pkg.md)
- [Fuchsia product-bundle download documentation](https://fuchsia.googlesource.com/fuchsia.git/+/HEAD/docs/development/tools/ffx/workflows/start-the-fuchsia-emulator.md)
- [Fuchsia product-bundle catalog snapshot 33.20260913.4.1](https://chrome-infra-packages.appspot.com/p/fuchsia/development/product_bundles/v2/%2B/7NxIm079jlSH_IjIKpgqEQvuxjLHVqc9dIypMlmTADQC)
- [Google Nest firmware versions and release notes](https://support.google.com/googlehome/answer/7365257?hl=en)

