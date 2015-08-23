This page contains information on debugging AP/GP bridge and SVC firmware images using the [Segger J-Link Pro JTAG interface](http://www.segger.com/jlink-pro.html). 

# Contents

- [Important Notes](#important-notes)
- [BDB JTAG debugging](#bdb-jtag-debugging)
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

# BDB JTAG Debugging

## AP/GP bridge firmware JTAG debugging on BDB

This section provides details on setting up your BDB and JTAG hardware.

### Required Materials

Verify you have everything listed in the [BDB Quick Start setup](BDB2-Quick-Start-Guide#step-2-set-up-your-development-hardware) section.

### General Advice

The development hardware has exposed components and connectors, and is extremely **ESD-sensitive**.

- Ensure your work area is ESD-safe by using a grounded static-dissipative mat or work surface, and wearing a grounded wrist strap at all times.
- Do not handle boards any more than necessary, and prefer to handle by the edges.
- Avoid touching components or pins unless necessary.

The development hardware is also sensitive to power sequencing, so...

**DO NOT CONNECT POWER SUPPLIES UNTIL DIRECTED BY THE INSTRUCTIONS!**

### Hardware Setup Summary

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

### Hardware Setup Details

#### BDB Debug Hardware

Each programmable device on the BDB has a connector that mates with the "BDB Debug Board" via an FPC (a flat flexible cable). Here is a picture of the BDB Debug Board:

<a href=""https://github.com/projectara/Firmware-wiki/wiki/images/BDB_debug_board.JPG"">
<img src="https://github.com/projectara/Firmware-wiki/wiki/images/BDB_debug_board.JPG" width=640/></a>

The FPC connects:

- Bridge ASIC and SVC JTAG lines to the JTAG connector, for debugging
- Bridge ASIC SPI ROM lines to the Dediprog ([SF 100](Hardware-Overview#debugging-hardware)) connector, for reflashing firmware
- Bridge ASIC and reset lines to SW5, for resetting images
- Bridge ASIC serial port lines to a USB serial chip, and then a USB connector, for console access

The BDB Debug Board has the following features:
*CON6 - USB conversion of serial debug output from device
*CON1 - SPI ROM programming header (1.8V)
*SW5  - Reset switch
*CON3 - JTAG for Bridge
*CON2 - JTAG for SVC
*JP15 - pins 1-2 jumpered = JTAG power = 1.8V from BDB
*JP15 - pins 2-3 jumpered = JTAG power = 1.8V from Debug Adapter Board
*JP16 - pins 1-2 jumpered = 1.8V generated from USB VBUS
*JP16 - pins 2-3 jumpered = 1.8V generated from ??

Each bridge device has a slide RESET switch and a slide Detect/Wake (D/W) switch. The UniPro switch has a slide RESET switch. The SVC has momentary tact switches for RESET and GPIO.

#### Debug Adapter Board Configuration

1. Verify jumper is installed at JP15 pins 1-2. This provides power to the JTAG interface. Pin 1 is denoted by a round solid dot on the board.
2. Verify SW5 is positioned *away* from the SW5 label on the silkscreen. This allows the ARM processor in the bridge chip to run. If SW5 is in the other position, the processor is held in reset.

[Picture of debug board JP15 and SW5 settings](images/Debug-Adapter-Board-Rev-B-Setup.png)

#### Debug Board / BDB Connection Procedure

Important:

- **UNPLUG THE BDB POWER SUPPLY BEFORE STARTING.**
- **DO NOT APPLY POWER UNTIL INSTRUCTED.**

Refer to the picture and follow the steps below.

<img src="https://github.com/projectara/Firmware-wiki/wiki/images/BDB2A.JPG" width=640/>

The following table shows these features for each device. Ensure that the Reset switch associated with each device is positioned *away* from pin 1.

Device| Debug Board Connector | Reset Switch   | Wake/Detect switch
------|-----------------------|----------------|-------------------
APB1  | CON17                 | SW8            | SW3
APB2  | CON19                 | SW9            | SW10
APB3  | CON16                 | SW7            | SW4
GPB1  | CON14                 | SW5            | SW2
GPB2  | CON15                 | SW6            | SW1
SVC   | CON18                 | SW11           | none
Switch| (undocumented here)   | (undocumented) | none

1. Attach the FPC end labeled **BDB** to BDB Debug Board Connector for your device (see table above). Attach the other end to the mating connector on a Debug Adapter Board.

2. Verify device reset swtich is positioned *toward* pins 3 and 6 (left).

3. Attach a USB cable to each Debug Adapter Board at CON6 (UART). Leave the other end unconnected:

  <img src="https://github.com/projectara/Firmware-wiki/wiki/images/Debug-Adapter-Board-Rev-B-Connections.png"/>

4. Connect JTAG interface cable from J-Link to CON3 (Bridge) on a Debug Adapter Board. Verify you are connecting to CON3, **not CON2**!

5. Connect J-Link USB cable to your development workstation.

6. Connect the free end of the USB cable you attached in step 3 to the development system.

8. On the development system, run a terminal emulator such as minicom, and configure for 115200, 8N1, no hardware flow control. The device should come up as `/dev/ttyUSBN`, for some `N`.

9. Attach a USB cable to BDB CON 28 (USB to APB1 HSIC). Leave the free end disconnected for now. This cable will connect the AP to the BDB when you have booted the system.  [Picture of BDB CON28](images/BDB1B-AP-USB.png)

#### Apply power

1. Attach the 12VDC power supply to BDB CON27 (DC Jack 12V). [Picture of BDB Power Connection](images/BDB1B-Power-Connection.png)

2. Plug the 12VDC power supply into an outlet

### Software steps

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

## SVC firmware JTAG debugging on BDB

The SVC executes code from its internal flash, and supports debugging the firmware as it runs from flash. Therefore, the same GDB session is used to both flash and debug the firmware. Flashing the SVC firmware is described [here](Flashing-Images#load-firmware-image-to-svc-internal-flash-on-bdb).

### Hardware Setup

1. REMOVE POWER FROM BDB
2. Connect the J-Link JTAG interface to debug board CON3 and host USB
3. Connect FPC from debug board CON9 to BDB CON18. Cable is directional. Observe FPC labeling 'side Debug
Board' and 'BDB'.  
4. Verify debug board SW5 is positioned *away from* the SW5 label  
5. Verify jumper is installed on debug board JP15 pins 1-2
6. Connect USB cable to BDB CON12 for SVC serial debug output at 115200-n-8-1 on /dev/ttyUSBx. 
7. Apply power to the BDB.

### Software steps

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

## BDB Debug Board CON3 JTAG Pinout

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
