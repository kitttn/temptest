This page is intended for developers getting started for the first time with the Project Ara MDK and development hardware. This provides a step-by-step guide to get set up, build the firmware, load it onto the hardware, and verify everything is working by twiddling GPIO and I2C over Greybus. 

--------------------------------------

Complete each of the following steps in order. Within each step, there are links to other pages that contain additional information. You may find it helpful to leave this page open and open the other pages in a new tab or window.

[Step 1. Set up your development computer](#step-1-set-up-your-development-computer)  
[Step 2. Set up your development hardware](#step-2-set-up-your-development-hardware)  
[Step 3. Build the firmware images](#step-3-build-the-firmware-images)  
[Step 4. Load the firmware images](#step-4-load-the-firmware-images)  
[Step 5. Flash Jetson with Android image](#step-5-flash-jetson-with-android-image)  
[Step 6. Rebuild Jetson kernel](#step-6-rebuild-jetson-kernel)  
[Step 7. Load Greybus modules and connect Jetson to BDB](#step-7-load-greybus-modules-and-connect-jetson-to-bdb)  
[Step 8. Verify GPIO](#step-8-verify-gpio)    
[Step 9. Verify I2C](#step-9-verify-i2c)    

--------------------------------------------------------------

###Step 1. Set up your development computer

* Instructions are tested on Ubuntu 14.04. You can use a VM or a physical machine.
* Follow the software setup instructions on [this page](Software-Setup)

--------------------------------------------------------------

###Step 2. Set up your development hardware

* Make sure you have the following:
  * 1 [Big Development Board version 2A (BDB2A) kit](images/BDB2A.jpg)
  * 1 [Jetson TK1 DevKit](https://developer.nvidia.com/jetson-tk1) (may be included with BDB2A kit)
  * 2 [J-Link Pro JTAG interfaces](http://www.segger.com/jlink-pro.html)
  * 1 [Dediprog SF100 Programmer](http://www.dediprog.com/pd/spi-flash-solution/SF100)
  * 3 [USB A to micro B cable](images/usb-micro-b.jpg)
  * 1 USB 2.0 hub
  * 1 [9-pin USB serial adapter](images/usb_ser.jpg)
  * 1 [9-pin female-female null modem](images/nullmodem9ff.jpg) 
  * 1 Multimeter for reading GPIO pin voltage
  * [ProjectAra MDK](http://www.projectara.com/mdk/)
* Follow the hardware setup instructions on [this page](Hardware-Setup)

--------------------------------------------------------------

###Step 3. Build the firmware images
Follow the steps below to build the firmware for APB1 and APB2 bridges
on the BDB.  

* `cd $HOME/nuttx`  
* `./build_ara_image.sh ara bridge/es2-debug-apbridgea`  
* `./build_ara_image.sh ara bridge/es2-debug-generic`  

The resulting binary image files are here:  
APB1:  `$HOME/nuttx/build/ara-bridge-es2-debug-apbridgea/images/nuttx.bin`  
APB2:  `$HOME/nuttx/build/ara-bridge-es2-debug-generic/images/nuttx.bin`  

--------------------------------------------------------------

###Step 4. Load the firmware images

#####Option 1: Load firmware image to flash (RECOMMENDED)  
For each of the firmware images listed in step 3, follow 
[this procedure](Flashing-images#load-firmware-image-to-spirom).

#####Option 2: Load firmware image using JTAG
For each of the firmware images listed in step 3, follow 
[this procedure](Debugging#how-to-debug-apgp-bridge-firmware-using-jtag). 

#####Build and load SVC firmware (OPTIONAL) 
The SVC is loaded with the latest firmware prior to shipping the BDB. 
These steps are included *just in case* you need to reflash the SVC. 

Build SVC firmware:
* `cd $HOME/nuttx`
* `./build_ara_image.sh ara svc/bdb2a

Flash SVC firmware:  
Follow [this procedure](Flashing-images#load-firmware-image-to-svc-internal-flash-on-bdb) using the ELF  file you just built:  
`$HOME/nuttx/build/ara-svc-bdb2a/images/nuttx`. 

--------------------------------------------------------------

###Step 5. Flash Jetson with Android image

Jetson has a 9-pin serial port which is used for the u-boot console, boot log, and kernel console. Connect a 9-pin female-female null modem and USB serial adapter. The Jetson serial port is circled in orange in [this picture](http://releases-ara-mdk.linaro.org/static/wiki-images/Ports.jpg).  Run a terminal program on the host at 115200-n-8-1.

The Android image is part of the "Android_for_Jetson NVFlash Package". Download the 5.1.0 release package from [here](http://releases-ara-mdk.linaro.org/jetson-5.1.0-open/nvidia-jetson-ara-android-lmy47z.tgz), and follow the "Instructions for using the NVflash package" section on [this page](https://github.com/projectara/Android-wiki/wiki/Build-and-Boot-Instructions-for-Jetson-reference-platform).

###Step 6. Rebuild Jetson kernel

Rebuild the Jetson kernel and Greybus modules, and update the Jetson with newboot.img, by following the steps on [this page](https://github.com/projectara/Android-wiki/wiki/Kernel-Only-Build-Instructions-for-Jetson-reference-platform).

--------------------------------------------------------------

###Step 7. Load Greybus modules and connect Jetson to BDB

**DO NOT CONNECT THE JETSON TO THE BDB UNTIL INSTRUCTED.**

Reboot the Jetson and wait for console output to settle down. Press Enter and the command prompt should appear:  
```
shell@jetson:/ $ 
```

Load the Greybus kernel modules from /lib/modules as follows. Prompt strings and time stamps removed for clarity.
```
$ su
# cd /lib/modules
# insmod greybus.ko
# insmod gb-phy.ko                                   
greybus: Registered gpio protocol.
greybus: Registered pwm protocol.
greybus: Registered uart protocol.
greybus: Registered sdio protocol.
greybus: Registered usb protocol.
greybus: Registered i2c protocol.
greybus: Registered spi protocol.
greybus: Registered hid protocol.
greybus: Registered gb_audio_mgmt protocol.
greybus: Registered gb_audio_data protocol.
# insmod gb-vibrator.ko                              
greybus: Registered vibrator protocol.
# insmod gb-es2.ko
usbcore: registered new interface driver es2_ap_driver
#
```
At this point, you should have 3 serial terminal sessions open: 
* BDB bridge APB1 debug output
* BDB bridge APB2 debug output
* Jetson console

The APB1 and APB2 windows should show the nutshell prompt `nsh>`, and the Jetson console should show `shell@jetson:/ #`. You may wish to position and resize these windows so you can monitor the debug output from all of them at the same time. 

The Jetson USB host port is circled in green in [this picture](http://releases-ara-mdk.linaro.org/static/wiki-images/Ports.jpg). BDB CON28 "USB1 to APB1 HSIC" is in the upper left corner of the BDB.  

Connect a USB 2.0 hub to the Jetson USB2 host port.

Connect the hub to BDB CON28.  You should see the following on the Jetson console:

```
usb 2-1.2.1: New USB device found, idVendor=ffff, idProduct=0002
usb 2-1.2.1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
usb 2-1.2.1: Product: APBridge
usb 2-1.2.1: Manufacturer: Toshiba
usb 2-1.2.1: SerialNumber: 0
gpiochip_add: registered GPIOs 989 to 1015 on device: greybus_gpio
                               ^^^ note this value!
```
**Note**: The GPIO base listed here by gpiochip_add is 989. If your reported value is different, you'll need to substitute that value in the console commands.

You should also see the following on the APB1 debug output:
```
[I] GB: MID-1 module inserted                                              	 
[I] GB: AP handshake complete  	
```

This indicates the AP to APB1 link was successfully established. If so, continue with [Step 8](#step-8-verify-gpio).

If the above output did not appear, or if you have other problems, retry as follows:    
* Unplug the USB cable between the Jetson and the BDB
* Cycle power to the BDB (Note - If you used the JTAG method for loading the bridge firmware, you'll need to repeat [those steps](#option-2-load-firmware-image-using-jtag) after cycling power to the BDB).  
* [Go back to this step](#step-7-load-greybus-modules-and-connect-jetson-to-bdb).

--------------------------------------------------------------

###Step 8. Verify GPIO

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

Measure the voltage on the BDB at J79 pin 1. [Here's a picture of J79](images/BDB1B-Header-J79.png). You should read approximately 1.8V above GND when the GPIO value is 1, and very close to 0.0 when the value is 0.  For an easy GND connection, use any of the standoff mounting holes at the corners of the BDB.

###Step 9. Verify I2C

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
                         
#***Congratulations! You've completed the Quick Start Guide!***