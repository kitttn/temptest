####Building NuttX firmware images

This page describes how to build firmware images for the AP or GP bridges, and the SVC. There are some important differences between the AP/GP bridge firmware and the SVC firmware, however the build process is essentially the same for all.

The instructions assume you have cloned the nuttx and other related repositories into your $HOME directory,
i.e. 
```
$HOME
   +--nuttx
   +--flashrom
   +--manifesto  
  . . .
```

There are two different ways to build the firmware: 
* Select an existing configuration (specify a defconfig) 
  Recommended if you're unfamiliar with the plethora of configuration options
* Generate a custom configuration (make menuconfig)
  Use this if you need to configure certain runtime or debugging scenarios, or 
  enable specific functionality.
 
####Firmware build configuration files
As of this writing, the available defconfigs are:
./nuttx/configs/ara/bridge/es2-debug-apbridgea/defconfig  
./nuttx/configs/ara/bridge/es1-debug-apbridgea/defconfig  
./nuttx/configs/ara/bridge/es2-debug-generic/defconfig  
./nuttx/configs/ara/bridge/es1-debug-generic/defconfig  
./nuttx/configs/ara/lgd/apb1/defconfig  
./nuttx/configs/ara/lgd/apb2/defconfig  
./nuttx/configs/ara/svc/bdb2a/defconfig  
./nuttx/configs/ara/svc/bdb1b/defconfig  

####Building firmware images using a defconfig file
```
cd $HOME/nuttx
./build_ara_image.sh  ara <config-name>
```
Where \<config-name\> is the relative path to the defconfig file, against the base path of ./nuttx/configs/ara.
When the build script completes successfully, the resulting image files (binary and ELF) are placed in  
./build/\<name-of-build\>/images/  
Where "name-of-build" is the path to the defconfig, with the slashes replaced with dashes.

Example 1:
```
./build_ara_image.sh  ara svc/bdb2a
```
This builds the SVC firmware for the BDB2A
Image files are here:
```
./build/ara-svc-bdb2a/image/
```

Example 2:
```
./build_ara_image.sh  ara bridge/es2-debug-apbridgea
```
This builds a debuggable image for the AP bridge on the BDB2A (which is based on ES2 silicon).
Image files are here:
```
./build/ara-bridge-es1-debug-apbridgea/image/
```

