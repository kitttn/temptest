This page describes how to load firmware images into flash memory.  

There are 3 supported scenarios:
* [Load firmware image to SPIROM for a bridge ASIC on BDB](Flashing-images#load-firmware-image-to-spirom)
* [Load firmware image to SVC internal flash on BDB](Flashing-images#load-firmware-image-to-svc-internal-flash-on-bdb)
* [Load firmware image to SVC internal flash on endo](Flashing-images#load-firmware-image-to-svc-on-endo)


<br>
####Load firmware image to SPIROM

#####Hardware Setup
1. REMOVE POWER FROM BDB
2. Connect the JTAG interface to debug board CON3 and host USB
3. Connect FPC from debug board CON9 to BDB. Observe FPC labeling 'side Debug
Board' and 'BDB'. The table below lists which BDB connector is associate with each device.
4. Verify debug board SW5 is positioned *toward* the SW5 label  
5. Verify jumper is installed on debug board JP15 pins 1-2
6. Connect the Dediprog SF100 8-pin IDC to debug board CON1 "SPI ROM" header. Note, this header is not keyed. Visually ensure that the red wire of the cable aligns with the pin 1 dot and the "M" of "ROM" on the debug board silkscreen.
7. Optional: Connect USB cable to debug board CON6 for bridge serial debug output. Run terminal prog at 115200-n-8-1  
8. Apply power to the BDB. 


Bridge ASIC | BDB Debug Connector | Reset Switch 
------------|---------------------|-------------
AP Bridge 1 | CON17  | SW8
AP Bridge 2 | CON19  | SW9
AP Bridge 3 | CON16  | SW7
GP Bridge 1 | CON14  | SW5
GP Bridge 2 | CON15  | SW6


#####Software Steps

1. The Flashrom utility expects the binary image file to be the same size as the flash device. The truncate utility pads the binary image file to match the device size.
To run the truncate utility: `truncate -s 2M <path-to-binary-image-file>`   
2. Run the flashrom utility: `flashrom  --programmer dediprog -w <path-to-binary-image-file>`  
If all goes well, you should see something like the following:
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
Note: If you attempt to reprogram the same image twice, you may see the following output instead:
```
Reading old flash chip contents... done.
Erasing and writing flash chip...
Warning: Chip content is identical to the requested image.
Erase/write done.
```
This is OK, but it’s a good idea to check that you’re indeed programming
the image that you had intended, rather than some previous version.

<br>
####Load firmware image to SVC internal flash on BDB
STM32 internal flash is written via the JTAG interface, 
using gdb commands.

#####Hardware Setup
1. REMOVE POWER FROM BDB
2. Connect JTAG interface to debug board CON3 and host USB
3. Connect FPC from debug board CON9 to BDB CON18. Observe FPC labeling 'side Debug
Board' and 'BDB'. 
4. Verify debug board SW5 is positioned *away* from the SW5 label, to hold the bridge ARM processor in reset while programming.  
5. Verify jumper is installed at debug board JP15 pins 1-2  
6. Optional: USB cable connected to BDB CON12 for debug serial output.  
7. Apply power to BDB

#####Software Steps  
1. Open a terminal window and start the JLink gdbserver, specifying the SVC device: `JLinkGDBServer -device STM32F417IG`
2.  Open another terminal window and start GDB, passing the nuttx ELF image you want to upload: `arm-none-eabi-gdb nuttx`
3. Connect GDB to gdbserver: `target remote localhost:2331`
4. Reset target: `monitor reset`
5. Load the ELF image into flash memory: `load`. Note: to load the SVC binary image, use the 'restore' command instead: `restore nuttx.bin binary 0x08000000`

<br>
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
1. Open a terminal window and start the JLink gdbserver, specifying the SVC device: `JLinkGDBServer -device STM32F417IG`
2.  Open another terminal window and start GDB, passing the nuttx ELF image you want to upload: `arm-none-eabi-gdb nuttx`
3. Connect GDB to gdbserver: `target remote localhost:2331`
4. Reset target: `monitor reset`
5. Load the ELF image into flash memory: `load`. Note: to load the SVC binary image, use the 'restore' command instead:
   `restore nuttx.bin binary 0x08000000`


###Additional Info

**Debug Adapter Board CON1 SPI ROM Header Pinout**  

Pin|Signal|Signal|Pin
---|------|------|---
1|3V in|GND  |2
3|CS   |CLK  |4
5|MISO/DQ1|MOSI/DQ0 |6
7|UART TX|UART RX |8

Notes:  
* UART signals are 1.8V logic levels.
