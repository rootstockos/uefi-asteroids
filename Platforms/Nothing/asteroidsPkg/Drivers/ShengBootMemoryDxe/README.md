# ShengBootMemoryDxe (from UniXNU/uefi-sheng)

Copied from `Platforms/Xiaomi/shengPkg/Drivers/ShengBootMemoryDxe` in UniXNU/uefi-sheng
(BSD-2-Clause-Patent). It reads the device tree the bootloader passes in x0 (kept by uefi-sheng's SEC in the
`gSiliciumEntryArgHobGuid` HOB), adds DRAM the firmware's own map doesn't cover, and publishes the
`/chosen linux,initrd-start/-end` range as a read-only block device. The UniXNU payload in the boot image's ramdisk
therefore shows up as a FAT volume. It reads every range from the device tree and has no board-specific addresses.

## Change for asteroids

The 3a's bootloader needs a header v4 boot image (it doesn't start one with header v1). With v3/v4 the initrd
is the vendor_boot ramdisk (6948396 bytes on the 3a, not sector-aligned), then the boot image's ramdisk (the payload
FAT volume), then bootconfig. `FindFatVolume` finds the first valid FAT boot sector inside the initrd, and the
driver publishes only that volume, sized from its own header. When the initrd is the volume alone (header v0-v2,
as on sheng), the volume is found at offset 0, as before.

## Bring-up report

At the end of DXE and again at ReadyToBoot (just before UniXNU starts), the driver prints a `SHENGMEM: =====
payload volume report` block:

- the device tree address from x0, the `/chosen` initrd range, and how far the driver got (or why it stopped);
- whether the volume's bytes are unchanged since they were published (CRC32), and how many block reads it has had;
- whether DiskIo and a file system are bound to it, before and after a `ConnectController` on its handle;
- whether `\EFI\UniXNU\unixnu.toml` opens, and how many file systems exist in total.

The ReadyToBoot report holds for 15 seconds so it can be photographed. The connect also makes the volume usable
when nothing else connects it, for example when UniXNU is started as a plain application, which skips its own
connect-all.
