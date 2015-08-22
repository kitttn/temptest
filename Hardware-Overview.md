###Big Development Board (BDB)
The Big Development Board (BDB) is the canonical development platform
for firmware developers. It is a large printed circuit board that incorporates 
most of the circuitry for a Project Ara phone, along with a plethora of connectors, 
jumpers, and switches. The current BDB revision is 2B, which is referred to 
in documents and discussions as "BDB2B". In this wiki, we try to keep it simple 
by just calling it "BDB". 

Note: A previous version of the BDB, the "BDB2A", is also in use. If you have one, it should have been reworked as necessary to make it work with any BDB2B instructions in this wiki. 

BDB features include:
* An ST Microelectronics [STM32F417](http://www.st.com/web/en/catalog/mmc/FM141/SC1169/SS1577/LN11) MCU, which acts as Supervisory Controller (**SVC**) to the UniPro network on the board.
* A Toshiba UniPro **switch** (T6WT0XBG), supporting high-speed point-to-point connectivity.
* USB and HSIC connections for an Application Processor (AP) to connect to the BDB.
* Toshiba "APBridge" bridge ASIC (T6WT1XBG), which connects AP, camera, and display devices to the UniPro network. There are 3 of these devices on the BDB; they're named **APB1**, **APB2**, **APB3**.
* Toshiba "GPBridge" (T6WT2XBG), which connects various interfaces such as GPIO, PWM, I2C, I2S, USB, SDIO, UART, etc. to the UniPro network. There are 2 of these devices on the BDB; they're named **GPB1** and **GPB2**.
* SMA connectors for probing or connecting HSIC, UniPro, D-PHY, and M-PHY to the switch or bridge ASICs.
* Breakout connectors for device JTAG, serial debug output, and flash programming.

###Toshiba Bridge ASICs

Two types of bridge ASIC are used for Project Ara:
1. AP Bridge
2. GP Bridge

####Application Processor (AP) Bridge

The Toshiba AP Bridge is an ASIC custom-developed for Project Ara, with a Cortex-M3 microcontroller running at 96MHz.

The term "AP Bridge" is something of a misnomer. This device is actually used for three use cases for Project Ara:

1. To connect an application processor (AP) without UniPro to the UniPro network, via USB (technically USB HSIC).
2. To connect camera devices using the MIPI CSI-2 protocol to the UniPro network.
3. To connect display devices using the MIPI DSI protocol to the UniPro network.

When the AP Bridge is used to connect an AP to UniPro, it is called "**APBridgeA**" ("A" for "AP"). This is sometimes shortened to "APBA". The APBridgeA also connects APs with MIPI CSI-2 or DSI interfaces to cameras and displays on the UniPro network.

When the AP Bridge is used to connect a camera or display device to UniPro, it is called "**APBridgeE**" ("E" for "endpoint"). This is sometimes shortened to "APBE".

Initial firmware is loaded into AP Bridge internal SRAM from an external SPI ROM, or the SRAM can be loaded from JTAG for debugging scenarios.

####General Purpose (GP) Bridge

The Toshiba GP Bridge has the same core features as the AP Bridge, with a few differences:

1. GP Bridge doesn't have CSI-2 or DSI like AP Bridge.
2. GP Bridge has an SDIO peripheral, unlike AP Bridge.
3. GP Bridge has fewer UniPro CPorts than AP Bridge.

The GP Bridge allows chips using various existing protocols to connect to the UniPro network.

###Supervisory Controller (SVC)

Chips from the ST Microelectronics STM32F4 series are used as the Supervisory Controller (SVC) for the current implementation of the Project Ara platform.

The SVC coordinates the connection of modules and bridges to the UniPro network switch, and handles system power sequencing, module detection, and various other functions.

###Supported AP Boards
-   [NVIDIA Jetson TK1](https://developer.nvidia.com/jetson-tk1)
-   [96Boards Marvell PXA1938](https://www.96boards.org/products/)
 
###Additional Debugging Hardware Required
-   [Dediprog SF100 Device Programmer](http://www.dediprog.com/pd/spi-flash-solution/SF100)
-   [Segger J-Link Pro JTAG Adapter](https://www.segger.com/jlink-pro.html)
-   USB Micro-B Cables
