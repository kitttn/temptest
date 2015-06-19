This page provides details on setting up your BDB and other hardware, to prepare for loading and debugging firmware images.

The development hardware has exposed components and connectors, and is extremely ESD-sensitive. Ensure your work area is ESD-safe by using a grounded static-dissipative mat or work surface, and wearing a grounded wrist strap at all times. Do not handle boards any more than necessary, and always handle by the edges. Avoid touching components or pins.

The development hardware is sensitive to power sequencing. *DO NOT CONNECT POWER SUPPLIES UNTIL REQUIRED BY THE INSTRUCTIONS!*.

####STEP 1. Required Materials

Verify you have everything listed in this section.

Your BDB shipment should contain the following.

Qty | Item | Description
----|------|-------------
1 | B6A DA0B6ATH8C0 | Big development board
1 | ADUS01 | 12VDC power supply for BDB
2 | DAB6ATH96B0 | Debug adapter board
2 | B6A-DEBUG_FPC | FPC cable for debug adapter
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
 
####STEP 2. Configure Debug Adapter Boards

Verify the Debug Adapter Boards are correctly configured.  

1. JP15 header pins 1-2 must have a jumper installed. JP15 is located between CON2 (CPU) and CON3 (Bridge). This provides power to the JTAG interface. Pin 1 is denoted by a round solid dot on the board.
2. SW5 (RST_40uS) must be positioned *away* from the SW5 label on the silkscreen. This allows the ARM processor in the bridge chip to run. If SW5 is in the other position, the processor is held in reset and JTAG debugging is not possible.

[Pciture showing JP15 and SW5 settings](images/Debug-Adapter-Board-Rev-B-Setup.png)

####STEP 3. Attach all cables

*DO NOT APPLY POWER UNTIL INSTRUCTED.*  

Refer to the picture and follow the steps below.  
[Picture BDB1B Setup](images/BDB1B-Setup-Birds-Eye-View.png)

1. Attach a micro USB cable to BDB CON 28 (USB to APB1 HSIC).  Leave the other end unconnected. [BDB1 AP USB Connection](images/BDB1B-AP-USB.png)  
2. Identify the debug adapter FPC end labeled **BDB**. Attach this to  BDB1B CON17 (APB1 SPI/JTAG/TRACE). Attach the other end, labeled Debug Board, to the mating connector on one of the Debug Adapter Boards. Be sure you are connecting to CON17, not CON15.  Verify that BDB SW8 (APB1 RESET) is in the position shown, so that APBridge 1 will be operational later on. [BDB1B APBridge 1 JTAG and Reset](images/BDB1B-APBridge-1-JTAG-And-Reset.png)  
3. Identify the debug adapter FPC end labeled **BDB** to BDB1B CON19 (APB2 SPI/JTAG/TRACE). Attach the other end, labeled Debug Board, to the mating connector on the other Debug Adapter Board. Verify that BDB SW9 (APB2 RESET) is in the position shown, so that APBridge 2 will be operational later on. [BDB1B APBridge 2 JTAG and Reset](images/BDB1B-APBridge-2-JTAG-And-Reset.png)  
4. Attach a micro USB cable to each Debug Adapter Board at CON6 (UART). Leave the other end unconnected. [Debug Adapter Board UART connections](images/Debug-Adapter-Board-Rev-B-Connections.png)  
5. Connect a JTAG interface to CON3 (Bridge) on each Debug Adapter Board. Verify that you are plugging into CON3, not CON2.   
6. Connect the J-Link USB cable to the development system.  
7. Connect the USB cable from the Debug Adapter Boards for AP Bridge 1 to the development system.
8. Connect the USB cable from the Debug Adapter Boards for AP Bridge 2 to the development system.
9. On the development system, run a terminal emulator such as minicom, and configure for 115200, 8N1, no hardware flow control. AP Bridge 1 console should appear on  /dev/ttyUSB0 and AP Bridge 2 console on  /dev/ttyUSB1. This assumes no other USB serial devices are attached to the development system.  

####STEP 4. Recheck all connections.
Review the above steps and confirm all cables are secure.

####STEP 5. Apply power
Attach the 12VDC power supply to BDB CON27 (DC Jack 12V). [BDB1B Power Connection](images/BDB1B-Power-Connection.png)  

