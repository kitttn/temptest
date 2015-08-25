# Building TFTF packages using the [bootrom-tools](https://github.com/projectara/bootrom-tools)

The following instructions assume that you have already built bootrom-tools, the Jetson kernel with Greybus modules, and the module firmware you intend to package.  Assume that each project is stored as follows:

* The Jetson kernel having its build directory in $JKB_HOME
* The bootrom-tools being stored in $BOOTROM_TOOLS
* The firmware being stored in $FW_BUILD/image

Additionally, you will need to know the UniPro and Ara vendor and product ID numbers for the module onto which you intend to load firmware.  Assume they are $UNIPRO_VID, $UNIPRO_PID, $ARA_VID, and $ARA_PID; when values are not specified, they will be filled-in with defaults.

TFTF packages are created through the use of the `create-tftf` tool.  For example:

    ~ $ $BOOTROM_TOOLS/create-tftf -v \
    --elf ~/nuttx/build/ara-bridge-es2-debug-generic/image/nuttx \
    --unipro-vid $UNIPRO_VID --unipro-pid $UNIPRO_PID \
    --ara-vid $ARA_VID --ara-pid $ARA_PID --start 0x100012e0

This verbosely outputs a TFTF package consisting in the code and data sections taken from the given ELF binary, with the given vendor and product ID numbers and the given entry point.  It outputs the default firmware stage, that being 2; this can be specified manually via `--ara-stage`.  Refrain from passing an output filename via `--out` to get a filename automatically produced according to the Linux firmware framework's expectations.  The result should be a file of the form:

    ~ $ ls
    ara:$UNIPRO_VID:$UNIPRO_PID:$ARA_VID:$ARA_PID:0002.tftf