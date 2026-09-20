# Out-of-tree workaround

Not for review. Use only on `linux-aurora` 7.1.12.aurora2.5 + m1n1 1.6.1 until
the patches in `../patches/` are in the running kernel and DT.

1. Pack Aurora `t6020-j416s.dtb` (with `usb4_0_rc` aliases) into
   `/boot/m1n1/boot.bin`. Keep `/boot/m1n1/boot.bin.asahi-good`.
2. Build `tps6598x-core.ko` / `tps6598x.ko` from Aurora `tipd/` with patch 0001
   applied, against matching `linux-aurora-headers`.
3. `insmod` those modules (or install under `/lib/modules/$(uname -r)/updates`
   and `depmod`).
4. Replug the hub.

Do not replace `linux-asahi` via pacman: `linux-aurora` Conflicts with it.
