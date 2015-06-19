This page contains information on debugging AP/GP bridge and SVC firmware images using the [Segger J-Link Pro JTAG interface](http://www.segger.com/jlink-pro.html). 

####AP/GP bridge firmware boot process

Following reset, the device loads the firmware image from flash (SPIROM) 
to internal SRAM. (the firmware image contains startup logic to skip the copy to internal SRAM if the code is already running from ram.), and then jumps to it. This behavior is configured at reset 
by sampling the SPIBOOT_N pin, which is grounded. 

Firmware images can also be loaded and debugged via JTAG, by using GDB to load the image into internal SRAM while the processor is held in reset, and then releasing the processor from reset.  

In either of these cases, a valid firmware image is required in SPIROM in order
to boot the device. 

If the firmware image in flash fails to respond to JTAG, you will need to reprogram flash using a hardware programmer. For instructions on loading firmware into flash, see [this page](Flashing-images)

If you need a known-good firmware image to write to flash, in order to get JTAG debug working,  you can use nop-loop.bin, which is a firmware image that does nothing but loop forever. 

[Download the nop-loop firmware image](nop-loop.bin).  

####How to debug AP/GP bridge firmware using JTAG

If you're using multiple J-Link JTAG interfaces, you'll need to identify each by their serial number. The serial number of the J-Link Pro is on a label on the bottom.  Copy the numeric value following "S/N:".  We'll refer to this value as $JLINK_SN in the steps below.

Also if you're using multiple JTAG interfaces, you'll need to specify a unique port when launching the J-Link GDB server.  This port number can be any value 9999 or less, but it has to be unique for each JTAG session. We'll refer to this value as $JLINK_PORT in the steps below.

* Open a  terminal window and start the J-Link GDB server, specifying the serial number and port number that GDB will use when attaching to the server.  We use port number 2341 in this example.

`JLinkGDBServer -select usb=$JLINK_SN -port $JLINK_PORT`
 
You may see diagnostics of the form:  
`WARNING: Failed to read memory @ address 0xFFFFFFFF `  
`WARNING: Failed to read memory @ address 0xFFFFFFFF`  

These are harmless and it's okay to ignore them.

The following steps load the firmware image to internal RAM and run it.

* Open a second terminal window and start GDB:  `arm-none-eabi-gdb`   
* Connect to the gdbserver:  `target remote localhost:2341`  
* Reset the target device: `monitor reset`  
* Load the symbols into GDB: `file <path-to-image-elf-file>`  
* Load the binary image: `restore <path-to-image-binary-file>`  
* Set the program counter to the reset handler: `set $pc=Reset_Handler`
* Set any initial breakpoints if needed  
* Release the processor from reset: `continue`  

<!--

14. Remove power to the BDB
15.  Undo the SPI ROM-specific configuration on your debug daughter
    board.
1.  On Debug BoardRev A, remove the jumper on JP10
2.  On Debug Board Rev B, slide the switch labeled SW5 away from the SW5
    label on the board, towards the 3 and 6 labels below the switch.
15. Reapply power to the BDB. You can now proceed with developing on
    NuttX and loading code with JTAG.

###Building Code for the APBridges
1.  You should already have installed the necessary build prerequisites
    on your build machine, as referenced in the Development System
    Setup section. Note, these steps should only be needed once
    for a given build machine.
2.  Clone the NuttX repository as described in the Git repositories
    section above, and cd into the repository.
3.  Configure and build. You can either build in-tree or out-of-tree.
    Building out of tree is recommended because it avoids polluting the
    tree with build artifacts, and avoids having to run ‘make distclean’
    between each build, thus allowing you to build multiple images
    without reconfiguring, as is the case with in-tree builds. However,
    the script that builds out of tree does not yet support ‘make
    menuconfig’, so in that case you’ll need to do an in-tree build.  
1.  Building out-of-tree:
1.  Run the script build\_ara\_images.sh from the top level nuttx
    directory.
2.  Two parameters are required, board name and image name, which
    together form the path to the configuration, and are concatenated to
    form the build-name.
3.  The resulting image files are placed in
    nuttx/build/build-name/image. The raw binary image is nuttx.bin, and
    the ELF image file is nuttx.
4.  If you previously built in-tree, you’ll need to clean the tree
    before building out-of-tree. To do this, run ./clean\_tree.sh from
    the top-level nuttx directory. This removes any previously selected
    config and any leftover build products.
5.  To build the image for APBridge 1:
  ./build\_ara\_image.sh bdb apb1
6.  To build the image APBridge 2:
  ./build\_ara\_image.sh bdb apb2
2.  Building in-tree:
1.  Choose the nuttx build configuration.
  cd nuttx/tools
  ./configure.sh \<configs subdir\>
  cd ..
  where \<configs subdir\> is the subdirectory of nuttx/nuttx/configs that
contains the desired configuration files. This step copies certain files
from the configs subdir to the nuttx/nuttx dir so they are available to
the make utility.
1.   APBridge 1, use bdb/apb1
2.   APBridge 2, use bdb/apb2
2.  Build the image
        make
3.  The raw binary image is nuttx/nuttx/nuttx.bin, and the ELF image
    file is nuttx/nuttx/nuttx.
4.  Note that the repository is named nuttx, there is a nuttx
    subdirectory in the repository where ‘make’ is run, and the ELF file
    generated by a successful ‘make’ is also named nuttx.  Keep track of
    your build outputs, so that you don’t e.g., assume that an APBridge
    1 nuttx image is an APBridge 2 or SVC image).
5.  Important: If you’re going to switch between build configurations
    (APBridge 1, APBridge 2, SVC), run make distclean before cd’ing to
    tools and running the configure.sh script.
3.  Load the nuttx ELF file into memory with JTAG (see next section).

-->