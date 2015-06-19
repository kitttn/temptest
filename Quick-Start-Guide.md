Hello, and welcome! 

This guide is intended for module or kernel developers getting started for the first time with the Ara MDK and development hardware. This provides a step-by-step guide to setting up your hardware and computer, and then building and running a set of firmware images for the "GPIO and I2C demo". Once you've completed this guide, you can be confident that everything is working as expected, and you'll have familiarized yourself with the tools and techniques. 

Note: If you're more experienced, feel free to skip this guide and work your way through the other wiki pages listed in the box at the right, in the order they are listed.

This guide is broken into numbered sections, and each section has a numbered list of steps. Complete each section in order, and complete the steps in order. Within each section, there are links to other pages that contain additional information. When you are finished with those pages, you can either click the back button in your browser to come back to where you left off, or click the 'Quick Start Guide' link in the page menu on the right.

[SECTION 1. Set up your development computer](#section-1-set-up-your-development-computer)  
[SECTION 2. Set up your development hardware](#section-2-set-up-your-development-hardware)  
[SECTION 3. Build the firmware images](#section-3-build-the-firmware-images)  
[SECTION 4. Load the firmware images](#section-4-load-the-firmware-images)  
[SECTION 5. Configure the AP](#section-4-configure-the-ap)  
[SECTION 6. Verify operation](#section-5-verify-operation)  

Ready? Here we go!

<br>
###SECTION 1. Set up your development computer

1. Make sure you are running Linux 14.04. You can use a VM or a physical machine.
2. Follow the instructions [on this page](Software-Setup) to set up the software
 
<br>
###SECTION 2. Set up your development hardware

1. Make sure you have the following:
  1. Big Development Board version 2A (BDB2A) kit
  2. Jetson TK1 DevKit
  3. 3 or more USB micro B cables
  4. USB hub
  5. 2 (two!) [J-Link Pro JTAG interfaces](http://www.segger.com/jlink-pro.html)
2. Follow the instructions [on this page](Hardware-Overview) to set up the hardware

<br>
###SECTION 3. Build the firmware images

On your development machine:
1. Build the 
  * `cd $HOME/nuttx`
  * `./build_ara_image.sh ara bridge/es2-debug-apbridgea`
  * `./build_ara_image.sh ara bridge/es2-debug-generic`
2.  

When the build script completes successfully, the resulting image files (binary and ELF) are placed in  
```  
./build/<name-of-build>/images/  
```
Where \<name-of-build\> is the defconfig relative path, with the slashes replaced with dashes.

<br>
###SECTION 4. Load the firmware images

<br>
###SECTION 5. Configure the AP

<br>
###SECTION 6. Verify operation

 
[Hardware Setup](Hardware-Setup)  
 
&nbsp;&nbsp;&nbsp;&nbsp;[Build default config](Build-default-config)  
&nbsp;&nbsp;&nbsp;&nbsp;[Build custom config](Build-custom-config)  
[Flashing images](Flashing-images)  
[Debugging](Debugging)  
[Running the Examples](Running-the-examples)  
[Hardware Gotchas](Hardware-Gotchas) 
(todo)



