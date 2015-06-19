A number of one-time setup tasks are needed to prepare a development system for building and debugging firmware images. These instructions assume a 64-bit installation of Ubuntu 14.04, and only need to be applied once for a given development machine. A  physical or virtual machine can be used. Run `uname -m` on your machine and verify “x86_64” appears, indicating your system is 64-bit. 

These instructions assume you will clone the nuttx and related repositories into your $HOME directory,
i.e. 
```
$HOME
   +--nuttx
   +--flashrom
   +--manifesto  
   ... etc
```

If you need to install the repositories in another folder, you'll need to modify the paths and commands accordingly.

#####STEP 1. Install required software packages  
```
sudo apt-get install libusb-dev libpci-dev build-essential 
sudo apt-get install autoconf libtool gperf flex bison
sudo apt-get install libncurses5-dev libncurses5:i386
sudo apt-get install libusb-dev libpci-dev git
sudo apt-get install python-dev python-virtualenv
```

#####STEP 2. Install the ARM cross tools  
Firmware is built using a prebuilt ‘arm-none-eabi-*’ toolchain. This toolchain is maintained by ARM and hosted on Launchpad.  The currently supported version is "GCC ARM Embedded 4.8-2014-q3-update"  
1. Download the toolchain from [here](https://launchpad.net/gcc-arm-embedded/4.8/4.8-2014-q3-update/+download/gcc-arm-none-eabi-4_8-2014q3-20140805-linux.tar.bz2)  
2. Move the downloaded archive to $HOME
3. Extract with 'tar xjf gcc-arm-none-eabi-4_8-2014q3-20140805-linux.tar.bz2'  
3. append these lines to your .profile  
```
# add gcc cross tools path for building NuttX firmware
export PATH="$HOME/gcc-arm-none-eabi-4_8-2014q3/bin:$PATH"
```

#####STEP 3. Install Manifesto (python script)
```
cd $HOME
git clone https://github.com/projectara/manifesto
```
Append these lines to your .profile:
```
# add manifesto path
export PATH="$PATH:$HOME/manifesto"
```

#####STEP 4. JTAG Setup
These instructions assume the [Segger J-Link Pro](http://www.segger.com/jlink-pro.html) is used. Other JTAG interface may work, but you'll have to figure them out on your own. Segger J-Link software can be downloaded [here](http://www.segger.com/jlink-software.html). J-Link software includes a GDB server that can be connected to using ‘arm-none-eabi-gdb’ which is included with the prebuilt toolchain installed above.  
1. Download the DEB Installer for the "Software And Documentation Pack" for 64-bit Linux.  The download from Segger will typically open in the Ubuntu Software Center, and offer the option to Install the package, which should be accepted.  
2. If this is the first time you’ve set up Git on your machine, make sure
to set your git user.name and user.email fields [as described
here](http://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup).  

#####STEP 6. Install and Build the Flashrom Utility 
Flashrom is a utility used to flash the SPIROM.
Repository URL is [here](General-Information#flashrom-spi-rom-programming-tool)   
Clone the repository, checkout the master branch, and rebuild with the following command:
```
user@mymachine:~/flashrom$ CONFIG_DEDIPROG=yes make
```


