This page contains information on debugging AP/GP bridge and SVC firmware images using the [Segger J-Link Pro JTAG interface](http://www.segger.com/jlink-pro.html). 

# Contents

- [Important Notes](#important-notes)
- [AP/GP bridge firmware JTAG debugging on BDB](#apgp-bridge-firmware-jtag-debugging-on-bdb)
- [SVC firmware JTAG debugging on BDB](#svc-firmware-jtag-debugging-on-bdb)
- [BDB Debug Board CON3 JTAG Pinout](#bdb-debug-board-con3-jtag-pinout)
- [ES2 Bridge JTAG vs. SPI ROM boot details](#es2-bridge-jtag-vs-spi-rom-boot-details)
- [Using Semihosting](#using-semihosting)
- [Greybus Taping](#greybus-taping)

# Important Notes

## Mandatory ES2 Bridge ASIC JTAG Setup

Because of [implementation details in the bridge ASIC boot process](#es2-bridge-jtag-vs-spi-rom-boot-details), there must be a valid image flashed into SPI ROM for JTAG to work on the ES2 versions of the [Toshiba bridge ASICs](Hardware-Overview#toshiba-bridge-asics).

[nop-loop.bin](https://github.com/projectara/Firmware-wiki/raw/master/nop-loop.bin) is a known-good firmware image you can [[write to flash|Flashing-images]] in order to get JTAG debug working.

## Don't try to flash nop-loop.bin to the SVC.

Don't try to flash nop-loop.bin, mentioned in the previous section, onto the SVC.  It's for **ES2 [bridge ASICs](Hardware-Overview#toshiba-bridge-asics) only**.

## J-Link Serial Numbers and GDB Server Ports

These instructions let you use multiple J-Link JTAG dongles, e.g. to debug two bridges at once, or a bridge and the SVC. To make that happen, you'll need to identify each dongle by its serial number, and open each of their GDB servers on a unique port.

- The serial number of the J-Link Pro is on a label on the bottom. Copy the numeric value following "S/N". Here is an example, with the serial number underlined in green:

  <img src="https://github.com/projectara/Firmware-wiki/wiki/images/JLink-Serial-Number.jpg"/>

  We'll refer to this value as **$JLINK_SN** in the steps below.

- Choose a port number for each J-Link's GDB server.  They can be any value from 1111 to 9999, but must be unique for each GDB server instance.  We'll refer to this value as **$JLINK_PORT** in the steps below.

# AP/GP bridge firmware JTAG debugging on BDB

## Hardware Setup

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

## Software steps

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
* Load the binary image: `restore <path-to-image-binary-file> binary`  
* Set the program counter to the reset handler: `set $pc=Reset_Handler`
* Set any initial breakpoints if needed  
* Release the processor from reset: `continue`  

# SVC firmware JTAG debugging on BDB

The SVC executes code from its internal flash, and supports debugging the firmware as it runs from flash. Therefore, the same GDB session is used to both flash and debug the firmware. Flashing the SVC firmware is described [here](Flashing-Images#load-firmware-image-to-svc-internal-flash-on-bdb).

## Hardware Setup

1. REMOVE POWER FROM BDB
2. Connect the J-Link JTAG interface to debug board CON3 and host USB
3. Connect FPC from debug board CON9 to BDB CON18. Cable is directional. Observe FPC labeling 'side Debug
Board' and 'BDB'.  
4. Verify debug board SW5 is positioned *away from* the SW5 label  
5. Verify jumper is installed on debug board JP15 pins 1-2
6. Connect USB cable to BDB CON12 for SVC serial debug output at 115200-n-8-1 on /dev/ttyUSBx. 
7. Apply power to the BDB.

## Software steps

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

# BDB Debug Board CON3 JTAG Pinout

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

# ES2 Bridge JTAG vs. SPI ROM boot details

Following reset, the device loads the firmware image from flash (SPIROM) 
to internal SRAM. (the firmware image contains startup logic to skip the copy to internal SRAM if the code is already running from ram.), and then jumps to it. This behavior is configured at reset 
by sampling the SPIBOOT_N pin, which is grounded. 

Firmware images can also be loaded and debugged via JTAG, by using GDB to load the image into internal SRAM while the processor is held in reset, and then releasing the processor from reset.  

In either of these cases, a valid firmware image is required in SPIROM in order
to boot the device.

If the firmware image in flash fails to respond to JTAG, you will need to [reprogram flash](Flashing-images) using a hardware programmer.

# Using Semihosting

If you want to ship debug output out over JTAG instead of a UART, you can use ARM semihosting.

Step 1: activate the semihosting on the JTAG by running this in a GDB session:

```
gdb> monitor semihosting enable
```

Step 2: make sure the semihosting options are enabled in your build .config:

```
CONFIG_ARM_SEMIHOSTING=y
CONFIG_ARM_SEMIHOSTING_CONSOLE=y
```

Step 3: make sure that your .config’s uart driver will not try to register itself as /dev/console:

```
CONFIG_16550_NO_SERIAL_CONSOLE=y
```

To connect to the semihosting console where everything is printed:

```
$ telnet localhost 2333
```

# Greybus Taping

It can often be useful for debug to record Greybus operations on a bridge and be able to replay them. Greybus Taping will enable you to save all greybus operations received by a bridge and saves them in a file in your computer thanks to ARM Semihosting. At a later time you can replay them without needing an AP.

Step 1: enable GB Taping and the controlling application

```
CONFIG_GREYBUS_TAPE_ARM_SEMIHOSTING=y
CONFIG_ARA_GB_TAPE=y
```

Step 2: activate semihosting in JLink's GDBServer by running this in a GDB session on the bridge where you will record the operations.

```
gdb> monitor semihosting enable
```

Step 3: start taping the communication on the bridge

```
nsh> gb_tape -r filename.gb
```

Step 4: stop taping the communication on the bridge

```
nsh> gb_tape -s
```
Step 5: reset bridge FW, enable semihosting, and then replay your GB Session using the following command:

```
nsh> gb_tape -p filename.gb
```
