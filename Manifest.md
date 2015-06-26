The Manifest is a contiguous block of data that includes a Manifest Header and a set of Descriptors. When read, a Manifest is transferred in its entirety. This allows the Interface to be described to the AP Module all at once, alleviating the need for multiple communication messages during the enumeration phase of the Interface.

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
The manifest must be present in nuttx/apps/greybus-utils/manifests. The manifest name must not contain the extension.