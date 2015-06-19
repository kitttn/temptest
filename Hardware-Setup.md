This page provides details on setting up your BDB and other hardware, to prepare for flashing and debugging firmware images.

####General
The development hardware has exposed components and connectors, and is extremely ESD-sensitive. Ensure your work area is ESD-safe by using a grounded static-dissipative mat or work surface, and wearing a grounded wrist strap at all times. Do not handle boards any more than necessary, and always handle by the edges. Avoid touching components or pins.

The development hardware is also sensitive to power sequencing, so...   
##DO NOT CONNECT POWER SUPPLIES UNTIL DIRECTED BY THE INSTRUCTIONS!
<!--
####Connecting with the devices

Each programmable device on the BDB has a connector that mates with the Debug Adapter Board FPC and carries SPI, JTAG,and serial debug lines to the Debug Adapter Board. Each bridge device has a slide RESET switch and a slide Detect/Wake (D/W) switch. The UniPro switch has a slide RESET switch. The SVC has momentary tact switches for RESET and GPIO.

The following table shows these features for each device.

Device| FPC    | Reset | D/W 
------|--------|-------|-----
APB1  | CON17  | SW8   | SW3
APB2  | CON19  | SW9   | SW10
APB3  | CON16  | SW7   | SW4
GPB1  | CON14  | SW5   | SW2
GPB2  | CON15  | SW6   | SW1
SVC   | CON18  | SW11  | none
Switch| CON20  | SW12  | none

The Debug Adapter Board has the following features:
*CON6 - USB conversion of serial debug output from device
*CON1 - SPI ROM programming header (1.8V)
*SW5  - Reset switch
*CON3 - JTAG for Bridge
*CON2 - JTAG for SVC
*JP15 - pins 1-2 jumpered = JTAG power = 1.8V from BDB
*JP15 - pins 2-3 jumpered = JTAG power = 1.8V from Debug Adapter Board
*JP16 - pins 1-2 jumpered = 1.8V generated from USB VBUS
*JP16 - pins 2-3 jumpered = 1.8V generated from ?? -->
####Required Materials

Verify you have everything listed in this section.

Your BDB shipment should contain the following.

Qty | Item | Description
----|------|-------------
1 | B6A DA0B6ATH8C0 | Big development board
1 | ADUS01 | 12VDC power supply for BDB
2 | DAB6ATH96B0 | Debug adapter board
2 | B6A-DEBUG_FPC | FPC cable for debug adapter board
1 | DA0B6APB4A0 | Jetson adapter board
1 | IJZ-IR | FPC cable for Jetson adapter
1 | Jetson TK1 AP Devkit | Includes PSU, USB cable, etc

[Picture of BDB](images/BDB1B-Board.png)  
[Picture of Debug Boards, FPCs, and BDB Power Supply](images/BDB1B-Accessories.png)  

The following items are also required:  

Qty | Item | Description
----|------|-------------
2   | [J-Link Pro](http://www.segger.com/jlink-pro.html) | JTAG USB interface
3   | cable | USB A to micro B cable
 
####Configure Debug Adapter Boards

Verify the Debug Adapter Boards are correctly configured.  

1. JP15 header pins 1-2 must have a jumper installed. JP15 is located between CON2 (CPU) and CON3 (Bridge). This provides power to the JTAG interface. Pin 1 is denoted by a round solid dot on the board.
2. SW5 (RST_40uS) must be positioned *away* from the SW5 label on the silkscreen. This allows the ARM processor in the bridge chip to run. If SW5 is in the other position, the processor is held in reset and JTAG debugging is not possible.

[Picture of debug board JP15 and SW5 settings](images/Debug-Adapter-Board-Rev-B-Setup.png)

####Attach cables

*DO NOT APPLY POWER UNTIL INSTRUCTED.*  

Refer to the picture and follow the steps below.  
[Picture of BDB cabling](images/BDB1B-Setup-Birds-Eye-View.png)

1. Attach a micro USB cable to BDB CON 28 (USB to APB1 HSIC).  Leave the other end unconnected. [Picture of BDB CON28](images/BDB1B-AP-USB.png)  
2. Identify the end of the Debug Adapter Board FPC labeled **BDB**. Attach this to BDB CON17 (APB1 SPI/JTAG/TRACE). Attach the other end to the mating connector on a Debug Adapter Board. Be sure you are connecting to CON17, not CON15.  Verify that BDB SW8 (APB1 RESET) is in the position shown, so that APB1 will be operational later on. [Picture of BDB APB1 JTAG and Reset](images/BDB1B-APBridge-1-JTAG-And-Reset.png)  
3. Identify the end of the Debug Adapter Board FPC labeled **BDB**. Attach this to BDB CON19 (APB2 SPI/JTAG/TRACE). Attach the other end to the mating connector on the other Debug Adapter Board. Verify that BDB SW9 (APB2 RESET) is in the position shown, so that APB2 will be operational later on. [Picture of APB2 JTAG and Reset](images/BDB1B-APBridge-2-JTAG-And-Reset.png)  
4. Attach a micro USB cable to each Debug Adapter Board at CON6 (UART). Leave the other end unconnected. [Debug Adapter Board UART connections](images/Debug-Adapter-Board-Rev-B-Connections.png)  
5. Connect a JTAG interface to CON3 (Bridge) on each Debug Adapter Board. Verify that you are plugging into CON3, not CON2.   
6. Connect the J-Link USB cable to the development system.  
7. Connect the USB cable from the Debug Adapter Boards for AP Bridge 1 to the development system.
8. Connect the USB cable from the Debug Adapter Boards for AP Bridge 2 to the development system.
9. On the development system, run a terminal emulator such as minicom, and configure for 115200, 8N1, no hardware flow control. The APB1 serial debug output should appear on /dev/ttyUSB0 and APB2 debug output on /dev/ttyUSB1. This assumes no other USB serial devices are attached to the development system.

####Apply power
Review the above steps and confirm all cables are secure, then attach the 12VDC power supply to BDB CON27 (DC Jack 12V). [Picture of BDB Power Connection](images/BDB1B-Power-Connection.png)  

