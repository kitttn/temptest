# Boot Over UniPro

The Project Ara boot infrastructure for modules provides two main ways for modules to load their Stage 2 firmware: via SPI flash or via UniPro.  Both of these require that the firmware first be packaged as a Trusted Firmware Transfer Format package from its compiled binary, and the SPI flash alternative requires that the TFTF package then be wrapped inside a Flash Format For Firmware package.  This page will explain the steps necessary to use the Boot-Over-UniPro alternative.

Developers intending to test their firmware through Boot-Over-Unipro will need the following before beginning:

* The module firmware they intend to test, in either raw binary or ELF form, with its [Ara Interface Manifest](Interface-Manifest-HOWTO) built into the firmware image.
* At least ES2 hardware: a [Big Dev Board](BDB2-Quick-Start-Guide) or [Software Development Board](SDB-Quick-Start-Guide), with [firmware loaded into its Supervisory Controller and APBridge A](Flashing-images).
* Hardware attribute values for the module they intend to boot over UniPro: the UniPro vendor and product ID numbers, and the Ara vendor and product ID numbers.  At this time, the dummy Ara VID and PID numbers returned by the SVC for all modules are: `0xdead` and `0xbeef`.
* [A Jetson board with up-to-date Greybus support](NVidia-Jetson-Setup) in its Android kernel.

## Using bootrom-tools to construct a Trusted Firmware Transfer Format package

Follow the instructions for [building TFTF packages using bootrom-tools](Building-TFTF-bootrom-tools), making sure *not* to pass the `--out` option to `create-tftf` so that it will write the package to a filename formatted for the Linux kernel's firmware framework.

## Adding a TFTF package to the Ara Android kernel's firmware framework on Jetson

