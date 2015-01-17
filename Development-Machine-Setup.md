You'll need a development machine or VM to configure, build and load the firmware for the Application Processor (AP) bridges on BDB 1B.

These instructions assume a 64-bit installation of Ubuntu 14.04, and only need to be applied once for a given devleopment machine.

You'll need various Ubuntu packages:

````
sudo apt-get install autoconf libtoolize libtool gperf
sudo apt-get install flex bison libncurses5-dev libncurses5:i386
sudo apt-get install libusb-dev libpci-dev
````

#### ARM Compiler and GDB
You'll build firmware using the gcc-arm-embedded ‘arm-none-eabi’ toolchain maintained by ARM and hosted on Launchpad:

https://launchpad.net/gcc-arm-embedded/4.8/4.8-2014-q3-update/+download/gcc-arm-none-eabi-4_8-2014q3-20140805-linux.tar.bz2

To install, bunzip2, untar, and add the bin subdirectory to your PATH.

#### Go

Go is the language in which Manifesto (see below) is written.

````
sudo apt-get install git golang
mkdir $HOME/go
export GOPATH=$HOME/go
````

#### Manifesto

Manifesto is a tool used to build human-readable module manifest files into their on-wire binary representation as defined within the Greybus Specification. These binaries are incorporated into the firmware images.

````
cd $HOME
go get code.google.com/p/gcfg
git clone https://github.com/ohporter/manifesto
cd manifesto
go build manifesto.go
export PATH=$PATH:$HOME/manifesto/
````

#### JTAG Programming and Debugging

These instructions describe how to use the [JLink Pro JTAG programmer](http://www.segger.com/jlink-pro.html) from Segger for use with BDB1B. Software can be obtained from the [downloads](http://www.segger.com/jlink-software.html) section on the Segger website. It launches a GDB server that can be connected to using ‘arm-none-eabi-gdb’ included with the toolchain above.

For a build machine running Ubuntu, you would download the DEB Installer for the Software And Documentation Pack for Linux, the 64-bit version if you have a 64-bit Ubuntu installation, and the 32-bit version if you have a legacy 32-bit installation.  If uncertain of the Ubuntu installation type, run “uname -m” on your build machine.  An output of “x86_64” indicates that you have a 64-bit install.  The download from Segger will typically open in the Ubuntu Software Center, and offer the option to Install the package, which should be accepted.

#### Next Steps

Now that your machine is set up, you can proceed to the [Firmware Build](Firmware-Build).
