# Ventoy 

This is a fork from [original Ventoy V1.0.71 module](https://github.com/ventoy/Ventoy). This modified version will try to install locally on hard disk 0 in partition 2 in replacement of MSR one. Menu will be done by adding a grub line for it. My try is done on GPT system.

## Installing/compiling:

1) Resize sda1 efi partition to 84 Mo
2) Delete partion MSR, create sda2  32 Mo & format fat 16
3) Copy a Ventoy formatted USB stick 1.0.71 partition VTOYEFI to this partition
4) Modify grub patition start (6 for me) instead 1 in (sda2) grub/grub.cfg  ->  set vtoy_iso_part=($vtoy_dev,6)
5) Modify [ventoy_cmd.c](https://github.com/enigma131/Ventoy/commit/90cb250db5ec096ddd3811fbe65d2a38f1398fdf) and [ventoy_unix.c](https://github.com/enigma131/Ventoy/commit/70cbe11b640b162f8b05ba4ae47abab4d6585b76). Repack BOOTX64.EFI and replace it in sda2 part
6) Modify [ctoydump.c](https://github.com/enigma131/Ventoy/commit/ca4773986d72f366671af2be9506445ad8ac9e05), then recompile vtoytool_64.

Uncompress original tool.cpio: (in ventoy_x86.cpio):

    busybox cpio -i -m -F tool.cpio
    
Replace vtoytool_64, then create new cpio:

    find tool | busybox cpio -o -H newc --owner=root:root >./tool.cpio
Recompress it:

    xz tool.cpio
Uncompress original ventoy_x86.cpio:

    busybox cpio -i -m -F ventoy_x86.cpio
Replace tool.cpio.xz form above
Repack all, then replace result in sda2 part:

    find ventoy | busybox cpio -o -H newc --owner=root:root >./ventoy_x86.cpio
    
7) Modify [vtoyjump.c](https://github.com/enigma131/Ventoy/commit/d45f71cb098ace70166c8d4bea35690dee100287), recompile vtoyjump64.exe and replace it in sda2 part.

8) Create chainloader entry in grub menu

Edit Grub custom:

    sudo nano /etc/grub.d/40_custom
    
Add the lines :
menuentry 'Ventoy' {
chainloader (hd0,2)/EFI/BOOT/BOOTX64.EFI
}
Save file, then:

    sudo update-grub

Et voila!


