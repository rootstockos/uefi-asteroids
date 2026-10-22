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
