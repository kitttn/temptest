This page describes how to load firmware images into external flash memory (bridge's SPIROM) or internal flash memory (SVC).

####Load firmware image into external flash (bridge's SPIROM)

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

####Load firmware image to internal flash on SVC

The JTAG debugger can reflash the code in the STM32 internal flash. For
this to work it is needed to specify the chipset target to the
gdbserver.

1.  Open a separate terminal window and start the JLink gdbserver
                JLinkGDBServer -device STM32F417IG
2.  Open another terminal window and start GDB, pointing it at the
    “nuttx” ELF image you want to upload:
                arm-none-eabi-gdb nuttx
3.  Connect to your gdbserver (this and the steps that follow are
    commands issued to GDB)
                target remote localhost:2331
4.  Reset your target
                monitor reset
5.  Load the ELF image into flash memory
                load
Note: if only the SVC binary image is provided, use this instead of the
‘load’ command:
                restore nuttx.bin binary 0x08000000
