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

### BDB Debug Hardware

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

### Debug Adapter Board Configuration

1. Verify jumper is installed at JP15 pins 1-2. This provides power to the JTAG interface. Pin 1 is denoted by a round solid dot on the board.
2. Verify SW5 is positioned *away* from the SW5 label on the silkscreen. This allows the ARM processor in the bridge chip to run. If SW5 is in the other position, the processor is held in reset.

[Picture of debug board JP15 and SW5 settings](images/Debug-Adapter-Board-Rev-B-Setup.png)

### Debug Board / BDB Connection Procedure

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

### Apply power

1. Attach the 12VDC power supply to BDB CON27 (DC Jack 12V). [Picture of BDB Power Connection](images/BDB1B-Power-Connection.png)

2. Plug the 12VDC power supply into an outlet
