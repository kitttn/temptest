This page describes how to load firmware images into flash memory.  

There are 3 supported scenarios:
* [SPIROM for a bridge ASIC](Flashing-images#load-firmware-image-to-spirom)
* [SVC internal flash on BDB](Flashing-images#load-firmware-image-to-svc-internal-flash-on-bdb)
* [SVC internal flash on endo](Flashing-images#load-firmware-image-to-svc-on-endo)


####Load firmware image to SPIROM

#####Hardware Setup
1. REMOVE POWER FROM BDB
2. Connect JTAG interface to debug board CON3  
3. Connect FPC from debug board CON9 to BDB. Observe FPC labeling 'side Debug
Board' and 'BDB'. The following table shows which BDB connector goes with which chip.

Bridge ASIC | BDB Debug Connector  
-------------|------  
AP Bridge 1 | CON17  
AP Bridge 2 | CON19  
AP Bridge 3 | CON16  
GP Bridge 1 | CON14  
GP Bridge 2 | CON15

4. Verify debug board SW5 is positioned *toward* the SW5 label  
5. Verify jumper installed at debug board JP15 pins 1-2
6. Connect the Dediprog SF100 8-pin IDC to debug board CON1 "SPI ROM" header. Note, this header is not keyed. Visually ensure that the red wire of the cable aligns with the pin 1 dot and the "M" of "ROM" on the debug board silkscreen.
7. Optional: USB cable connected to BDB CON12 (SVC console).  
8. Apply power to the BDB. 

#####Software Steps

1. Run the truncate utility on the nuttx.bin file: `truncate -s 2M nuttx.bin`   
The Flashrom utility expects nuttx.bin to be the same size as the SPIROM device that it is writing to. To ensure this, we run the truncate utility on the nuttx binary image file. This creates holes at the end of the file in order to get the same size (i.e. it will pad with 0 when read back but it won’t use any physical memory on your disk).
2. Run the flashrom utility: `flashrom  --programmer dediprog -w nuttx.bin`  
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



####Load firmware image to SVC internal flash on BDB
STM32 internal flash is written via the JTAG interface, 
using gdb commands.

Hardware setup:
- JTAG connected to debug board CON3  
- FPC connected from debug board CON9 to BDB CON18. Observe FPC labeling 'side Debug
Board' and 'BDB' !!  
- debug board SW5 positioned *away* from the SW5 label  
- debug board JP15 pins 1-2 jumper installed  
- Optional: USB cable connected to BDB CON12 (SVC console).  

Software:  
1. Open a terminal window and start the JLink gdbserver, specifying the SVC device: `JLinkGDBServer -device STM32F417IG`
2.  Open another terminal window and start GDB, passing the nuttx ELF image you want to upload: `arm-none-eabi-gdb nuttx`
3. Connect GDB to gdbserver: `target remote localhost:2331`
4.  Reset target: `monitor reset`
5.  Load the ELF image into flash memory: `load`    
    Note: to load the SVC binary image, use the following instead of the ‘load’ command:
   `restore nuttx.bin binary 0x08000000`


####Load firmware image to SVC on endo

Hardware setup:  
- provide power to the Endo via USB charger or battery module  
- JTAG connected to debug board CON2  
- debug board JP14 pins 1-2 jumper installed  
- Optional: USB cable connected to debug board CON6 (SVC console).  

Software:  
Software steps are the same as for [BDB](Flashing-images#load-firmware-image-to-svc-internal-flash-on-bdb).

