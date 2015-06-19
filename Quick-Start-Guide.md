Hello, and welcome! 

This page is intended for developers getting started for the first time with the Ara MDK and development hardware. This provides a step-by-step guide to setting up your hardware and computer, and building and running the "GPIO and I2C demo". 

Once you've completed this guide, you can be confident that everything is working as expected, and you'll have familiarized yourself with the tools and techniques. 

If you're more experienced, feel free to skip this guide and work your way through the other wiki pages listed in the box at the right, in the order they are listed.

Complete each of the following sections in order. Within each section, there are links to other pages that contain additional information. When you are finished with those pages, you can either click the back button in your browser, or click the 'Quick Start Guide' link in the page menu on the right.

[SECTION 1. Set up your development computer](#section-1-set-up-your-development-computer)  
[SECTION 2. Set up your development hardware](#section-2-set-up-your-development-hardware)  
[SECTION 3. Build the firmware images](#section-3-build-the-firmware-images)  
[SECTION 4. Load the firmware images](#section-4-load-the-firmware-images)  
[SECTION 5. Configure the AP](#section-5-configure-the-ap)  
[SECTION 6. Verify operation](#section-6-verify-operation)  

Ready? Here we go!

<br>
###SECTION 1. Set up your development computer

1. Make sure you are running Linux 14.04. You can use a VM or a physical machine.
2. Follow the instructions [on this page](Software-Setup)
 
<br>
###SECTION 2. Set up your development hardware

1. Make sure you have the following:
  * Big Development Board version 2A (BDB2A) kit
  * Jetson TK1 DevKit (may be included with BDB2A kit)
  * 3 or more USB micro B cables
  * USB hub
  * 2 (two!) [J-Link Pro JTAG interfaces](http://www.segger.com/jlink-pro.html)
  * Dediprog SF100 Programmer
2. Follow the instructions [on this page](Hardware-Setup)

<br>
###SECTION 3. Build the firmware images
Follow these steps to build the firmware images for the 
APB1 and APB2 microcontrollers on the BDB.  There is no need
to build or flash the SVC (Supervisory Controller) firmware because the 
SVC on your BDB has been preloaded with firmware.

1. `cd $HOME/nuttx`
2. `./build_ara_image.sh ara bridge/es2-debug-apbridgea`
3. `./build_ara_image.sh ara bridge/es2-debug-generic`

<br>
###SECTION 4. Load the firmware images

For each of the firmware images listed below, follow 
[this procedure](Flashing-images#load-firmware-image-to-spirom) to 
flash the firmware image to the device. 

1. `$HOME/nuttx/nuttx/build/ara-bridge-es2-debug-apbridgea/images/nuttx.bin`  
2. `$HOME/nuttx/nuttx/build/ara-bridge-es2-debug-generic/images/nuttx.bin`

<br>
###SECTION 5. Configure the AP
In this section, you will prepare the Jetson TK1 Application Processor (AP), and build the [Greybus kernel modules](https://github.com/projectara/greybus) (greybus, gb-phy, and gb-es1) for the Linux kernel running on that AP.

Project Ara provides a [pre-built Android image](https://github.com/projectara/Android-wiki/wiki/Build-and-Boot-Instructions-for-Jetson-reference-platform) for the Jetson TK1 development board.  With this image flashed to your Jetson TK1, ready-to-use Greybus kernel modules are available in the /lib/modules directory.

**DO NOT CONNECT THE BDB TO THE AP VIA USB UNTIL INSTRUCTED.**

Use May 15-era Jetson build.
Kernel modules to load on Jetson: 
* greybus.ko
* gb-phy.ko
* gb-es1.ko

1. Have everything connected (JTAG to APB1, JTAG to APB2) but **DO NOT CONNECT THE AP TO THE BDB VIA USB**
2. Reboot the AP. If the AP's console is unresponsive, reset the AP to force a reboot.
3. On the Jetson serial console Load the required kernel modules on the Jetson:  
```
su  
cd /lib/modules`
insmod greybus.ko
insmod gb-phy.ko
insmod gb-es1.ko
```
 
9. Connect the Jetson main USB port to the BDB. The main USB port is circled in green :
![Jetson TK1 Connectors](http://releases-ara-mdk.linaro.org/static/wiki-images/Ports.jpg)

If you run into problems, disconnect the AP from the BDB, and return to step 2.  A fallback if that fails to bring up the system is to remove power from the BDB and reapply power.  This fallback typically requires that the J-Link GDB servers and the GDB clients be stopped and restarted as well.

<br>
###SECTION 6. Verify operation

A user at the Jetson TK1 serial console is able to remotely interrogate and control GPIO 0 on APB2, using Greybus.  Greybus requests travel from Jetson through APB1 through the switch to APB2, and responses take the reverse path.

GPIO 0 on APB2 is registered on Jetson as 989.

APB2 GPIOs sometimes fail to register successfully on Jetson.  When this happens, the message "GB: AP handshake complete" does not appear on the APB1 console. 

####Verify the USB Connection is Established

The APB1 firmware will output the following on its serial console when a connection is established with the AP and a successful Greybus handshake has occurred:
```
[I] GB: MID-1 module inserted                                              	 
[I] GB: AP handshake complete  	
```

Now that the AP to APB1 link is successfully established, you will be able to control and monitor GPIO and I2C on APB2 from the AP.

####GPIO


Greybus creates an entry in /sys/class/gpio/ (gpiochip989 for example) when it receives a manifest with GPIO Protocol enabled.  If there are several gpiochips, you can use the label attribute to find the one associated with Greybus:

    $ cat /sys/class/gpio/gpiochip*/label
    tegra-gpio                                                                      
    as3722-gpio                                                                     
    greybus_gpio       
    $ cat /sys/class/gpio/gpiochip989/label  
    greybus_gpio

In this example, gpiochip989 is associated with Greybus.  **It may be different on your machine.**


#####GPIO Number
The GPIO number assigned by Linux will differ from the APBridge 2 GPIO number.
You have to apply an offset from the APBridge 2 GPIO number to get the Linux one.
To get the offset, run:

    $ cat /sys/class/gpio/gpiochip989/base  
    989

For example, to control the GPIO 0 on APBridge 2, you have to control the GPIO 989 on Linux.

#####Export and Unexport a GPIO
The first thing to do is export the gpio that you want to use.

    $ echo 989 > /sys/class/gpio/export  

This command will add a new entry in /sys/class/gpio/ (usually gpion where n is the exported gpio number).

When you have finished using a GPIO, you can unexport it. This operation will remove the gpion entry from /sys/class/gpio.
    
    $ echo 989 > /sys/class/gpio/unexport

#####Get and Set GPIO Direction
To get the direction, just run:

    $ cat /sys/class/gpio/gpio989/direction  
    in

To change the direction:

    $ echo out > /sys/class/gpio/gpio989/direction


#####Get and Set GPIO Value
To get the value, execute:

    $ cat /sys/class/gpio/gpio989/value  
    0

To change the value:

    $ echo 1 > /sys/class/gpio/gpio989/value
Note: On the BDB, APB2 GPIO 0 (mapped on Linux as 989) is available on pin 1 (look for a white dot on the PCB) of a header labeled J79:
![BDB Pin 1 of Header J79](images/BDB1B-Header-J79.png)
The pin should read approximately 1.8V to ground when the GPIO value is 1, and a fraction of a volt when the value is 0.

    $ echo 0 > /sys/class/gpio/gpio989/value

####I2C

Greybus creates an entry in /sys/class/i2c-dev/ (i2c-6 for example) when it receives a manifest with the I2C Protocol enabled. 

#####Find the I2C Adapter
Here is some example output showing how to find the I2C adapter:

    # cat /sys/class/i2c-dev/i2c-*/name
    Tegra I2C adapter                                                               
    Tegra I2C adapter                                                               
    Tegra I2C adapter                                                               
    Tegra I2C adapter                                                               
    Tegra I2C adapter                                                               
    Tegra I2C adapter                                                               
    Greybus i2c adapter          
    # cat /sys/class/i2c-dev/i2c-6/name
    Greybus i2c adapter

So in this case, the I2C device is /dev/i2c-6. **It may be different on your machine.**

#####I2C Tools

A quick way to test i2c is to use i2c-tools, which comprises i2cdetect, i2cdump, i2cget, and i2cset.  These tools are already incuded in the [pre-built Android image for the Jetson TK1](https://github.com/projectara/Android-wiki/wiki/Build-and-Boot-Instructions-for-Jetson-reference-platform).  If you were using a Debian-derived Linux distribution on some other AP and found that the tools weren't present, you could install the i2c-tools package as follows:

    $ sudo apt-get install i2c-tools  

Next, use i2cdetect to test i2c. 
````
$ i2cdetect -r 6
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
* “6” is the i2c bus number, which could be different on your AP (e.g. “1” rather than “6”).
* On some APs other than the Jetson TK1, you may have to load the i2c-dev kernel module before using the i2c-tools.

You can also use i2cget to read a specfic value, e.g.:
````
$ i2cget 6 0x29 3 c                                 
WARNING! This program can confuse your I2C bus, cause data loss and worse!      
I will read from device file /dev/i2c-6, chip address 0x29,
data address 0x03, using write byte/read byte.                                           
Continue? [Y/n] y                                                           
0x14
````
Notes:
* As with i2cdetect “6” is the i2c bus number, which could be different on your AP (e.g. “1”)
* 0x29 is the chip address
* 3 is the data address
* The last argument is the mode, which is write byte/read byte (“c”) in this example
                         




