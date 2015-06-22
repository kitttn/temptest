This page describes how to build the firmware by generating a custom configuration. The tree is set up to build from a defconfig using a script, then 'make menuconfig' is run, and the user modifies the configuration as required.  On completion, the generated configuration file is at ./nuttx/.config. 

After the configuration step is complete, the firmware is built in-tree by running make.

#####STEP 1. run configuration script
```
cd nuttx/tools
./configure.sh <configs subdir>
cd ..
```
where \<configs subdir\> is the relative path to the desired configuration, versus the assumed base 
path of ./nuttx/nuttx/configs.  

This step sets up the environment and copies some of the configuration files to ./nuttx so they are available to the make utility.

Examples:                
- ara/bridge/es2-debug-apbridgea - AP bridge A
- ara/bridge/es2-debug-generic   - AP bridge 2
- ara/svc/bdb2a - SVC for BDB2

#####STEP 2. run 'make menuconfig'

`make menuconfig`

Use the config menu system to select custom options.
When finished, Save and Exit.

#####STEP 3. Rebuild the image (in tree):
```
make
```
The resulting images files are placed under ./nuttx:  
`./nuttx/nuttx.bin` - raw binary  
`./nuttx/nuttx    ` - ELF (includes debug info)