Follow the instructions for [adding a TFTF firmware package to the Jetson's firmware framework](TFTF-Firmware-Jetson), then follow the instructions to [flash Jetson with the resulting disk image](https://github.com/projectara/Android-wiki/wiki/Kernel-Only-Build-Instructions-for-Jetson-reference-platform).

## Preparing the BDB2 to boot its bridge modules over UniPro

1. Attach a debug board to the Supervisory Controller of the BDB2:

    <img src="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/bdb2b-svc-jtag-setup.jpg" />

2. Flash the SVC firmware to the board [according to the normal procedure](Flashing-images#load-firmware-image-to-svc-internal-flash-on-bdb).

3. Attach a debug board to the APBridge A on the BDB2:

    <img src="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/bdb2b-apb1-debug-board-setup.jpg" />

4. Flash the APBA firmware to the board by [following the instructions for APB1](Flashing-images#load-firmware-image-to-spirom).

5. In the same directory where you've [built the rest of your Project Ara firmware](Building-the-Code), clone the [bootrom](https://github.com/projectara/bootrom) source:

    `git clone https://github.com/projectara/bootrom.git`

6. Since the BDB2 contains ES2 bridge hardware, configure the bootrom for building on ES2 (`es2tsb`).  Alternatives include building for generic ES3 hardware (`es3tsb`) or FPGA implementations of ES3 (`fpgatsb`):

    `~/bootrom $ ./configure es2tsb`

7. Build the bootrom.  You should see output similar to the following:

    ```
    ~/bootrom $ make
    Generating manifest data...
    Assembling chips/tsb/src/boot.S
    Assembling chips/tsb/src/copy_to_ram.S
    Compiling chips/tsb/src/tsb_chipapi.c
    Compiling chips/tsb/src/tsb_scm.c
    Compiling chips/tsb/src/tsb_dbguart.c
    Compiling chips/tsb/src/tsb_isaa.c
    Compiling chips/tsb/src/tsb_unipro.c
    Compiling common/src/start.c
    Compiling common/src/tftf.c
    Compiling common/src/ffff.c
    Compiling common/src/crypto.c
    Compiling common/src/utils.c
    Compiling common/src/unipro.c
    Compiling common/src/gbcore.c
    Compiling common/src/debug.c
    Compiling common/src/gbfirmware.c
    Compiling chips/es2tsb/src/es2_spi.c
    Compiling chips/es2tsb/src/es2_unipro.c
    Compiling chips/es2tsb/src/es2_efuse.c
    Compiling chips/es2tsb/src/es2_advertise.c
    Linking build/bootrom
    Wrote build/bootrom.hex
    Done
    ```

8. Follow the [standard instructions](Flashing-images#load-firmware-image-to-spirom) for flashing firmware to all the other bridges, except replacing the nuttx binary with the [bootrom](https://github.com/projectara/bootrom) one:

    ```
    truncate -s 2M ~/bootrom/build/bootrom.bin
    flashrom --programmer dediprog -w ~/bootrom/build/bootrom.bin
    ```

9. Remove power from the BDB after flashing the bootrom to all the bridge chips (everything but APBA and the SVC), and ensure that no switches on any debug board or on the BDB itself are holding bridges in reset.

10. Attach a USB cable to the SVC's UART output as pictured below, and then start `minicom` to obtain the SVC's console.  Your console number under `/dev/ttyUSBN` may vary: please plug USB cables into your workstation in a well-defined order to ensure you know where the SVC console will be made available.  You should not see any console output at this time, since you have yet to apply power to the BDB.

    ```
    minicom --device /dev/ttyUSB1
    ```

    <img src="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/bdb2b-svc-jtag-setup.jpg" />

9. Attach a USB cable to the UART output of a debug board attached to a bridge chip (anything but APBA or the SVC), and then start `minicom` to obtain a bridge's console output.  Your console number under `/dev/ttyUSBN` may vary: please plug USB cables into your workstation in a well-defined order to ensure you know where the bridge console will be made available.  You should not see any console output at this time, since you have yet to apply power to the BDB.

    ```
    minicom --device /dev/ttyUSB2
    ```

    <img src="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/bdb2b-apb2-debug-board-setup.jpg" />

11. Apply power to the BDB.

   You should see console output similar to the following on the SVC's console:

    ```
    Welcome to minicom 2.7

    OPTIONS: I18n 
    Compiled on Jan  1 2014, 17:13:19.
    Port /dev/ttyUSB1

    Press CTRL-A Z for help on special keys

    ABCD__ara_build_target 'ara-svc-bdb2a'
    __ara_git_version  'd63a114-dirty'
    F
    [DBG_SVC]: starting svcd

    NuttShell (NSH)
    nsh> [DBG_SVC]: Power off all interfaces
    [DBG_SWITCH]: Initializing ES2 switch...
    [DBG_SWITCH]: ... Done!
    [DBG_SWITCH]: Applying M-PHY fixup settings to switch ports
    [DBG_SWITCH]: switch_detect_devices: Link status: 0x0
    [DBG_SVC]: Initializing all interfaces
    unipro_driver_register: Registering driver svcd-greybus on cport: 4
    unipro_driver_register: Registered driver svcd-greybus on cport: 4
    [DBG_SVC]: event received: type: 0 port: 1 val: 2
    [DBG_SVC]: event received: type: 0 port: 2 val: 2
    [DBG_SVC]: event received: type: 0 port: 3 val: 2
    [DBG_SVC]: event received: type: 0 port: 4 val: 2
    ```

   You should see output similar to the following on the bridge chip's console:

    ```
    Welcome to minicom 2.7

    OPTIONS: I18n 
    Compiled on Jan  1 2014, 17:13:19.
    Port /dev/ttyUSB2, 14:59:28

    Press CTRL-A Z for help on special keys

    Hello world from boot ROM!
    Unipro enabled!
    BOOTSELECTOR 0
    bootrom_main: Boot from SPIROM
    FFFF Bad sentinel
    FFFF Bad sentinel
    FFFF Bad sentinel
    FFFF Bad sentinel
    FFFF Bad sentinel
    FFFF Bad sentinel
    FFFF Bad sentinel
    FFFF Bad sentinel
    FFFF Bad sentinel
    FFFF Bad sentinel
    FFFF Bad sentinel
    FFFF Bad sentinel
    Failed to locate FFFF table
    couldn't locate image
    bootrom_main: Boot over UniPro
    Module ready advertised.
    Ready-poked from switch: ready to download firmware.
    ```

   This indicates that the bootrom and SVC have coordinated and are ready to download your firmware package via the Greybus Firmware protocol as soon as the AP is attached.

12. Plug the serial-to-USB converter cable into Jetson, and attach its USB end to your workstation.  Open a console on the Jetson board:

    ```
    minicom --device /dev/ttyUSB0
    ```

    As always, your value of `N` in `/dev/ttyUSBN` may vary.  Apply power to the Jetson board, and wait for it to boot.

13. From the shell of the booted Jetson board, acquire `root` privileges and insert the Greybus modules into the running kernel:

    ```
    $ su
    # cd /lib/modules
    # insmod greybus.ko
    # insmod gb-es2.ko
    ```

14. Use a mini-USB-to-USB cable to attach the BDB2 to the Jetson board, with the mini-USB being plugged into the "USB to APB1 HSIC" connector on the BDB2 and the USB end being plugged into the JIC2 USB2 connector on the Jetson board.  APBA should present the Jetson board's Greybus interface as an AP to the SVC, which should then automatically handle setting up the appropriate connections for the Jetson AP to control the rest of the bridge chips.

   Once those connections exist, the Jetson board's Greybus Firmware driver should automatically identify a firmware package in the Linux firmware directory by its UniPro and Ara VID/PID numbers, and proceed to download that package via Greybus Firmware.  The bridge chips' running bootrom will receive, load, and run the firmware via Greybus Firmware.

## Preparing the SDB to boot its bridge modules over UniPro