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
2. Follow the instructions [on this page](Hardware-Setup)

<br>
###SECTION 3. Build the firmware images
Follow these steps to build the firmware images for the 
APB1, APB2, and SVC microcontrollers on the BDB.

1. `cd $HOME/nuttx`
2. `./build_ara_image.sh ara bridge/es2-debug-apbridgea`
3. `./build_ara_image.sh ara bridge/es2-debug-generic`
4. `./build_ara_image.sh ara svc/bdb2a`  

<br>
###SECTION 4. Load the firmware images

For each of the firmware images listed below, follow the link
to view the procedure to flash the firmware image to the 
device. Note the procedure is different for the SVC!

1. ./nuttx/build/ara-bridge-es2-debug-apbridgea/images/nuttx.bin  
   [how to flash](Flashing-images#load-firmware-image-to-spirom)
2. ./nuttx/build/ara-bridge-es2-debug-generic/images/nuttx.bin  
   [how to flash](Flashing-images#load-firmware-image-to-spirom)
3. ./nuttx/build/ara-svc-bdb2a/images/nuttx.bin  
   [how to flash](Flashing-images#load-firmware-image-to-svc-internal-flash-on-bdb)

<br>
###SECTION 5. Configure the AP
(todo)

<br>
###SECTION 6. Verify operation

A user at the Jetson TK1 serial console is able to remotely interrogate and control GPIO 0 on APB2, using Greybus.  Greybus requests travel from Jetson through APB1 through the switch to APB2, and responses take the reverse path.

(todo)




