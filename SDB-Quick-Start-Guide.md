# SDB Quick Start Guide

This page is intended for developers getting started for the first time with the Project Ara MDK and SDB development hardware.

It provides a step-by-step guide to get set up, build the firmware, load it onto the hardware, and verify everything is working by twiddling GPIO and I2C over Greybus.

--------------------------------------

Complete each of the following steps in order. Within each step, there are links to other pages that contain additional information. You may find it helpful to leave this page open and open the other pages in a new tab or window.

- [Step 1. Set up your development computer](#step-1-set-up-your-development-computer)
- [Step 2. Set up your development hardware](#step-2-set-up-your-development-hardware)
- [Step 3. Build the firmware images](#step-3-build-the-firmware-images)
- [Step 4. Load the firmware images](#step-4-load-the-firmware-images)
- [Step 5. Set up Android on Jetson](#step-5-set-up-android-on-jetson)
- [Step 6. Load Greybus modules and connect Jetson to SDB](#step-6-load-greybus-modules-and-connect-jetson-to-sdb)
- [Step 7. Verify GPIO](#step-7-verify-gpio)
- [Step 8. Verify I2C](#step-8-verify-i2c)

--------------------------------------------------------------

###Step 1. Set up your development computer

* Instructions are tested on Ubuntu 14.04. You can use a VM or a physical machine.
* Follow the software setup instructions on [this page](Software-Setup)

--------------------------------------------------------------

###Step 2. Set up your development hardware

* Make sure you have the following:
  * 1 Software Development Board (SDB) development kit (see below)
  * 1 [Jetson TK1 DevKit](https://developer.nvidia.com/jetson-tk1) (may be included with SDB kit)
  * 1 [J-Link Pro JTAG kit](http://www.segger.com/jlink-pro.html), including [J-Link 19-pin Cortex-M Adapter](https://www.segger.com/jlink-adapters.html#CM_19pin) adapter (may be included with SDB kit)
  * 1 [Dediprog SF100 Programmer](http://www.dediprog.com/pd/spi-flash-solution/SF100)
  * 2 [USB A to micro B cables](images/usb-micro-b.jpg)
  * 1 9-pin USB serial adapter with null modem. ([This one from Startech works fine](http://www.startech.com/Cards-Adapters/Serial-Cards-Adapters/USB-to-Null-Modem-RS232-DB9-Serial-Adapter-Cable-DCE-FTDI~ICUSB232FTN)).
  * 1 multimeter for reading GPIO pin voltage

Here's a picture of the SDB, with labels in green for some of the important components, etc. Click the image for a larger version.
<a href="https://github.com/projectara/Firmware-wiki/wiki/images/SDB.jpg"><img src="https://github.com/projectara/Firmware-wiki/wiki/images/SDB.jpg" width="640"/></a>

--------------------------------------------------------------

###Step 3. Build the firmware images
Follow the steps below to build the firmware for the SVC, as well as the APB1 and APB2 bridges on the SDB. Look at the picture of the SDB above to see where those are on the board.

```
cd $HOME/nuttx
./build_ara_image.sh all
```

If the above succeeds, the binary image files you will use for APB1 and APB2 are here:

APB1:  `$HOME/nuttx/build/ara-bridge-es2-debug-apbridgea/image/nuttx.bin`  
APB2:  `$HOME/nuttx/build/ara-bridge-es2-debug-generic/image/nuttx.bin`

The SVC ELF image is here:

SVC:   `$HOME/nuttx/build/ara-svc-bdb2a/image/nuttx`

For more about the firmware build process, see [building the code](Building-the-Code).

--------------------------------------------------------------

###Step 4. Load the firmware images

##### Put all bridges in reset

You must keep each bridge ASIC in reset while programming new images onto their SPI ROMs. To do that, flip all of the reset switches to the right as shown in this image.

<img src="https://github.com/projectara/Firmware-wiki/wiki/images/SDB-Bridge-Reset.jpg"/>

##### Flash APB1 firmware

The APB1 firmware image you built in step 3 is in `$HOME/nuttx/build/ara-bridge-es2-debug-apbridgea/image/nuttx.bin`.

Set up your SDB for programming the APB1 SPI ROM by connecting the SF-100 to the APB1 SPI ROM header:

<img src="https://github.com/projectara/Firmware-wiki/wiki/images/SDB-APB1-SPI-ROM-OK.jpg"/>

**WARNING**: polarity is important. Do not connect the SF 100 like this!

<img src="https://github.com/projectara/Firmware-wiki/wiki/images/SDB-APB1-SPI-ROM-WRONG.jpg"/>

Next, follow the [SPI ROM programming procedure](Flashing-images#load-firmware-image-to-spirom) to flash the APB1 firmware image.

##### Flash APB2 firmware

The APB2 firmware image you built in step 3 is in `$HOME/nuttx/build/ara-bridge-es2-debug-generic/image/nuttx.bin`.

Set up your SDB for programming the APB2 SPI ROM by connecting the SF-100 to the APB2 SPI ROM header:

<img src="https://github.com/projectara/Firmware-wiki/wiki/images/SDB-APB2-SPI-ROM-OK.jpg"/>

**WARNING**: polarity is important. Do not connect the SF 100 like this!

<img src="https://github.com/projectara/Firmware-wiki/wiki/images/SDB-APB2-SPI-ROM-WRONG.jpg"/>

Next, follow the [SPI ROM programming procedure](Flashing-images#load-firmware-image-to-spirom) to flash the APB2 firmware image.

#####Flash SVC firmware with JTAG

The SVC firmware image you built in step 3 is in `$HOME/nuttx/build/ara-svc-bdb2a/image/nuttx`.

Set up your SDB flash it by connecting the J-Link Pro through its adapter board like this:

<img src="https://github.com/projectara/Firmware-wiki/wiki/images/SDB-SVC-JTAG.jpg"/>

Next, follow the [SVC programming procedure](Flashing-images#load-firmware-image-to-svc-internal-flash-on-bdb) to flash the SVC ELF image.

#####Flash SVC firmware with STM32 bootrom

The SVC firmware image you built in step 3 is in `$HOME/nuttx/build/ara-svc-sdb/image/nuttx.bin`.

Next, follow the [SVC programming procedure on SDB with STM32 bootrom](Flashing-images#load-firmware-image-to-svc-internal-flash-on-sdb-via-embedded-bootrom) to flash the SVC binary image.

#####Take APB1 and APB2 out of reset

Now that you've flashed both bridges you are interested in, you can take them out of reset. You put them in reset earlier by flipping their reset switches right. Flip them left to take them out of reset.

--------------------------------------------------------------

###Step 5. Set up Android on Jetson

Follow the [NVidia Jetson Setup](NVidia-Jetson-Setup) instructions to get an Android image with Greybus support running on Jetson.

**MAKE SURE TO USE "sudo fastboot flash boot newboot.img", AND NOT "sudo fastboot boot newboot.img" WHEN REFLASHING JETSON, OR YOUR BOOT IMAGE WILL BE LOST AT REBOOT.**

**DO NOT CONNECT THE JETSON TO SDB UNTIL INSTRUCTED.**

--------------------------------------------------------------

###Step 6. Load Greybus modules and connect Jetson to SDB

**DO NOT CONNECT THE JETSON TO THE SDB UNTIL INSTRUCTED.**

Reboot the Jetson and wait for console output to settle down. Press Enter and the command prompt should appear:
```
shell@jetson:/ $
```

Load the Greybus kernel modules from /lib/modules as follows.

```
su
cd /lib/modules
insmod greybus.ko
insmod gb-phy.ko
insmod gb-vibrator.ko
insmod gb-es2.ko
```

The Jetson USB host port is circled in green in the following image. Click the image for a larger version.

<a href="http://releases-ara-mdk.linaro.org/static/wiki-images/Ports.jpg"><img src="http://releases-ara-mdk.linaro.org/static/wiki-images/Ports.jpg" width="640">

Connect the Jetson USB host port to SDB CON1001 with a micro-B USB cable.  A lot of debug output should be printed.

**NOTE ON "A LOT OF DEBUG OUTPUT": ERROR MESSAGES DOESN'T MEAN IT DIDN'T WORK. KEEP GOING AND LOOK FOR THE LINES DESCRIBED IN THIS SECTION.**

This output should include the following lines:

```
usb 2-1.2.1: New USB device found, idVendor=ffff, idProduct=0002
usb 2-1.2.1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
usb 2-1.2.1: Product: APBridge
usb 2-1.2.1: Manufacturer: Toshiba
usb 2-1.2.1: SerialNumber: 0
```

That output means the AP successfully enumerated APB1 over USB.

The debug output should also include a line that looks like this:

```
gpiochip_add: registered GPIOs 989 to 1015 on device: greybus_gpio

                               ^^^
                               ^^^ note this value!
                               ^^^
```

That output means the GPIO-over-UniPro on APB2 was successfully discovered. The values "989" and "1015" could be different on your system.

**Note**: The GPIO base listed here by gpiochip_add is 989. If your reported value is different, you'll need to substitute that value in the console commands in the following sections.

If APB1 was enumerated and APB2 was successfully discovered, continue to the next section.

If the above output did not appear, or if you have other problems, retry as follows:

* Unplug the USB cable between the Jetson and the SDB
* Power-cycle the SDB using the ON / OFF button at bottom left.
* Retry the instructions in this step.

If that still doesn't work, please report the problem. Including SVC, APB1, APB2, and Jetson console logs will make it easier to support you.

--------------------------------------------------------------

###Step 7. Verify GPIO

In this section, we'll issue some commands to access GPIO 0 on APB2 over Greybus. Greybus requests travel from Jetson to APB2, passing through APB1 and the UniPro switch. Responses take the reverse path.

#####GPIO sysfs files

For more information on GPIO sysfs usage, see
https://www.kernel.org/doc/Documentation/gpio/sysfs.txt

Greybus creates a GPIO controller entry in /sys/class/gpio/ when it receives a manifest with GPIO Protocol enabled, as it does with APB2.  Dump the GPIO controllers' label attributes to identify the one associated with Greybus:

```
# cat /sys/class/gpio/gpiochip*/label
tegra-gpio
as3722-gpio
greybus_gpio
```

You can double check by dumping the label explicitly:

```
#cat /sys/class/gpio/gpiochip989/label
greybus_gpio
```

#####GPIO Number

The GPIO base number is used as an offset for all GPIOs associated with the device. Examine the gpiochip base value:
```
# cat /sys/class/gpio/gpiochip989/base
989
```
Thus, 989 base + 0 offset (for GPIO 0 on APB2) = GPIO number 989.

#####Export the GPIO

In order to manipulate GPIOs using sysfs files, you must export the gpio number:
```
# echo 989 > /sys/class/gpio/export
```
This command adds a new entry in /sys/class/gpio/, gpio*n*, where *n* is the exported gpio number.
```
# ls /sys/class/gpio
export
gpio143
gpio989
gpiochip0
gpiochip1016
gpiochip989
unexport
```

#####Get and Set GPIO Direction

To get the currently configured GPIO direction:
```
# cat /sys/class/gpio/gpio989/direction
in
```
To set the direction:
```
# echo out > /sys/class/gpio/gpio989/direction
```

#####Get and Set GPIO Value

To get the value:
```
# cat /sys/class/gpio/gpio989/value
0
```
To set the value:
```
# echo n > /sys/class/gpio/gpio989/value
```
Where *n* equals 0 or 1.

Measure the voltage on the SDB at J1101 pin 1.

You should read approximately 1.8V above GND when the GPIO value is 1, and very close to 0.0 when the value is 0.  For an easy GND connection, use any of the standoff mounting holes at the corners of the SDB, or the 0.1" debug headers labeled GND.

--------------------------------------------------------------

###Step 8. Verify I2C

XXX TODO XXX

-----------------------------------------------------------

#***Congratulations! You've completed the SDB Quick Start Guide!***
