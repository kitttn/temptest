The Manifest is a contiguous block of data that includes a Manifest Header and a set of Descriptors. When read, a Manifest is transferred in its entirety. This allows the Interface to be described to the AP Module all at once, alleviating the need for multiple communication messages during the enumeration phase of the Interface.

### Current Limitations
Currently, the SVC protocol is not yet operational.

For this reason:
* SVC protocol is emulated by APBridgeA (send hotplug event by example)
* SVC routing table is static

In order to be have Greybus working from AP to Bridges, we have to build manifest in every firmwares (APBridgeA, Bridges, SVC):
* APBridgeA require it to send a hotplug event
* SVC require it to generate the routing table
* Bridge require it for registering Greybus drivers and to transmit it AP for enumeration

When SVC prtocol will be operational then only Bridge will have to use manifest.

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