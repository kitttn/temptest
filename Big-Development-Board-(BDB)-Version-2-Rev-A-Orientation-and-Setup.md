#### Required Hardware

Congratulations!  You've received Version 1 Rev B of the Project Ara development hardware, a "Big Development Board" as we like to call it.  We typically use the shorthand "BDB 1 Rev B" or "BDB1B":
![BDB1B PCB](images/BDB1B-Board.png)
Here's an alternate image of the BDB1B, this time with a PCB-BASE plate installed:
![BDB1B PCB with PCB-BASE](images/BDB1B-Board-With-PCB-BASE.png)
Images in this wiki may vary as to whether they show the PCB-BASE plate.

Your kit should also contain:

* Two Debug Adapter Boards Rev B
* Two Debug Flat Printed Circuit (FPC) cables Rev B
* A 12V DC power source

![BDB1B Accessories](images/BDB1B-Accessories.png)

You'll also want to have:

* 2 [J-Link Pro JTAG programmers](http://www.segger.com/jlink-pro.html) with USB cable and 20-pin JTAG cable, so that you'll be able to program and control Application Processor (AP) Bridge 1 and Bridge 2 on your BDB1B.
* 1 micro USB to USB A cable, to connect to your Application Processor (AP).  Don't connect the USB A end of the cable to your AP until instructed during the bring up procedure.
* 2 micro USB to USB A cables, to provide access to the APBridge 1 and APBridge 2 consoles.

As with any development board, you'll ideally want to work in an ESD-safe environment that provides a controlled path for dissipating static buildup. 

#### One-Time Setup

Now what? Let's start with some setup.

Please take a few moments to verify that both of your Debug Adapter Boards are configured properly for JTAG use.  To enable JTAG debugging via a Debug Adapter Board Rev B, there are two essential prerequisites:

1. A jumper must be present between pins 1 and 2 of the header labeled JP15, located between CON2 (CPU) and CON3 (Bridge).  This provides power to the JTAG interface.  Pin 1 is denoted by a round solid dot on the board.

2. The switch labeled SW5 (RST_40uS) must be be positioned **away** from the SW5 label, towards the labels 3 and 6 on the board.  This allows the ARM processor in the bridge chip to run: otherwise, it's held in reset and JTAG debugging won't be possible.

The proper configuration is illustrated here:
![Debug Adapter Board Rev B Setup](images/Debug-Adapter-Board-Rev-B-Setup.png)

We'll begin with at a bird's-eye view of the BDB1B setup, for orientation and reference.  Please, however, follow the step-by-step instructions that follow, and **wait to apply power to the BDB1B until instructed**:
![BDB1B Setup Bird's-Eye View](images/BDB1B-Setup-Birds-Eye-View.png)

Before applying power, you'll want to cable everything together:
* Attach the micro USB end of a USB cable to the connector on the BDB1B labeled CON 28 (USB to APB1 HSIC).  Leave the other end of this cable unconnected, until instructed to attach it to your AP:
![BDB1 AP USB Connection](images/BDB1B-AP-USB.png)
* Attach one end of a Debug FPC cable (the end labeled **BDB**) to the BDB1B connector labeled CON17 (APB1 SPI/JTAG/TRACE) and the other end (labeled Debug Board) to the mating connector on one of the Debug Adapter Boards:
![BDB1B APBridge 1 JTAG and Reset](images/BDB1B-APBridge-1-JTAG-And-Reset.png)
Please double-check that you're connecting to CON17, not to CON15, which is in the same neighborhood.  Please also verify that SW8 (APB1 RESET) on the BDB1B is in the position shown, so that APBridge 1 will be operational later on.
* Attach one end of a Debug FPC cable (the end labeled **BDB**) to the BDB1B connector labeled CON19 (APB2 SPI/JTAG/TRACE) and the other end (labeled Debug Board) to the mating connector on the other Debug Adapter Board:
![BDB1B APBridge 2 JTAG and Reset](images/BDB1B-APBridge-2-JTAG-And-Reset.png)
Please verify that SW9 (APB2 RESET) on the BDB1B is in the position shown, so that APBridge 2 will be operational later on.
* Attach the micro USB end of a USB cable to the connector labeled CON6 (UART) on each Debug Adapter Board.  These cables will provide access to the consoles for APBridge 1 and APBridge 2:
![Debug Adapter Board Rev B](images/Debug-Adapter-Board-Rev-B-Connections.png)
* Connect a JTAG debugger to the 20-pin connector labeled CON3 (Bridge) on the each Debug Adapter Board. **Note**: Please visually verify that you are plugging into CON3, not CON2. CON3 is shown in the image above, with a ribbon cable attached, and the CON3 label is also printed on the Debug Adapter Board itself.
* Connect each JTAG debugger to the build machine via a direct USB connection.  The J-Link Pro JTAG Debugger also supports connections via Ethernet, but the present document assumes USB.

If the 2 USB console cables are connected to a PC, the APBridge 1 and APBridge 2 consoles may be accessed (when code is running) via a terminal emulator such as minicom: 115200, 8N1, no hardware flow control.

You may apply power at this point by attaching the provided 12V DC power source to CON27 (DC Jack 12V) on the BDB1B:
![BDB1B Power Connection](images/BDB1B-Power-Connection.png)

As a result of following the procedure detailed above, your setup should now match the bird's eye view that first appeared towards the top of the present page:
![BDB1B Setup Bird's-Eye View](images/BDB1B-Setup-Birds-Eye-View.png)

#### Next Steps

The next step in bringing up your development board is to finish the [Development Machine Setup](Development-Machine-Setup).

After that, you can proceed to the [Firmware Build](Firmware-Build).