###Big Development Board (BDB)
The Big Development Board (BDB) is the canonical development platform
for firmware developers. It is a large printed circuit board that incorporates 
most of the circuitry for a Project Ara phone, along with a plethora of connectors, 
jumpers, and switches. The current BDB revision is 2A, which is referred to 
in documents and discussions as "BDB2A". A previous version, "BDB1B", is 
deprecated and no longer supported for development. If you have a BDB1B, you'll
need to exchange it for a BDB2A. 

BDB features include:
* ST Microelectronics [STM32F417](http://www.st.com/web/en/catalog/mmc/FM141/SC1169/SS1577/LN11) Supervisory Controller (SVC)
* Toshiba T6WT0XBG UniPro network switch supporting high-speed point-to-point connectivity
* HSIC connection for an Application Processor (AP) to connect to the BDB
* Toshiba T6WT1XBG Application Processor (AP) bridge ASICs to connect AP and display devices to the UniPro network
* Toshiba T6WT2XBG General Purpose (GP) bridge ASICs to connect various interfaces such as GPIO, PWM, I2C, I2S, USB, SDIO, UART, etc. to the UniPro network
* SMA connectors for probing or connecting HSIC, UniPro, D-PHY, and M-PHY
* Breakout connectors for SVC and bridge ASIC debugging and programming.
* Debug breakout boards with industry-standard JTAG and USB serial connectors

###Software Development Board (SDB)
This is a smaller, more compact version of the BDB that is currently in development and
is expected to become available to developers some time during the 2nd half of 2015. 

###Endoskeleton (Endo)
The endoskeleton (Endo) is the physical frame that Ara modules plug into. It is electrically similar to the BDB, but realized in the Ara phone form factor. 

###Modules
Modules are form-factor sized boards, plus mechanical casings, that implement the functional blocks of an Ara smartphone. Modules slide into slots in the endo to connect to power and UniPro. The BDB has a plastic fixture that allow the modules to be mounted in a similar way.  Modules incorporate a UniPro bridge device to support connection to the UniPro network in the BDB or endo.

###Microcontrollers
There are 3 types of microcontroller used on the BDB:
- Application Processor (AP) Bridge
- General Purpose (GP) Bridge
- Supervisory Controller (SVC)

####Application Processor (AP) Bridge
This chip is a Toshiba full-custom ASIC with a Cortex-M3 microcontroller running at 96MHz.
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