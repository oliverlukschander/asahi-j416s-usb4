# USB4 hub bring-up on apple,j416s

Notes and patches from getting an **OWC Thunderbolt 5 Hub** talking USB on a
**MacBook Pro 16-inch, M2 Pro, 2023** (`Mac14,10` / `apple,j416s`) running
Omarchy with the Aurora kernel (`linux-aurora` 7.1.12, `aurora-silicon/linux`).

A Keychron Q4 on the hub USB-A port enumerates and types. That is USB 3
tunneled over USB4. **DisplayPort and PCIe tunnels are not implemented** in
this stack; a monitor on the hub Thunderbolt ports stays dark.

## What to send where

Do **not** upstream boot.bin, GRUB snippets, or out-of-tree `.ko` files. The
reviewable work is two kernel patches:

| Patch | Tree | Why |
| --- | --- | --- |
| `patches/0001-usb-typec-tipd-prefer-USB4-TBT-over-DP-on-CD321x.patch` | [Aurora-Silicon/linux](https://github.com/Aurora-Silicon/linux) (`aurora-wip`), then Asahi if they carry the same CD321x USB4 switch | CD321x was taking DP first and turning the thunderbolt switch **off** on USB4 docks |
| `patches/0002-arm64-dts-apple-t600x-add-m1n1-1.6.1-USB4-aliases.patch` | same kernel trees | m1n1 **v1.6.1** copies ACIO tunables via `usb4_%d_rc`; the DT only had `usb4-%d-acio` |

Suggested order:

1. Open a PR against **Aurora-Silicon/linux** `aurora-wip` with both patches (that is the kernel this machine actually ran).
   Opened: https://github.com/aurora-silicon/linux/pull/6
2. Offer the same tipd change to **AsahiLinux/linux** / Sven if that `cd321x_typec_update_mode()` block is in their tree.
3. **m1n1**: current `main` already falls back to hyphen aliases. Bumping asahi-alarm `m1n1` past 1.6.1 is an alternative to patch 0002, not a replacement if 1.6.1 stays shipped.
4. **Omarchy MX Mac** (`maralcbr/omarchy-mx-mac`): separate later. That is a catalog allowlist for `apple,j416s` on the Aurora RC image, plus hardware evidence. Not these patches.

## Local workaround (until the PRs land)

This repo is not a kernel fork. The scripts under `workaround/` rebuild
`tps6598x` out of tree against `linux-aurora` headers and document the m1n1
alias DTB step. They are for this machine’s current packages, not for review.

## Hardware

- Host: MacBook Pro 16-inch M2 Pro 2023, `apple,j416s`, `t6020`
- Hub: OWC Thunderbolt 5 Hub (`1e91:de81`)
- Keyboard: Keychron Q4 on hub USB-A (`3434:0140`)
- Kernel: `7.1.12-2.5-1-ARCH` (`linux-aurora` aurora-edge-5, `2439016d`)
- m1n1: 1.6.1-1 (asahi-alarm)
