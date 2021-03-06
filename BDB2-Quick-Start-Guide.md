# BDB2 Quick Start Guide

This page is intended for developers getting started for the first time with the Project Ara MDK and development hardware.

This provides a step-by-step guide to get set up, build the firmware, load it onto the hardware, and verify everything is working by twiddling GPIO and I2C over Greybus.

--------------------------------------

Complete each of the following steps in order. Within each step, there are links to other pages that contain additional information. You may find it helpful to leave this page open and open the other pages in a new tab or window.

- [Step 1. Set up your development computer](#step-1-set-up-your-development-computer)
- [Step 2. Set up your development hardware](#step-2-set-up-your-development-hardware)
- [Step 3. Build the firmware images](#step-3-build-the-firmware-images)
- [Step 4. Load the firmware images](#step-4-load-the-firmware-images)
- [Step 5. Set up Android on Jetson](#step-5-set-up-android-on-jetson)
- [Step 6. Load Greybus modules and connect Jetson to BDB](#step-6-load-greybus-modules-and-connect-jetson-to-bdb)
- [Step 7. Verify GPIO](#step-7-verify-gpio)
- [Step 8. Verify I2C](#step-8-verify-i2c)

--------------------------------------------------------------

###Step 1. Set up your development computer

* Instructions are tested on Ubuntu 14.04. You can use a VM or a physical machine.
* Follow the software setup instructions on [this page](Software-Setup)

--------------------------------------------------------------

###Step 2. Set up your development hardware

* Make sure you have the following:
  * 1 Big Development Board (BDB) version 2A (BDB2A) or version 2B (BDB2B) kit, which should include 1 BDB and 2 debug boards (see below)
  * 1 [Jetson TK1 DevKit](https://developer.nvidia.com/jetson-tk1) (may be included with BDB2A or BDB2B kit)
  * 1 [J-Link Pro JTAG interfaces](http://www.segger.com/jlink-pro.html)
  * 1 [Dediprog SF100 Programmer](http://www.dediprog.com/pd/spi-flash-solution/SF100)
  * 3 [USB A to micro B cable](images/usb-micro-b.jpg)
  * 1 USB 2.0 hub (or a computer with 3 USB 2.0 connectors)
  * 1 [9-pin USB serial adapter](images/usb_ser.jpg)
  * 1 [9-pin female-female null modem](images/nullmodem9ff.jpg)
  * 1 multimeter for reading GPIO pin voltage

Here's a picture of the BDB, with labels in white for some of the important components, etc. Click the image for a larger version.
<a href="https://github.com/projectara/Firmware-wiki/wiki/images/BDB2A.jpg"><img src="https://github.com/projectara/Firmware-wiki/wiki/images/BDB2A.jpg" width="640"/></a>

Here's a picture of the debug boards that come with the BDB, with labels in white for some important components, and labels in red for unused components. Click the image for a larger version.
<a href="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/BDB_debug_board.JPG"><img src="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/BDB_debug_board.JPG" width=640/></a>

--------------------------------------------------------------

###Step 3. Build the firmware images
Follow the steps below to build the firmware for the SVC, as well as the APB1 and APB2 bridges on the BDB. Look at the picture of the BDB above to see where those are on the board.

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

##### Flash APB1 firmware

The APB1 firmware image you built in step 3 is in `$HOME/nuttx/build/ara-bridge-es2-debug-apbridgea/image/nuttx.bin`.

Set up your BDB2 and debug board to flash it by connecting the debug board to CON17 on the BDB2:

<img src="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/bdb2b-apb1-debug-board-setup.jpg"/>

Next, follow the [SPI ROM programming procedure](Flashing-images#load-firmware-image-to-spirom) to flash the APB1 firmware image.

##### Flash APB2 firmware

The APB2 firmware image you built in step 3 is in `$HOME/nuttx/build/ara-bridge-es2-debug-generic/image/nuttx.bin`.

Set up your BDB2 and debug board to flash it by connecting the debug board to CON19 on the BDB2:

<img src="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/bdb2b-apb2-debug-board-setup.jpg"/>

Next, follow the [SPI ROM programming procedure](Flashing-images#load-firmware-image-to-spirom) to flash the APB2 firmware image.

#####Flash SVC firmware

The SVC firmware image you built in step 3 is in `$HOME/nuttx/build/ara-svc-bdb2a/image/nuttx`.

Set up your BDB2 and debug board to flash it by connecting the debug board to CON18:

<img src="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/bdb2b-svc-jtag-setup.jpg"/>

Next, follow the [SVC programming procedure](Flashing-images#load-firmware-image-to-svc-internal-flash-on-bdb) to flash the SVC ELF image.

--------------------------------------------------------------


###Step 5. Set up Android on Jetson

Follow the [NVidia Jetson Setup](NVidia-Jetson-Setup) instructions to get an Android image with Greybus support running on Jetson.

**MAKE SURE TO USE "sudo fastboot flash boot newboot.img", AND NOT "sudo fastboot boot newboot.img" WHEN REFLASHING JETSON, OR YOUR BOOT IMAGE WILL BE LOST AT REBOOT.**

**DO NOT CONNECT THE JETSON TO BDB UNTIL INSTRUCTED.**

--------------------------------------------------------------

###Step 6. Load Greybus modules and connect Jetson to BDB

**DO NOT CONNECT THE JETSON TO THE BDB UNTIL INSTRUCTED.**

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

The BDB CON28 "USB1 to APB1 HSIC" is in the upper left corner of the BDB.

Connect the Jetson USB host port to BDB CON28 with a micro-B USB cable.  A lot of debug output should be printed.

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

* Unplug the USB cable between the Jetson and the BDB.
* Power-cycle the BDB.
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

Measure the voltage on the BDB at J79 pin 1.

Here's a picture of J79. Click the image for a larger version.
<a href="https://github.com/projectara/Firmware-wiki/wiki/images/BDB1B-Header-J79.png"><img src="https://github.com/projectara/Firmware-wiki/wiki/images/BDB1B-Header-J79.png" width="640">

You should read approximately 1.8V above GND when the GPIO value is 1, and very close to 0.0 when the value is 0.  For an easy GND connection, use any of the standoff mounting holes at the corners of the BDB.

###Step 8. Verify I2C

In this section, we'll issue some commands to examine I2C devices over Greybus. Greybus requests travel from Jetson to APB2, passing through APB1 and the UniPro switch. Responses take the reverse path.

We will use the i2c-tools utilities: i2cdetect, i2cdump, i2cget, and i2cset. These are included in the image. Also note that Greybus creates entries in /sys/class/i2c-dev/ in a similar fashion as GPIO.

Use i2cdetect to enumerate i2c adapters, with the -l argument:
````
# i2cdetect -l
i2c-0	i2c       	Tegra I2C adapter               	I2C adapter
i2c-1	i2c       	Tegra I2C adapter               	I2C adapter
i2c-2	i2c       	Tegra I2C adapter               	I2C adapter
i2c-3	i2c       	Tegra I2C adapter               	I2C adapter
i2c-4	i2c       	Tegra I2C adapter               	I2C adapter
i2c-5	i2c       	Tegra I2C adapter               	I2C adapter
i2c-6	i2c       	Greybus i2c adapter             	I2C adapter
```
Greybus is adapter (bus) number 6.

Use i2cdetect to enumerate the i2c devices. The -y argument suppresses prompting, -r probes using the read method, and 6 is the i2c bus number.
````
# i2cdetect -y -r 6
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f                            
00:          -- -- -- -- -- -- -- -- -- -- -- -- --                            
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --                            
20: -- -- -- -- -- -- -- -- -- 29 -- -- -- -- -- --                            
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --                            
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --                            
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --                            
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --                            
70: -- -- -- -- -- -- -- --                             
````
The output shows that a device has responded at address 0x29. This corresponds to U62, an Intersil ISL98607 LCD Power Controller.

Use i2cget to read a value from a specific device. Arguments: -y suppresses prompting, 6 is the bus number, 0x29 is the i2c device address, 3 is the byte address, and 'c' indicates 8 bit read.
````
# i2cget -y 6 0x29 3 c                                 
0x14
````


-----------------------------------------------------------
                         
#***Congratulations! You've completed the BDB2 Quick Start Guide!***