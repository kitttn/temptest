This page is intended for developers getting started for the first time with the Project Ara MDK and development hardware. This provides a step-by-step guide to get set up, build the firmware, load it onto the hardware, and verify everything is working by twiddling GPIO and I2C over Greybus. 

--------------------------------------

Complete each of the following steps in order. Within each step, there are links to other pages that contain additional information. When you are finished with those pages, you can either click the back button in your browser, or click the 'Quick Start Guide' link in the page menu on the right.

[Step 1. Set up your development computer](#step-1-set-up-your-development-computer)  
[Step 2. Set up your development hardware](#step-2-set-up-your-development-hardware)  
[Step 3. Build the firmware images](#step-3-build-the-firmware-images)  
[Step 4. Load the firmware images](#step-4-load-the-firmware-images)  
[Step 5. Flash Jetson with Android image](#step-5-flash-jetson-with-android-image)  
[Step 6. Rebuild Jetson kernel](#step-6-rebuild-jetson-kernel)  
[Step 7. Load Greybus modules and connect Jetson to BDB](#step-7-load-greybus-modules-and-connect-jetson-to-bdb)  
[Step 8. Connect Jetson to BDB](#step-8-connect-jetson-to-bdb)    
[Step 9. Verify GPIO and I2C](#step-9-verify-gpio-and-i2c)    

--------------------------------------------------------------

###Step 1. Set up your development computer

* Make sure you are running Linux 14.04. You can use a VM or a physical machine.
* Follow the software setup instructions on [this page](Software-Setup)

--------------------------------------------------------------

###Step 2. Set up your development hardware

* Make sure you have the following:
  * 1 [Big Development Board version 2A (BDB2A) kit](images/bdb2a_kit.jpg)
  * 1 [Jetson TK1 DevKit](https://developer.nvidia.com/jetson-tk1) (may be included with BDB2A kit)
  * 2 [J-Link Pro JTAG interfaces](http://www.segger.com/jlink-pro.html)
  * 1 [Dediprog SF100 Programmer](http://www.dediprog.com/pd/spi-flash-solution/SF100)
  * 3 [USB A to micro B cable](images/usb-micro-b.jpg)
  * 1 USB 2.0 hub
  * 1 [9-pin USB serial adapter](images/usb_ser.jpg)
  * 1 [9-pin female-female null modem](images/nullmodem9ff.jpg) 
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
The SVC is loaded with the latest firmware prior to shipping the BDB, 
these steps are included *just in case* you need to reflash the SVC. 

Build SVC firmware:
* `cd $HOME/nuttx`
* `./build_ara_image.sh ara svc/bdb2a

Flash SVC firmware:  
Follow [this procedure](Flashing-images#load-firmware-image-to-svc-internal-flash-on-bdb) using the ELF  file you just built:  
`$HOME/nuttx/build/ara-svc-bdb2a/images/nuttx`. 

--------------------------------------------------------------

###Step 5. Flash Jetson with Android image

The Android image is packaged as the "Android_for_Jetson NVFlash Package". Follow the instructions through the end of the "Instructions for using the NVflash package" section on [this page](https://github.com/projectara/Android-wiki/wiki/Build-and-Boot-Instructions-for-Jetson-reference-platform). There are also instructions there for connecting to the Jetson serial port, which we will use as a console in later steps.

###Step 6. Rebuild Jetson kernel

Rebuild the Jetson kernel and Greybus modules, and update the Jetson board with "newboot.img.
Follow the steps on [this page](https://github.com/projectara/Android-wiki/wiki/Kernel-Only-Build-Instructions-for-Jetson-reference-platform).

--------------------------------------------------------------

###Step 7. Load Greybus modules and connect Jetson to BDB

**NOTE: DO NOT CONNECT THE JETSON TO THE BDB VIA USB UNTIL INSTRUCTED.**

Reboot the Jetson and wait for console output to settle down. Press Enter in the console and the command prompt should appear:  
```
shell@jetson:/ $ 
```

Load the Greybus kernel modules from /lib/modules as follows:
```
su  
cd /lib/modules`
insmod greybus.ko
insmod gb-phy.ko
insmod gb-vibrator.ko
insmod gb-es2.ko
```
Upon loading gb-phy.ko, you should see a number of greybus messages about registering protocols.  Upon loading gb-es1.ko, you should see a usbcore message about registering a new interface driver.

####Connect the Jetson to the BDB via USB cable

At this point, you should have 3 serial terminal sessions open: 
* BDB bridge APB1 debug output
* BDB bridge APB2 debug output
* Jetson console

The APB1 and APB2 windows should show the "nsh>" nutshell prompt, and the Jetson console should show "shell@jetson:/ #"

You may wish to position and resize these windows so you can monitor the debug output from all of them at the same time. 

The Jetson USB host port is circled in green in [this picture](http://releases-ara-mdk.linaro.org/static/wiki-images/Ports.jpg).

BDB CON28 "USB1 to APB1 HSIC" is in the upper left corner of the BDB.  

Connect the Jetson USB2 host port to BDB CON28.  You should see the following on the Jetson console:

```
[ 219.520574] usb 2-1.2.1: New USB device found, idVendor=ffff, idProduct=0002
[ 219.527647] usb 2-1.2.1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[ 219.535162] usb 2-1.2.1: Product: APBridge
[ 219.539275] usb 2-1.2.1: Manufacturer: Toshiba
[ 219.543733] usb 2-1.2.1: SerialNumber: 0
[ 219.849844] gpiochip_add: registered GPIOs 989 to 1015 on device: greybus_gpio
```

You should also see the following on the APB1 debug output:
```
[I] GB: MID-1 module inserted                                              	 
[I] GB: AP handshake complete  	
```

With the AP to APB1 link successfully established, you will be able to go on to the next section, where you will control and monitor GPIO and I2C on APB2 using commands on the Jetson console.

Sometimes the APB1 debug messages do not appear, indicating the GPIOs failed to register successfully. If this happens, or you run into other problems:  
* Unplug the USB cable between the Jetson and the BDB
* Cycle power to the BDB (Note - If you used the JTAG method for loading the bridge firmware, you'll need to repeat [those steps](#option-2-load-firmware-image-using-jtag) after cycling power to the BDB).  
* [Reboot Jetson and retry](#step-7-boot-jetson-and-load-greybus-modules).  


--------------------------------------------------------------

###Step 8. Verify GPIO and I2C]

In this section, we'll use commands from the Jetson serial console to interrogate and control GPIO 0 on APB2, using Greybus. Greybus requests travel from Jetson through APB1 through the UniPro switch to APB2, and responses take the reverse path.

####GPIO

Greybus creates an entry in /sys/class/gpio/ (gpiochip989 for example) when it receives a manifest with GPIO Protocol enabled, as with APB2.  Inspect the label attribute to find the one associated with Greybus:
```
cat /sys/class/gpio/gpiochip*/label
tegra-gpio                                                                      
as3722-gpio                                                                     
greybus_gpio       

cat /sys/class/gpio/gpiochip989/label  
greybus_gpio
```
In this example, gpiochip989 is associated with Greybus.  **It may be different on your machine.**

#####GPIO Number

The device GPIO number (in this case, 0 for APB2 GPIO 0) is used as an offset to the GPIO base number assigned by Linux. 

Check the gpiochip base value:
```
cat /sys/class/gpio/gpiochip989/base
989
```
Thus, 989 base + 0 offset (GPIO 0 on APB2) = GPIO number 989.

#####Export and Unexport a GPIO

The first thing to do is export the gpio number that you want to use.
```
echo 989 > /sys/class/gpio/export
```  

This command adds a new entry in /sys/class/gpio/, usually gpio*n* where *n* is the exported gpio number.

When you have finished using a GPIO, you can unexport it. This operation removes the entry from /sys/class/gpio.
```
echo 989 > /sys/class/gpio/unexport
```
#####Get and Set GPIO Direction

To get the direction:
```
cat /sys/class/gpio/gpio989/direction
in
```
To set the direction:
```
echo out > /sys/class/gpio/gpio989/direction
```

#####Get and Set GPIO Value

To get the value:  
```
cat /sys/class/gpio/gpio989/value  
0
```
To set the value:  
```
echo n > /sys/class/gpio/gpio989/value
```
Where *n* equals 0 or 1.

Measure the APB2 GPIO 0 output voltage on the BDB at J79 pin 1. [Picture of BDB showing J79 pin 1](images/BDB1B-Header-J79.png). A white dot indicates pin 1. This pin should read approximately 1.8V above GND when the GPIO value is 1, and a fraction of a volt when the value is 0.

####I2C

Greybus creates an entry in /sys/class/i2c-dev/ (i2c-6 for example) when it receives a manifest with the I2C Protocol enabled. 

Identify the I2C device name:
```
cat /sys/class/i2c-dev/i2c-*/name
Tegra I2C adapter                                                               
Tegra I2C adapter                                                               
Tegra I2C adapter                                                               
Tegra I2C adapter                                                               
Tegra I2C adapter                                                               
Tegra I2C adapter                                                               
Greybus i2c adapter          

cat /sys/class/i2c-dev/i2c-6/name
Greybus i2c adapter
```

In this case, the I2C device is /dev/i2c-6. **It may be different on your machine.**

#####I2C Tools

A quick way to test i2c is to use i2c-tools, which comprises i2cdetect, i2cdump, i2cget, and i2cset.  These tools are already incuded in the pre-built Android image for the Jetson TK1. 

Use i2cdetect to test i2c. 
````
i2cdetect -r 6
WARNING! This program can confuse your I2C bus, cause data loss and worse!
I will probe file /dev/i2c-6 using read byte commands.
I will probe address range 0x03-0x77.
Continue? [Y/n] Y
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
Note: 
* The “-r” argument forces i2cdetect to probe i2c devices using the read method (actually, the only method supported).
* “6” is the i2c bus number

You can also use i2cget to read a specific value, e.g.:
````
i2cget 6 0x29 3 c                                 
WARNING! This program can confuse your I2C bus, cause data loss and worse!      
I will read from device file /dev/i2c-6, chip address 0x29,
data address 0x03, using write byte/read byte.                                           
Continue? [Y/n] y                                                           
0x14
````
Notes:
* As with i2cdetect “6” is the i2c bus number
* 0x29 is the chip address
* 3 is the data address
* The last argument is the mode, which is write byte/read byte (“c”) in this example

-----------------------------------------------------------
                         
#***Congratulations! You've completed the Quick Start Guide!***