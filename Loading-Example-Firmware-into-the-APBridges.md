Before proceeding, please complete the [Firmware Build](Firmware-Build) steps.  If you followed the recommendation on that page, you'll have two firmware images ready to load:
````
$HOME/nuttx.apb2
$HOME/nuttx.apb1
````
For simplicity, the instructions on this page will assume this naming, and assume the use of a J-Link Pro JTAG Debugger.

#### Loading Firmware into APBridge 2
1. Identify the serial number of the J-Link Pro that's attached to APBridge 2.  This numeric value follows the S/N: marking on the bottom of the device.  We'll call that the "jlink_apb2_sn".
2. Open a separate terminal window and start the J-Link GDB server, specifying the serial number and a unique port number that GDB will use when attaching to the server (2341 in this example)

   `JLinkGDBServer -select usb=jlink_apb2_sn -port 2341`
3. Open another terminal window start GDB

    `arm-none-eabi-gdb`  
4. Connect to your gdbserver (this and the steps that follow are commands issued to GDB)

    `target remote localhost:2341`
5. Reset your target

    `monitor reset`
6. Load the NuttX symbols into GDB, and load the binary into memory (answer affirmatively if asked whether this is OK)

    `file $HOME/nuttx.apb2`  
    `load`
7. Set your program counter to the NuttX reset handler

    `set $pc=Reset_Handler`
8. Run your code

    `continue`

At this point, if you have a terminal emulator open and successfully connected to the APBridge 2 console, you would see some initial output followed by an "nsh>" prompt.

#### Loading Firmware into APBridge 1
1. Identify the serial number of the J-Link Pro that's attached to APBridge 1.  This numeric value follows the S/N: marking on the bottom of the device.  We'll call that the "jlink_apb1_sn".
2. Open a separate terminal window and start the J-Link GDB server, specifying the serial number and a unique port number that GDB will use when attaching to the server (2331 in this example, which happens to be JLinkGDBServer's default)

   `JLinkGDBServer -select usb=jlink_apb1_sn -port 2331`
3. Open another terminal window and start GDB

    `arm-none-eabi-gdb`
4. Connect to your gdbserver (this and the steps that follow are commands issued to GDB)

    `target remote localhost:2331`
5. Reset your target

    `monitor reset`
6. Load the NuttX symbols into GDB, and load the binary into memory (answer affirmatively if asked whether this is OK)

    `file $HOME/nuttx.apb1`  
    `load`
7. Set your program counter to the NuttX reset handler

    `set $pc=Reset_Handler`
8. Run your code

    `continue`

At this point, if you have a terminal emulator open and successfully connected to the APBridge 1 console, you would see some initial output followed by an "nsh>" prompt.  There may be some errors output regarding unsuccessful connections to certain CPorts: this is normal and anticipated.

Great!  You've got the example firmware built, loaded, and running in APBridge 1 and APBridge 2.  Now it's time to [Exercise the Example Firmware in the APBridges](Exercising-Example-Firmware-in-the-APBridges).
