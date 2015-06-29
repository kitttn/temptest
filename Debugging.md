This page contains information on debugging AP/GP bridge and SVC firmware images using the [Segger J-Link Pro JTAG interface](http://www.segger.com/jlink-pro.html). 

###How To Use Multiple J-Link Dongles

If you're using multiple J-Link JTAG dongles (e.g. to debug two bridges at once, or a bridge and the SVC), you'll need to identify each by their serial number. The serial number of the J-Link Pro is on a label on the bottom.  Copy the numeric value following "S/N:".  We'll refer to this value as **$JLINK_SN** in the steps below.

Also if you're using multiple JTAG interfaces, you'll need to specify a unique port when launching the J-Link GDB server.  This port number can be any value 9999 or less, but it has to be unique for each GDB server instance. We'll refer to this value as **$JLINK_PORT** in the steps below.


###How to debug AP/GP bridge firmware using JTAG

####AP/GP bridge firmware boot process

Following reset, the device loads the firmware image from flash (SPIROM) 
to internal SRAM. (the firmware image contains startup logic to skip the copy to internal SRAM if the code is already running from ram.), and then jumps to it. This behavior is configured at reset 
by sampling the SPIBOOT_N pin, which is grounded. 

Firmware images can also be loaded and debugged via JTAG, by using GDB to load the image into internal SRAM while the processor is held in reset, and then releasing the processor from reset.  

In either of these cases, a valid firmware image is required in SPIROM in order
to boot the device. 

If the firmware image in flash fails to respond to JTAG, you will need to reprogram flash using a hardware programmer. For instructions on loading firmware into flash, see [this page](Flashing-images)

If you need a known-good firmware image to write to flash, in order to get JTAG debug working,  you can use nop-loop.bin, which is a firmware image that does nothing but loop forever. You can download the nop-loop firmware image [here](nop-loop.bin).  

#####Hardware Setup

1. REMOVE POWER FROM BDB
2. Connect the J-Link JTAG interface to debug board CON3 and host USB
3. Connect FPC from debug board CON9 to the associated device's FPC connector on the BDB. Cable is directional. Observe FPC labeling 'side Debug
Board' and 'BDB'. The table below lists the BDB FPC connector associated with each device.
4. Verify debug board SW5 is positioned *away from* the SW5 label  
5. Verify jumper is installed on debug board JP15 pins 1-2
6. Connect USB cable to debug board CON6 for serial debug output at 115200-n-8-1 on /dev/ttyUSBx. 
7. Apply power to the BDB.

Bridge | FPC Connector
-------|--------------  
APB1 | CON17  
APB2 | CON19  
APB3 | CON16  
GPB1 | CON14  
GPB2 | CON15


#####Software steps

Open a terminal window and start the J-Link GDB server, specifying the serial number and port number for the J-Link JTAG interface. 

`JLinkGDBServer -select usb=$JLINK_SN -port $JLINK_PORT`
 
You may see diagnostics of the form:  
`WARNING: Failed to read memory @ address 0xFFFFFFFF `  
`WARNING: Failed to read memory @ address 0xFFFFFFFF`  

These are harmless and it's okay to ignore them.

The following steps load the AP/GP bridge firmware image to internal RAM and run it.

* Open a second terminal window and start GDB:  `arm-none-eabi-gdb`   
* Connect to the gdbserver:  `target remote localhost:$JLINK_PORT`  
* Reset the target device: `monitor reset`  
* Load the symbols into GDB: `file <path-to-image-elf-file>`  
* Load the binary image: `restore <path-to-image-binary-file>`  
* Set the program counter to the reset handler: `set $pc=Reset_Handler`
* Set any initial breakpoints if needed  
* Release the processor from reset: `continue`  

###How to debug SVC firmware using JTAG

The SVC executes code from its internal flash, and supports debugging the firmware as it runs from flash. Therefore, the same GDB session is used to both flash and debug the firmware. Flashing the SVC firmware is described [here](Flashing-Images#load-firmware-image-to-svc-internal-flash-on-bdb).

#####Hardware Setup

1. REMOVE POWER FROM BDB
2. Connect the J-Link JTAG interface to debug board CON3 and host USB
3. Connect FPC from debug board CON9 to BDB CON18. Cable is directional. Observe FPC labeling 'side Debug
Board' and 'BDB'.  
4. Verify debug board SW5 is positioned *away from* the SW5 label  
5. Verify jumper is installed on debug board JP15 pins 1-2
6. Connect USB cable to BDB CON12 for SVC serial debug output at 115200-n-8-1 on /dev/ttyUSBx. 
7. Apply power to the BDB.

#####Software steps

1. Open a terminal window and start the J-Link GDB server, specifying the SVC device type, and the serial number and port number for the J-Link JTAG interface:  
```
JLinkGDBServer  -device STM32F417IG -select usb=$JLINK_SN -port $JLINK_PORT
```
2. Open a second terminal window and start GDB: `arm-none-eabi-gdb`   
3. Connect to the gdbserver: `target remote localhost:$JLINK_PORT`  
4. Reset the target: `monitor reset`  
5. Specify the ELF file: `file <path-to-image-elf-file>`  
6. Load the ELF file (and write to internal flash): `load`
7. Set initial breakpoints if needed.  
8. Release the processor from reset: `continue`  

If you rebuild the image, you can reload the new image from the same GDB session by typing control-C, then:
```
monitor reset
load
continue
```

###Additional Info

Debug Adapter Board CON3 JTAG Pinout

Pin|Signal|Comments
---|------|--------
1 | Vtgt | 1.8V
3 | TRST |
5 | TDI |
7 | TMS |
9 | TCK |
11 | NC |
13 | TDO  |
15 | SRESET | RESET_40uS
17 | NC |
19 | NC |
2  | Vtgt |(thru 0 ohm) 
4-20 |GND| Even pins only