####Building firmware images using a custom configuration file
(_w.i.p. hidden from view because it's ugly_)
<!--
#####STEP 1. a firmware build configuration.
   cd nuttx/tools
   ./configure.sh \<configs subdir\>
   cd ..
   where \<configs subdir\> is the subdirectory of nuttx/nuttx/configs/ara that
contains the desired configuration files. This step copies certain files
from the configs subdir to the nuttx/nuttx dir so they are available to
the make utility.
   Examples:                
        ara/bridge/es2-debug-apbridge - AP bridge A for BDB2
        ara/bridge/es2-debug-generic   - AP bridge 2 for BDB2
        ara/svc/bdb2a - SVC for BDB2
1.  From the nuttx/nuttx dir, run the configuration menu:
2.  make menuconfig
2.  Select System Type
3.  Select Boot Mode
4.  Select Boot from FLASH but copy to ram
5.  Save and Exit.
3.  Rebuild the image (in tree):
                make
        The resulting raw binary image is nuttx/nuttx/nuttx.bin.
        There is also an ELF image file, nuttx/nuttx/nuttx.

###Program the nuttx runtime image into SPI ROM

To flash an image that was built to load from SPI ROM

Run :

                truncate -s 2M nuttx.bin

Flashrom requires to have the same binary size as the size of the
SPIROM, this command will create holes at the end of the file in order
to get the same size (i.e. it will pad with 0 when read back but it
won’t use any physical memory on your disk).

5.  Get yourself a [SF100
    ISP](http://www.google.com/url?q=http%3A%2F%2Fwww.dediprog.com%2Fpd%2Fspi-flash-solution%2Fsf100&sa=D&sntz=1&usg=AFQjCNHXXwNK8cQ1U0qlJL8VdAxNQFqeVw) programmer
    from Dediprog.
6.  Build the flashrom utility from the git repository above.
7.  Disconnect power to the BDB.
8.  Connect your debug adapter board with a 40-pin FPC connector to the
    bridge you are attempting to program. Be careful, the connector is
    fragile.
9.  Configure your debug adapter board for use with the SPI ROM chip.

1.  On Debug Board Rev A, use a jumper to jump pins 2-3 on JP10.
2.  On Debug Board Rev B, slide the switch labeled SW5 towards the SW5
    label on the board.

10. Attach the grey SF100 ribbon cable that has an 8-pin connector to
    the mating 8-pin SPIROM programming header on the debug adapter
    board.  See the photo below for the combined result of steps 8 and
    9.  The jumper referenced in step 9 appears two columns to the left
    of the grey cable.  Caution: The SPIROM programming header is not
    keyed, so pay careful attention to the position of the red wire of
    grey ribbon cable, which denotes cd pin
    1!![IMG\_0017.JPG](images/image00.jpg)
11. Apply power to the BDB. Attach the USB cable.
12. Run (root permission may be needed, e.g. as granted via a “sudo “
    prefix):

flashrom  --programmer dediprog -w nuttx.bin

If all goes well, you should see something like:

```
flashrom v0.9.7-r1852 on Linux 3.13.0-24-generic (x86\_64)
flashrom is free software, get the source code at
http://www.flashrom.org
Calibrating delay loop... delay loop is unreliable, trying to continue
OK.
Found Winbond flash chip "W25Q16.W" (2048 kB, SPI) on dediprog.
[...bunch of blah blah here, omitted for clarity...]
Reading old flash chip contents... done.
Erasing and writing flash chip... Erase/write done.
Verifying flash... VERIFIED.
```
Note: If you attempt to reprogram the same image that’s already on the
SPIROM, you may instead see the following output:
```
Reading old flash chip contents... done.
Erasing and writing flash chip...
Warning: Chip content is identical to the requested image.
Erase/write done.
```
This is OK, but it’s a good idea to check that you’re indeed programming
the image that you had intended, rather than some previous version.

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


###Flashing the SPIROM

The AP and GP bridges run the firmware image by loading it from SPIROM 
to internal SRAM, and jumping to it. This behavior is configured at reset 
by sampling the SPIBOOT_N pin. Therefore, a valid firmware image is required in SPIROM in order
to boot. If the SPIROM does not contain a valid firmware image, you will also be unable
to debug via JTAG. 

1.  Download this image:
    [nop-loop.bin](https://drive.google.com/open?id=0B_FzW0vpoLvqX3R6UVM4cC1YN0U&authuser=1) (private link).
    This program is loaded immediately after reset by the bridges. It
    does nothing but loop forever. This will let you connect to the ARM
    core via JTAG and load your image to internal SRAM and debug. Without 
    this image in SPI, you will be unable to connect via JTAG.
2.  Execute the steps below to flash this program to
    SPIROM.

####AP/GP bridge firmware boot process overview

Following reset, the device loads the firmware image from flash (SPIROM) 
to internal SRAM, and then jumps to it. This behavior is configured at reset 
by sampling the SPIBOOT_N pin, which is grounded. 

Firmware images can also be loaded and debugged via JTAG, by using GDB to load the image into internal SRAM while the processor is halted, and then letting the processor run.  

To support both of these scenarios, the firmware image contains startup logic to skip the copy to internal SRAM if the code is already running from ram. 

In either case, a valid firmware image is required in SPIROM in order
to boot the device. BDBs are shipped with a valid firmware image in flash, but if the image
becomes corrupted, or crashes at boot time, or fails to boot, you will need to reprogram flash using a hardware programmer. For instructions on programming flash (SPIROM), see [this section]()

To build an image that can 
is loaded from SPI ROM at reset, you will need to run the configuration
menu (kconfig) and set the options as described

-->