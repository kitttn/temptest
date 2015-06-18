This page describes how to load firmware images into flash memory.  

There are 3 supported scenarios:
* SPIROM for a bridge ASIC
* internal flash for SVC on BDB
* internal flash for SVC on endo


####Load firmware image to SPIROM

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
1.  Open a terminal window and start the JLink gdbserver, specify the SVC device.
                JLinkGDBServer -device STM32F417IG
2.  Open another terminal window and start GDB, passing the
    “nuttx” ELF image you want to upload:
                arm-none-eabi-gdb nuttx
3.  Connect GDB to gdbserver
                target remote localhost:2331
4.  Reset target
                monitor reset
5.  Load the ELF image into flash memory
                load  
    Note: to load the SVC binary image, use the following instead of the ‘load’ command:
                restore nuttx.bin binary 0x08000000


####Load firmware image to SVC on endo

Hardware setup:
- provide power to the Endo via USB charger or battery module  
- JTAG connected to debug board CON2
- debug board JP14 pins 1-2 jumper installed
- Optional: USB cable connected to debug board CON6 (SVC console).  

The software steps are the same as for BDB.

####Next Steps

Now that we know how to load images to flash, we can do some [Debugging](Debugging).
