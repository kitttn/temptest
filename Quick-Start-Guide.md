Hello, and welcome! 

This guide is intended for module or kernel developers getting started for the first time with the Ara MDK and development hardware. This provides a step-by-step guide to setting up your hardware and computer, and then building and running a set of firmware images that lets you verify everything is working as expected. If you're more experienced, feel free to skip this guide and work your way through the other wiki pages listed in the box at the right, in the order they are listed.

Ready? Here we go!

This guide is broken into numbered sections, and each section has a numbered list of steps. Complete each section in order, and complete the steps in order. Within each section, there are links to other pages that contain additional information. When you are finished with those pages, you can either click the back button in your browser to come back to where you left off, or click the 'Quick Start Guide' link in the page menu on the right.

###SECTION 1. Set up your development computer
1. Make sure you are running Linux 14.04. You can use a VM or a physical machine.
2. Follow the instructions [on this page](Software-Setup) to set up the software
 
###SECTION 2. Set up your development hardware
1. Make sure you have the following:
  1. Big Development Board version 2A (BDB2A) kit
  2. Jetson TK1 DevKit
  3. 3 or more USB micro B cables
  4. USB hub
  5. 2 of [these fancy J-Link Pro JTAG interfaces](http://www.segger.com/jlink-pro.html)
2. Follow the instructions [on this page](Hardware-Overview) to set up the hardware
 
###SECTION 3. Build the firmware images
1. On your development machine:
  1. `cd $HOME/nuttx`
  2. `./build_ara_image.sh ara bridge/es2-debug-apbridgea`
  3. `./build_ara_image.sh ara bridge/es2-debug-generic`
```
Where \<config-name\> is the relative path to the defconfig file, assuming the base path of 
```
./nuttx/configs/ara  
```

When the build script completes successfully, the resulting image files (binary and ELF) are placed in  
```  
./build/<name-of-build>/images/  
```
Where \<name-of-build\> is the defconfig relative path, with the slashes replaced with dashes.
###SECTION 4. Load firmware images into the hardware
###SECTION 5. Verify operation

 
[Hardware Setup](Hardware-Setup)  
 
&nbsp;&nbsp;&nbsp;&nbsp;[Build default config](Build-default-config)  
&nbsp;&nbsp;&nbsp;&nbsp;[Build custom config](Build-custom-config)  
[Flashing images](Flashing-images)  
[Debugging](Debugging)  
[Running the Examples](Running-the-examples)  
[Hardware Gotchas](Hardware-Gotchas) 
(todo)



