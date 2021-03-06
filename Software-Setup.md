A number of one-time setup tasks are needed to prepare a development system for building and debugging firmware images. These instructions assume a 64-bit installation of Ubuntu 14.04, and only need to be applied once for a given development machine. A  physical or virtual machine can be used. Run `uname -m` on your machine and verify “x86_64” appears, indicating your system is 64-bit. 

These instructions call for installing the nuttx and related repositories into your $HOME 
directory. If you need to install the repositories in another folder, you'll need to modify 
paths and commands accordingly.

#####STEP 1. Install required software packages  
```
sudo apt-get install libusb-dev libpci-dev build-essential 
sudo apt-get install autoconf pkg-config libtool git
sudo apt-get install gperf flex bison
sudo apt-get install libncurses5-dev libncurses5:i386
sudo apt-get install python-dev python-virtualenv
```

#####STEP 2. Make sure Git is set up correctly

If this is the first time you’ve set up Git on your machine, make sure
to set your git user.name and user.email fields [as described
here](http://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup).  

#####STEP 3. Install the ARM cross tools  
Firmware is built using a prebuilt ‘arm-none-eabi-*’ toolchain. This toolchain is maintained by ARM and hosted on Launchpad.  The currently supported version is "GCC ARM Embedded 4.8-2014-q3-update"  
1. Download the toolchain from [here](https://launchpad.net/gcc-arm-embedded/4.8/4.8-2014-q3-update/+download/gcc-arm-none-eabi-4_8-2014q3-20140805-linux.tar.bz2)  
2. Move the downloaded archive to $HOME  
3. `cd home`  
4. `tar xjf gcc-arm-none-eabi-4_8-2014q3-20140805-linux.tar.bz2`  
5. append these lines to your .profile   
`# add gcc cross tools path for building NuttX firmware`  
`export PATH="$HOME/gcc-arm-none-eabi-4_8-2014q3/bin:$PATH"`  


#####STEP 4. Install Manifesto (python script)
`cd $HOME`  
`git clone https://github.com/projectara/manifesto`  

Append these lines to your .profile:  
`# add manifesto path`  
`export PATH="$PATH:$HOME/manifesto"`  


#####STEP 5. JTAG Setup
These instructions assume the [Segger J-Link Pro](http://www.segger.com/jlink-pro.html) is used. Other JTAG interfaces could be used, but you'll have to figure them out on your own. 

Download the Segger J-Link software [here](http://www.segger.com/jlink-software.html). Select the DEB Installer for the "Software And Documentation Pack" for 64-bit Linux.  The download from Segger will typically open in the Ubuntu Software Center, and offer the option to Install the package, which should be accepted.  The J-Link software includes a GDB server that accepts connections from ‘arm-none-eabi-gdb’ which is included with the prebuilt toolchain installed above.  


#####STEP 6. Install and Build the Flashrom Utility 
Flashrom is a utility used to flash the SPIROM.

**DO NOT USE FLASHROM OBTAINED FROM ANYWHERE OTHER THAN THE PROJECT ARA GITHUB.**

Recent versions of the Dediprog SF-100 are not working with mainline flashrom. You **must** use the Project Ara Flashrom Repository.

Clone the [Project Ara flashrom repository](General-Information#flashrom-spi-rom-programming-tool), checkout the master branch, and rebuild with the following command:  
`user@mymachine:~/flashrom$ make`

Install flashrom and the manpage into /usr/local:  
`user@mymachine:~/flashrom$ sudo make install`

If you want to use flashrom without root permission:
```
user@mymachine:~/flashrom$ sudo cp util/z60_flashrom.rules /etc/udev/rules.d/
user@mymachine:~/flashrom$ sudo udevadm control --reload
user@mymachine:~/flashrom$ sudo usermod -a -G plugdev $USER
```

Then log out and log back in. You should able to run flashrom without sudo.

#####STEP 7. Build and install the stm32ld utility

Clone https://github.com/jsnyder/stm32ld

Build with the following command:
```
user@mymachine:~/stm32ld$ gcc -o stm32ld main.c stm32ld.c serial_posix.c
```

Install by copying to /usr/local:
```
user@mymachine:~/stm32ld$ sudo cp stm32ld /usr/local/bin
```

#####STEP 8. Clone the firmware git repository
```
cd $HOME
git clone https://github.com/projectara/nuttx
cd nuttx
git config commit.template nuttx/tools/git-commit-template
```


#####STEP 9. Build and install kconfig support
```
cd $HOME
cd nuttx/misc/tools/kconfig-frontends
./bootstrap
./configure --enable-mconf
make
sudo make install
sudo ldconfig
cd ../../..
```
Note: The above instructions assume that /usr/local exists on your system, and is referenced by the system path and by the dynamic linker configuration database.
