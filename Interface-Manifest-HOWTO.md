**FIXME: the manifest selection examples are out of date. NuttX needs some more examples for module developers (along with info on how to integrate with Greybus and the physical peripherals etc.)**

All Project Ara modules must declare an interface manifest for each of their UniPro links.

This page tells you how to build manifests into your module's bridge ASIC firmware images, so that it can be properly enumerated by the AP.

The interface manifest is a contiguous block of data that includes a Manifest Header and a set of Descriptors. The AP reads a module's interface manifests using the Greybus Control Protocol. (The SVC sends the AP hotplug notifications when modules come and go, so the AP knows when to read manifests, and when the modules with those manifests leave).

When read, a Manifest is transferred in its entirety. This allows the Interface to be described to the AP Module all at once, alleviating the need for multiple communication messages during the enumeration phase of the Interface.

## Select a manifest:

Project Ara's nuttx fork contains tools to manage manifests, along with some example manifests. When you configure your firmware image, you can either select an example manifest or use a custom manifest that you define.

Example manifests are located in nuttx/apps/greybus-utils/manifests. If you want to use your manifest, you currently must put it in this folder.

This section assumes you are familiar with [configuring, building, and flashing custom firmware images](#Build-custom-config).

To select an example manifest:
 
```
make menuconfig
Application Configuration  --->
	Greybus utility  --->
Select a predefined Manifest (Loopback)  --->
( ) GPIO
( ) I2C
( ) I2C and GPIO
( ) Battery, GPIO and I2C
(X) Loopback
( ) Vibrator
( ) GPIO, I2C, and Vibrator
( ) Custom manifest
```

Use the arrow keys to pick the example manifest you want to use, then save and exit make menuconfig. When you build your custom configuration by running `make`, the human-readable manifest will be converted into its binary representation using [manifesto](https://github.com/projectara/manifesto). The resulting manifest binary will be included in your firmware image, and, if your Greybus configuration options are correct, will be sent to the AP using the Greybus Control Protocol.

## Example

In this example for SDB or BDB, we want to have Greybus GPIO, I2C, and vibrator Protocols CPorts available on APB2, and a Loopback CPort on APB3.

First, select the gpio-i2c-vibrator manifest, by making a custom .config starting from the bridge/es2-debug-generic defconfig, then changing the predefined manifest like so:

```
make menuconfig
Application Configuration  --->
	Greybus utility  --->
Select a predefined Manifest (GPIO, I2C, and Vibrator)  --->
( ) GPIO
( ) I2C
( ) I2C and GPIO
( ) Battery, GPIO and I2C
( ) Loopback
( ) Vibrator
(X) GPIO, I2C, and Vibrator
( ) Custom manifest
```

Then build the firmware image and flash it to APB2.

Afterwards, select the loopback manifest:

```
make menuconfig
Application Configuration  --->
	Greybus utility  --->
Select a predefined Manifest (GPIO, I2C, and Vibrator)  --->
( ) GPIO
( ) I2C
( ) I2C and GPIO
( ) Battery, GPIO and I2C
(X) Loopback
( ) Vibrator
( ) GPIO, I2C, and Vibrator
( ) Custom manifest
```

Then build the firmware image and flash it to APB3.

Reset your dev board and AP. The two bridges (APB2 and APB3) should enumerate with the CPort connections described above.
