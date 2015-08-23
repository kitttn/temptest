All Project Ara modules must declare an interface manifest for each of their UniPro links.

The interface manifest is a contiguous block of data that includes a Manifest Header and a set of Descriptors. The AP reads a module's interface manifests using the Greybus Control Protocol. (The SVC sends the AP hotplug notifications when modules come and go, so the AP knows when to read manifests, and when the modules with those manifests leave).

When read, a Manifest is transferred in its entirety. This allows the Interface to be described to the AP Module all at once, alleviating the need for multiple communication messages during the enumeration phase of the Interface.

### Current Limitations
Currently, the SVC protocol is not yet operational.

For this reason:
* SVC protocol is emulated by APBridgeA (send hotplug event by example)
* SVC routing table is static

In order to support working Greybus protocols, we build manifests in every firmware image (APBridgeA, Bridges, SVC):

* The APBridgeA requires a manifest send hotplug events and emulate the SVC
* The SVC requires it to generate the routing table
* Other Bridges require it for registering Greybus drivers and to transmit it to the AP for enumeration

When the SVC protocol is done, only non-APBridgeA bridges will need manifests.

### Select a manifest:
Currently, nuttx come with tools to manage manifest and some manifest examples.
When you configure your firmware, you can select an examples manifest or use or more custom manifest.
Example manifest are located in nuttx/apps/greybus-utils/manifests. If you want to use your manifest, you must move it in this folder.

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
If you are configuring the SVC, you must also configure the device id:
```
(2) destination device id
```
The device id is used by SVC to know which Bridge must be setup in routing table.

If you select a custom manifest:
```
make menuconfig
Application Configuration  --->
	Greybus utility  --->
Select a predefined Manifest (Loopback)  --->
( ) GPIO
( ) I2C
( ) I2C and GPIO
( ) Battery, GPIO and I2C
( ) Loopback
( ) Vibrator
( ) GPIO, I2C, and Vibrator
(X) Custom manifest
(device_id,your_custom_module)    manifest name (NEW)
```
The manifest must be present in nuttx/apps/greybus-utils/manifests. The manifest name must not contain the extension. device_id is the device id of the bridge you want to use with this manifest.

### Multiple modules
By default, firmware are build with only one manifest (and then only one module support).
If you want to use more modules, you have to build SVC and APBridgeA firmware with several manifests.

To select several manifests for APBridgeA and SVC firmwares:
```
make menuconfig
Application Configuration  --->
	Greybus utility  --->
Select a predefined Manifest (Loopback)  --->
( ) GPIO
( ) I2C
( ) I2C and GPIO
( ) Battery, GPIO and I2C
( ) Loopback
( ) Vibrator
( ) GPIO, I2C, and Vibrator
(X) Custom manifest
(device_id_1,your_custom_module_1;device_id_2,your_custom_module_2)    manifest name (NEW)
```
Each device id and manifest name must be separated by semicolon. 

**Note:**
If SVC and APBridgeA are configured to support several modules, all modules configured in SVC and APBridgeA must be operational. Otherwise, it will disturb the routing table and the enumeration process.

#### Example
In this example, we want to have gpio, i2c and vibrate on APBridge2 and the loopback in APBridge3.

First configure APBridge2 to use gpio-i2c-vibrator manifest:
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
Then you can built firmware and flash the Bridge.

After configure APBridge3 to use loopback manifest:
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
Then you can built firmware and flash the Bridge.

Now, configure SVC and APBridgeA to use several manifests:
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
( ) GPIO, I2C, and Vibrator
(X) Custom manifest
(2,simple-gpio-i2c-vibrator-module;3,simple-loopback-module)    manifest name (NEW)
```
2 is the device id of APBridge2 and 3 the one of APBridge3.
The current configuration tell to SVC and APBridgeA to use gpio-i2c-vibrator manifest for APBridge2 and loopback manifest for APBridge3. These information will be used to create the routing table and send hotplug events to AP.