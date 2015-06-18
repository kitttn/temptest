###Big Development Board (BDB)
The Big Development Board (BDB) is the canonical development platform
for firmware developers. It is a large printed circuit board that incorporates 
most of the circuitry for a Project Ara phone, along with a plethora of connectors, 
jumpers, and switches. The current BDB revision is 2A, which is referred to 
in documents and discussions as "BDB2A". A previous version, "BDB1B", is 
deprecated and no longer supported for development. If you have a BDB1B, you'll
need to exchange it for a BDB2A. 

BDB features include:
* STM32F417 Supervisory Controller (SVC)
* UniPro network switch which allows point-to-point connection between hardware modules
* HSIC connection for an Application Processor (AP) such as the Nvidia Jetson TK1, or 96Boards Marvell PXA1938
* AP bridge ASICs to allow an AP and display device to connect to the UniPro network
* General Purpose (GP) bridge ASICs to allow the UniPro network to connect to various low-speed downstream interfaces such as GPIO, PWM, I2C, I2S, USB, SDIO, UART, etc.
* SMA connectors for probing or interconnecting UniPro, D-PHY, and M-PHY
* Breakout connectors for SVC and bridge ASIC debugging and programming pins.

###Software Development Board (SDB)
This is a smaller, more compact version of the BDB that is currently in development and
is expected to become available to developers some time during the 2nd half of 2015. 

###Endoskeleton (Endo)
The endoskeleton (Endo) is the physical frame that Ara modules plug into. It is electrically similar to the BDB, but realized in the Ara phone form factor. 

###Modules
Modules are form-factor sized boards, plus mechanical casings, that implement the functional blocks of an Ara smartphone. Modules slide into slots in the endo to connect to power and UniPro. The BDB has a plastic fixture that allow the modules to be mounted in a similar way.

###UniPro Bridge ASICs

####Application Processor (AP) Bridge
This chip is a full-custom ASIC with a Cortex-M3 microcontroller running at 96MHz.
Initial firmware is loaded to internal SRAM from an external SPIROM, or the SRAM can be loaded from JTAG for debugging scenarios. This device connects an AP Module to the
UniPro network. It has a USB HSIC interface for the AP module, and a UniPro M-PHY 
interface that connects to the network switch. It can also act as a DSI-1 transmitter/receiver 
for interfacing with a MIPI DSI-compliant display, and a CSI-2 transmitter/receiver for
interfacing with cameras.

####General Purpose (GP) Bridge
This is the same core device as the AP Bridge, but with a different 
set of peripherals (e.g. I2C/PWM/SDIO,etc). This bridge allows other modules, including the AP, to connect to the peripheral interfaces via UniPro.

####Supervisory Controller (SVC)
This is an off-the-shelf STM32F417IG microcontoller with 192KBytes of RAM and 1MByte
of flash, running at 104MHz. It coordinates the connection of modules
and bridges to the UniPro network switch, and handles system power
sequencing, module detection, etc.

###Supported AP Boards
-   [NVIDIA Jetson TK1](https://developer.nvidia.com/jetson-tk1)
-   [96Boards Marvell PXA1938](https://www.96boards.org/products/)
 
###Additional Debugging Hardware Required
-   [Dedipro SF100 Device Programmer](http://www.dediprog.com/pd/spi-flash-solution/SF100)
-   [Segger J-Link Pro JTAG Adapter](https://www.segger.com/jlink-pro.html)
-   USB Micro-B Cables
