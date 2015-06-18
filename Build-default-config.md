This page describes how to build a firmware image by specifying a default configuration, or 'defconfig'. The defconfig is passed as a parameter to a script that builds the image out-of-tree, thus preserving the state of your working copy. 

####Default configuration files
As of this writing, the available defconfigs are:

Path to defconfig                                         | Description
----------------------------------------------------------|-------------------------------
./nuttx/configs/ara/bridge/es2-debug-apbridgea/defconfig  | AP Bridge 'A' (connects to AP)
./nuttx/configs/ara/bridge/es2-debug-generic/defconfig    | AP or GP Bridge
./nuttx/configs/ara/svc/bdb2a/defconfig                   | SVC
<~--
./nuttx/configs/ara/svc/bdb1b/defconfig                   | BDB1B, do not use
./nuttx/configs/ara/bridge/es1-debug-generic/defconfig    | BDB1B, do not use
./nuttx/configs/ara/bridge/es1-debug-apbridgea/defconfig  | BDB1B, do not use
./nuttx/configs/ara/lgd/apb1/defconfig                    |
./nuttx/configs/ara/lgd/apb2/defconfig                    |
-->
To build a firmware image using a default configuration:
```
cd $HOME/nuttx
./build_ara_image.sh ara \<config-name\>
```
Where \<config-name\> is the relative path to the defconfig file, assuming the base path of ./nuttx/configs/ara.
When the build script completes successfully, the resulting image files (binary and ELF) are placed in  
./build/\<name-of-build\>/images/  
Where "name-of-build" is the path to the defconfig, with the slashes replaced with dashes.

Example 1:
```
./build_ara_image.sh  ara svc/bdb2a
```
This builds the SVC firmware for the BDB2A
Image files are here:
```
./build/ara-svc-bdb2a/image/
```

Example 2:
```
./build_ara_image.sh  ara bridge/es2-debug-apbridgea
```
This builds a debuggable image for the AP bridge on the BDB2A (which is based on ES2 silicon).
Image files are here:
```
./build/ara-bridge-es1-debug-apbridgea/image/
```
