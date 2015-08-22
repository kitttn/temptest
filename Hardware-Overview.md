# Contents

- [Toshiba Bridge ASICs](#toshiba-bridge-asics)
- [Supervisory Controller (SVC)](#supervisory-controller-svc)
- [Big Development Board (BDB)](#big-development-board-bdb)
- [Software Development Board (SDB)](#software-development-board-sdb)
- [Supported AP Boards](#supported-ap-boards)
- [Debugging Hardware](#debugging-hardware)

# Toshiba Bridge ASICs

Two types of bridge ASIC are used for Project Ara:

1. AP Bridge
2. GP Bridge

## Application Processor (AP) Bridge

The Toshiba AP Bridge is an ASIC custom-developed for Project Ara, with a Cortex-M3 microcontroller running at 96MHz.

The term "AP Bridge" is something of a misnomer. This device is actually used for three use cases for Project Ara:

1. To connect an application processor (AP) without UniPro to the UniPro network, via USB (technically USB HSIC).
2. To connect camera devices using the MIPI CSI-2 protocol to the UniPro network.
3. To connect display devices using the MIPI DSI protocol to the UniPro network.

When the AP Bridge is used to connect an AP to UniPro, it is called "**APBridgeA**" ("A" for "AP"). This is sometimes shortened to "APBA". The APBridgeA also connects APs with MIPI CSI-2 or DSI interfaces to cameras and displays on the UniPro network.

When the AP Bridge is used to connect a camera or display device to UniPro, it is called "**APBridgeE**" ("E" for "endpoint"). This is sometimes shortened to "APBE".

Initial firmware is loaded into AP Bridge internal SRAM from an external SPI ROM, or the SRAM can be loaded from JTAG for debugging scenarios.

## General Purpose (GP) Bridge

The Toshiba GP Bridge has the same core features as the AP Bridge, with a few differences:

1. GP Bridge doesn't have CSI-2 or DSI like AP Bridge.
2. GP Bridge has an SDIO peripheral, unlike AP Bridge.
3. GP Bridge has fewer UniPro CPorts than AP Bridge.

The GP Bridge allows chips using various existing protocols to connect to the UniPro network.

# Supervisory Controller (SVC)

Chips from the ST Microelectronics STM32F4 series are used as the Supervisory Controller (SVC) for the current implementation of the Project Ara platform.

The SVC coordinates the connection of modules and bridges to the UniPro network switch, and handles system power sequencing, module detection, and various other functions.

# Big Development Board (BDB)

The Big Development Board (BDB) is a large printed circuit board that incorporates 
most of the circuitry for the "spiral 2" iteration of the Project Ara platform design, along with a plethora of connectors, jumpers, and switches.

<a href="https://github.com/projectara/Firmware-wiki/wiki/images/BDB2A.jpg"><img src="https://github.com/projectara/Firmware-wiki/wiki/images/BDB2A.jpg" width="640"/></a>

The [BDB Quick Start Guide](BDB-Quick-Start-Guide) is a tutorial for getting a BDB up and running for firmware development.

The final BDB revision is 2B, which is referred to in documents and discussions as "BDB2B". In this wiki, we try to keep it simple by just calling it "BDB". 

Notes:

- A previous version of the BDB, the "BDB2A", is also in use. If you have one, it should have been reworked as necessary to make it work with any BDB2B instructions in this wiki. 
- Older versions of the BDB, BDB1A and BDB1B, are no longer supported.

BDB's features include:

* An ST Microelectronics [STM32F417](http://www.st.com/web/en/catalog/mmc/FM141/SC1169/SS1577/LN11) MCU, which acts as Supervisory Controller (**SVC**) to the UniPro network on the board.
* An Toshiba UniPro **switch** (T6WT0XBG, or revision "ES2" of the UniPro switch), supporting high-speed point-to-point connectivity.
* USB and HSIC connections for an Application Processor (AP) to connect to the BDB.
* Toshiba "APBridge" bridge ASICs (T6WT1XBG, or revision "ES2" of the AP Bridge), which connects AP, camera, and display devices to the UniPro network. There are 3 of these devices on the BDB; they're named **APB1**, **APB2**, and **APB3**.
* Toshiba "GP Bridge" (T6WT2XBG, or revision "ES2" of the GP Bridge), which connects various interfaces such as GPIO, PWM, I2C, I2S, USB, SDIO, UART, etc. to the UniPro network. There are 2 of these devices on the BDB; they're named **GPB1** and **GPB2**.
* SMA connectors for probing or connecting HSIC, UniPro, D-PHY, and M-PHY to the switch or bridge ASICs.
* Breakout connectors for device JTAG, serial debug output, and flash programming.

# Software Development Board (SDB)

The Software Development Board is a printed circuit board that serves a similar function to the [Big Development Board (BDB)](#big-development-board-bdb), but with various improvements and without the connectors for the spiral 2 design of a Project Ara module interface block.

<a href="https://github.com/projectara/Firmware-wiki/wiki/images/SDB.jpg"><img src="https://github.com/projectara/Firmware-wiki/wiki/images/SDB.jpg" width="640"/></a>

The [SDB Quick Start Guide](SDB-Quick-Start-Guide) is a tutorial for getting an SDB up and running for firmware development.

SDB's features include:

* An ST Microelectronics [STM32F417](http://www.st.com/web/en/catalog/mmc/FM141/SC1169/SS1577/LN11) MCU, which acts as Supervisory Controller (**SVC**) to the UniPro network on the board.
* An Toshiba UniPro **switch** (T6WT0XBG, or revision "ES2" of the UniPro switch), supporting high-speed point-to-point connectivity.
* A pair of low- and high-speed expansion connectors to interface the SDB with AP dev boards built according to the [Linaro 96Boards](https://www.96boards.org/) standard.
* Toshiba "APBridge" bridge ASICs (T6WT1XBG, or revision "ES2" of the AP Bridge), which connects AP, camera, and display devices to the UniPro network. There are 3 of these devices on the BDB; they're named **APB1**, **APB2**, and **APB3**.
* Toshiba "GP Bridge" (T6WT2XBG, or revision "ES2" of the GP Bridge), which connects various interfaces such as GPIO, PWM, I2C, I2S, USB, SDIO, UART, etc. to the UniPro network. There are 2 of these devices on the BDB; they're named **GPB1** and **GPB2**.
* Debug headers which break out various AP and GP Bridge peripherals.
* A bank of SMA connectors which break out APB2's CSI/DSI peripheral for use with external DSI displays.
* A bank of SMA connectors which break out APB3's CSI/DSI peripheral for use with external CSI-2 cameras.
* Two banks of SMA connectors for connecting external UniPro interfaces to the switch.
* Two banks of headers for connecting external module development boards to the SVC etc.
* Breakout connectors for device JTAG, serial debug output, and flash programming.

# Supported AP Boards

The following development boards are currently support for Android and Linux development for Project Ara.

-   [NVIDIA Jetson TK1](https://developer.nvidia.com/jetson-tk1)
-   [96Boards Marvell PXA1938](https://www.96boards.org/products/)
 
# Debugging Hardware

Additional debugging hardware is used for Project Ara firmware development.

-   [Dediprog SF100 Device Programmer](http://www.dediprog.com/pd/spi-flash-solution/SF100)
-   [Segger J-Link Pro JTAG Adapter](https://www.segger.com/jlink-pro.html)
