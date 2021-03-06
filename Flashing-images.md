This page describes how to load firmware images into flash memory.  Instructions for [[building firmware images|Building-the-Code]] and for [[obtaining prebuilt binaries from Buildbot|Buildbot]] are available.

There are 4 supported scenarios:
* [Load firmware image to SPIROM for a bridge ASIC on BDB](Flashing-images#load-firmware-image-to-spirom)
* [Load firmware image to SVC internal flash on BDB/SDB with JTAG](Flashing-images#load-firmware-image-to-svc-internal-flash-on-bdb)
* [Load firmware image to SVC internal flash on SDB with STM32 bootrom](Flashing-images#load-firmware-image-to-svc-internal-flash-on-sdb-via-embedded-bootrom)
* [Load firmware image to SVC internal flash on endo](Flashing-images#load-firmware-image-to-svc-on-endo)


<br>
####Load firmware image to SPIROM

#####Bridge ASIC debug board connectors and reset switches

The following table shows which connectors (on the BDB) to connect the debug board FPC cable to. You'll need it in later steps.

Bridge ASIC | BDB Debug Connector | Reset Switch 
------------|---------------------|-------------
APB1        | CON17               | SW8
APB2        | CON19               | SW9
APB3        | CON16               | SW7
GPB1        | CON14               | SW5
GPB2        | CON15               | SW6

#####Hardware Setup for BDB

The final debug board configuration should look like this. Click the image for a larger version.

<a href="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/Debug-Adapter-Board-Rev-B-Dediprog-Setup.jpg"><img src="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/Debug-Adapter-Board-Rev-B-Dediprog-Setup.jpg" width=640/></a>

1. REMOVE POWER FROM BDB.
2. Connect FPC from BDB debug board connector CON9 to the corresponding bridge ASIC connector on the BDB; the table below lists which BDB connector corresponds to each bridge. The FPC has labels 'side Debug Board' and 'BDB'; you must connect the boards according to those labels.
3. Verify jumper is installed on debug board JP15 pins 1-2
4. Make sure all bridge ASIC reset switches (see table above) are *TOWARDS* pin 1.
5. Connect the Dediprog SF100 8-pin IDC to debug board CON1 "SPI ROM" header. Note, this header is not keyed. Visually ensure that the red wire of the cable aligns with the pin 1 dot and the "M" of "ROM" on the debug board silkscreen.
6. Optional: Connect USB cable to debug board CON6 for bridge serial debug output. Run a terminal program at 115200 baud, 8N1.
7. Apply power to the BDB. 

#####Hardware Setup for SDB

1. USE POWER SWITCH TO REMOVE POWER FROM SDB.
2. Make sure all bridge ASIC reset switches are *TOWARDS* pin 3.

    <a href="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/SDB-Bridge-Reset.jpg"><img src="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/SDB-Bridge-Reset.jpg" width=640/></a>

3. Connect the Dediprog SF100 8-in IDC to the CON1 "SPI ROM" header.  Note, this header is not keyed.  Visually ensure that the red wire of the cable aligns with the white pin-1 dot.  Otherwise, your SF-100 can and will be damaged.

    <a href="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/SDB-APB1-SPI-ROM-OK.jpg"><img src="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/SDB-APB1-SPI-ROM-OK.jpg" width=640/></a>
    
    <a href="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/SDB-APB2-SPI-ROM-OK.jpg"><img src="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/SDB-APB2-SPI-ROM-OK.jpg" width=640/></a>

6. Optional: Connect USB cable to the Debug USB port for bridge serial debug output. Run a terminal program at 115200 baud, 8N1.
7. Apply power to the SDB via the power-on switch. 

#####Software Steps

1. The Flashrom utility expects the binary image file to be the same size as the flash device. Run the truncate utility to pad the image to 2M:

   `truncate -s 2M <path-to-nuttx-binary-image>.bin` 
  
2. Run the flashrom utility:

   `flashrom  --programmer dediprog -w <path-to-nuttx-binary-image>.bin`

   If all goes well, you should see something like the following:

   ```
   flashrom v0.9.7-r1852 on Linux 3.13.0-24-generic (x86_64)
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

   *Note*: If you attempt to reprogram the same image twice, you may see the following output instead:

   ```
   Reading old flash chip contents... done.
   Erasing and writing flash chip...
   Warning: Chip content is identical to the requested image.
   Erase/write done.
   ```

3. At this point, you can unplug the SF-100. However, leaving it connected will not cause any harm, as the SF-100 lines are high impedance except during programming.

4. Take all bridges out of reset by moving their reset switches *AWAY* from pin 1.

####Load firmware image to SVC internal flash on BDB/SDB with JTAG
STM32 internal flash is written via the JTAG interface, 
using gdb commands.

#####Hardware Setup

The final debug board configuration should look like this. Click the image for a larger version.

<a href="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/Debug-Adapter-Board-Rev-B-Setup.png"><img src="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/Debug-Adapter-Board-Rev-B-Setup.png" width=640/></a>

1. REMOVE POWER FROM BDB
2. Connect JTAG interface to debug board CON3 and host USB
3. Connect FPC from debug board CON9 to BDB CON18. Observe FPC labeling 'side Debug
Board' and 'BDB'. 
4. Verify debug board SW5 is positioned *away* from the SW5 label, to hold the bridge ARM processor in reset while programming.  
5. Verify jumper is installed at debug board JP15 pins 1-2  
6. Optional: USB cable connected to BDB CON12 for debug serial output.  
7. Apply power to BDB

#####Software Steps  
1. Open a terminal window and start the JLink gdbserver, specifying the SVC device:

   `JLinkGDBServer -device STM32F417IG`

2. Open another terminal window and start GDB, passing the nuttx ELF image you want to upload:

   `arm-none-eabi-gdb <path-to-svc-nuttx>`.

   For example, if you used "build_ara_image.sh ara svc/bdb2a" from the NuttX top level directory to build an SVC image for BDB2 (either BDB2A or BDB2B, **note the confusing name**), the BDB2 SVC image is build/ara-svc-bdb2a/image/nuttx.

3. Connect GDB to gdbserver:

   `target remote localhost:2331`

4. Reset target:

   `monitor reset`

5. Load the ELF image into flash memory. It will be stored there permanently (until you next reprogram it).

   `load`

   *Note*: to load the SVC binary image (nuttx.bin), use the 'restore' command instead:

   `restore nuttx.bin binary 0x08000000`

6. Run the ELF image.

   `continue`

   You can use control-C to break execution, set breakpoints, etc. Another `monitor reset` / `continue` will restart the image from its reset vector.

7. You can now disconnect the debug board FPC from the BDB. The SVC firmware image you just flashed will run whenever you power cycle the BDB.

####Load firmware image to SVC internal flash on SDB via embedded bootrom
SDB supports programming of the SVC internal flash over UART.
This is accomplished by placing the STM32 into it's on-chip embedded bootloader mode and then using the stm32ld utility to load firmware into the SVC.

#####Hardware Setup

The final debug board configuration should look like this. Click the image for a larger version.

<a href="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/SDB-BOOT-TYPE-SYSTEM-MEM.png"><img src="https://raw.githubusercontent.com/wiki/projectara/Firmware-wiki/images/SDB-BOOT-TYPE-SYSTEM-MEM.png" width=640/></a>

1. REMOVE POWER FROM SDB
2. Verify jumper is installed at BOOT0 pins 1-2
3. Verify jumper is installed at BOOT1 pins 2-3
4. Connect a USB cable to SDB Debug USB port
5. Apply power to SDB

#####Software Steps
1. Open a terminal window and determine which tty port is the SVC Port:

  `dmesg | grep tty`

   usb 1-1.2.2: FTDI USB Serial Device converter now attached to ttyUSB0

   usb 1-1.2.2: FTDI USB Serial Device converter now attached to ttyUSB1

   usb 1-1.2.2: FTDI USB Serial Device converter now attached to ttyUSB2

   usb 1-1.2.2: FTDI USB Serial Device converter now attached to ttyUSB3

   usb 1-1.2.3: FTDI USB Serial Device converter now attached to ttyUSB4

   usb 1-1.2.3: FTDI USB Serial Device converter now attached to ttyUSB5

   usb 1-1.2.3: FTDI USB Serial Device converter now attached to ttyUSB6

   usb 1-1.2.3: FTDI USB Serial Device converter now attached to ttyUSB7

   The USB debug hardware on SDB provides eight different UART-to-USB interfaces with the
   SVC USART1 wired into the first slot on the FT4232H, which means that 'ttyUSB0' above
   is the correct tty device for the next step. Note if you have other USB serial devices
   already connected and enumerated with your host system the ttyUSBX number will likely
   be different.

2. Run the stm32ld utility to upload the SVC binary image :

   `stm32ld <port-no> <baud-rate> <path-to-svc-nuttx.bin>`.

   Minimum BAUD rate is 1200 BAUD
   Maximum BAUD rate is 115200 BAUD
   Port-no should be the lowest new ttyUSBx identified after connecting the USB to SDB's USB debug port

   Example:
   $ stm32ld /dev/ttyUSB0 115200 build/ara-svc-sdb/image/nuttx.bin
   Found bootloader version: 3.1
   Chip ID: 0413
   Cleared write protection.
   Starting Extended Erase of FLASH memory. This will take some time ... Please be patient ...
   Extended Erased FLASH memory.
   Programming flash ... 10% 20% 30% 40% 50% 60% 70% 80% 90% 100%
   Done.

3. You can now power down the SDB and revert jumper positions

   Power down SDB
   Verify jumper is installed at BOOT0 pins 2-3
   Verify jumper is installed at BOOT1 pins 1-2

####Load firmware image to SVC on endo

#####Hardware Setup
  
1. REMOVE POWER TO THE ENDO. Power is supplied via USB charger or battery module.  
2. Connect JTAG interface to debug board CON2 (remove masking tape) 
3. Connect FPC from debug board CON9 to Endo (CON??). Observe FPC labeling 'side Debug
Board' and 'BDB'. 'BDB' end goes to Endo.
4. Verify jumper installed at debug board JP14 pins 1-2  
5. Optional: USB cable connected to debug board CON6 for SVC serial console.  
6. Apply power to Endo.
 
#####Software Steps  
1. Open a terminal window and start the JLink gdbserver, specifying the SVC device:

   `JLinkGDBServer -device STM32F417IG`

2. Open another terminal window and start GDB, passing the nuttx ELF image you want to upload:

   `arm-none-eabi-gdb <path-to-svc-nuttx>`.

   An [SVC image binary](http://ara-buildbot.leaflabs.com:8011/images/endo-svc.b4b29bdfc51281dbb30172d8625a2cb2deb51047/nuttx) you can use is available on the Google I/O demo buildbot.

3. Connect GDB to gdbserver:

   `target remote localhost:2331`

4. Reset target:

   `monitor reset`

5. Load the ELF image into flash memory:

   `load`.

   *Note*: to load the SVC binary image (nuttx.bin), use the 'restore' command instead:

   `restore nuttx.bin binary 0x08000000`

6. Run the ELF image.

   `continue`

   You can use control-C to break execution, set breakpoints, etc. Another `monitor reset` / `continue` will restart the image from its reset vector.

###Additional Info

**Debug Adapter Board CON1 SPI ROM Header Pinout**  

Pin|Signal|Signal|Pin
---|------|------|---
1|3V in|GND  |2
3|CS   |CLK  |4
5|MISO/DQ1|MOSI/DQ0 |6
7|UART TX|UART RX |8

Notes:  
* CAUTION: pins 2-8 are at 1.8V logic levels!
* Pins 1, 7, and 8 are not required for SPI programming and should be left unconnected.
