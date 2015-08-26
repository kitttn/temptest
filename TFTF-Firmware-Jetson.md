# Adding a TFTF package to the kernel's firmware framework

The following instructions assume that a TFTF firmware package has [already been built](Building-TFTF-bootrom-tools) in your home directory (it can be placed anywhere, but adjust the instructions if so).

We will now add this file to the firmware-loading framework of the Linux kernel running on the Jetson board.  $JKB_HOME should contain a directory called `boot-files/ramdisk`, with a subdirectory `boot-files/ramdisk/lib`.  This forms the `/lib/` directory of the Android-Linux system after it boots on the Jetson board.  The Linux firmware framework expects to find its firmware in the `/lib/firmware` directory, and the Greybus subsystem expects to find firmware blobs of approximately the above filename format, so we should create them.

    ~ $ cd $JKB_HOME/boot-files/ramdisk/lib
    $JKB_HOME/boot-files/ramdisk/lib $ mkdir firmware
    $JKB_HOME/boot-files/ramdisk/lib $ mv ~/ara:$UNIPRO_VID:$UNIPRO_PID:$ARA_VID:$ARA_PID:0002.tftf firmware/

The firmware blob now resides in the correct format and location for Greybus Firmware to find it when booting the module.