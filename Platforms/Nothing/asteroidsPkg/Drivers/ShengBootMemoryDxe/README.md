# ShengBootMemoryDxe (from UniXNU/uefi-sheng)

Copied unchanged from `Platforms/Xiaomi/shengPkg/Drivers/ShengBootMemoryDxe` in UniXNU/uefi-sheng
(BSD-2-Clause-Patent). It reads the device tree the bootloader passes in x0 (kept by uefi-sheng's SEC in the
`gSiliciumEntryArgHobGuid` HOB), adds DRAM the firmware's own map doesn't cover, and publishes the
`/chosen linux,initrd-start/-end` range as a read-only block device. The UniXNU payload in the boot image's ramdisk
therefore shows up as a FAT volume. It reads every range from the device tree and has no board-specific addresses.
