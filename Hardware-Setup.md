This page provides details on setting up your BDB and JTAG hardware. 

The configuration used here is for JTAG debugging of firmware images on APB1 and APB2 bridge devices.   If you're not using JTAG, you can skip the parts about connecting a JTAG interface. For details on using JTAG for debugging, see [this page](Debugging).

####General
The development hardware has exposed components and connectors, and is extremely ESD-sensitive. Ensure your work area is ESD-safe by using a grounded static-dissipative mat or work surface, and wearing a grounded wrist strap at all times. Do not handle boards any more than necessary, and always handle by the edges. Avoid touching components or pins.

The development hardware is also sensitive to power sequencing, so...   
**DO NOT CONNECT POWER SUPPLIES UNTIL DIRECTED BY THE INSTRUCTIONS!**

<!--
The configuration used here is for JTAG debugging of firmware images on APB1 and APB2 bridge devices.  

To configure for JTAG debugging the SVC firmware, see [this page](Debugging#how-to-debug-svc-firmware-using-jtag)

To configure for JTAG debugging on other bridge devices, see [this page](Debugging#how-to-debug-apgp-bridge-firmware-using-jtag).

Device| Debug FPC | Reset Sw
------|--------|------
APB1  | CON17  | SW8
APB2  | CON19  | SW9
APB3  | CON16  | SW7
GPB1  | CON14  | SW5
GPB2  | CON15  | SW6

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
2 | DAB6ATH96B0 | Debug adapter board (aka 'debug board')
2 | B6A-DEBUG_FPC | FPC cable for debug board
1 | DA0B6APB4A0 | Jetson adapter board
1 | IJZ-IR | FPC cable for Jetson adapter
1 | Jetson TK1 AP Devkit | Includes PSU, USB cable, etc

[Picture of BDB](images/BDB1B-Board.png)  
[Picture of debug boards, debug board FPC, and BDB Power Supply](images/BDB1B-Accessories.png)  

The following items are also required:  

Qty | Item | Description
----|------|-------------
2   | [J-Link Pro](http://www.segger.com/jlink-pro.html) | JTAG USB interface
3   | cable | USB A to micro B cable
 
####Configure Debug Adapter Boards

1. Verify jumper is installed at JP15 pins 1-2. This provides power to the JTAG interface. Pin 1 is denoted by a round solid dot on the board.
2. Verify SW5 is positioned *away* from the SW5 label on the silkscreen. This allows the ARM processor in the bridge chip to run. If SW5 is in the other position, the processor is held in reset.

[Picture of debug board JP15 and SW5 settings](images/Debug-Adapter-Board-Rev-B-Setup.png)

####Attach cables

**UNPLUG THE BDB POWER SUPPLY**  
**DO NOT APPLY POWER UNTIL INSTRUCTED.**  

Refer to the picture and follow the steps below.  
[Picture of BDB cabling](images/BDB1B-Setup-Birds-Eye-View.png)

1. Attach the FPC end labeled **BDB** to BDB CON17 (APB1 SPI/JTAG/TRACE). Attach the other end to the mating connector on a Debug Adapter Board. 
2. Verify BDB SW8 (APB1 RESET) is positioned *toward* pins 3 and 6 (left). [Picture of BDB CON17 and SW8](images/BDB1B-APBridge-1-JTAG-And-Reset.png)  
3. Attach the FPC end labeled **BDB** to BDB CON19 (APB2 SPI/JTAG/TRACE). Attach the other end to the mating connector on a Debug Adapter Board. 
4. Verify BDB SW9 (APB2 RESET) is positioned *toward* pins 3 and 6 (up). [Picture of BDB CON19 and SW9](images/BDB1B-APBridge-2-JTAG-And-Reset.png)  
5. Attach a USB cable to each Debug Adapter Board at CON6 (UART). Leave the other end unconnected. [Picture of CON6](images/Debug-Adapter-Board-Rev-B-Connections.png)  
6. Connect JTAG interface cable from J-Link to CON3 (Bridge) on a Debug Adapter Board. Repeat for the other J-Link and Debug Adapter Board [Picture of CON3](images/Debug-Adapter-Board-Rev-B-Connections.png). Verify you are connecting to CON3, not CON2!   
6. Connect both J-Link USB cables to the development system. 
7. Identify the Debug Adapter Board connected to APB1. Connect the free end of the USB cable you attached in step 4 to the development system.
8. Identify the Debug Adapter Board connected to APB2. Connect the free end of the USB cable you attached in step 4 to the development system.
9. On the development system, run a terminal emulator such as minicom, and configure for 115200, 8N1, no hardware flow control. APB1 debug output should appear on /dev/ttyUSB0 and APB2 debug output on /dev/ttyUSB1, assuming no other USB serial devices are attached to the development system.
10. Attach a USB cable to BDB CON 28 (USB to APB1 HSIC). Leave the free end disconnected for now. This cable will connect the AP to the BDB.  [Picture of BDB CON28](images/BDB1B-AP-USB.png)  

####Apply power
1. Attach the 12VDC power supply to BDB CON27 (DC Jack 12V). [Picture of BDB Power Connection](images/BDB1B-Power-Connection.png)  
2. Plug the 12VDC power supply into an outlet

