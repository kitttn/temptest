This page describes how to build a firmware image by specifying a default configuration, or 'defconfig'. The defconfig is passed as a parameter to a script that builds the image out-of-tree, thus preserving the state of your working copy.

- [Default configuration files (defconfigs)](#default-configuration-files-defconfigs)
- [Building a defconfig](#building-a-defconfig)
- [Building all defconfigs](#building-all-defconfigs)
- [Examples](#examples)

## Default configuration files (defconfigs)

As of this writing, the available defconfigs are as follows.

Location of defconfig relative to ./nuttx/configs/ara/   | Description
---------------------------------------------------------|-------------------------------
bridge/es2-debug-apbridgea/defconfig                     | APBridgeA for debugging use
bridge/es2-debug-bringup/defconfig                       | Use only during board bringup
bridge/es2-debug-generic/defconfig                       | "Generic" base image for non-APBridgeA
svc/bdb2a/defconfig                                      | SVC for BDB2A or BDB2B
svc/sdb/defconfig                                        | SVC for SDB

## Building a defconfig

To build a firmware image using a default configuration:
```
cd $HOME/nuttx
./build_ara_image.sh ara <config-name>  
```
Where \<config-name\> is the relative path to the defconfig file, assuming the base path of 
```
./nuttx/configs/ara  
```

When the build script completes successfully, the resulting image files (binary and ELF) are placed along with a symbol able (System.map) in  

```  
./build/<name-of-build>/image/  
```

Where \<name-of-build\> is the defconfig relative path, with the slashes replaced with dashes.

## Building all defconfigs

To build all Project Ara firmware images:

```
cd $HOME/nuttx
./build_ara_image.sh all
```

All Project Ara defconfigs will be built. Output images will be in `build/*/image/`.

## Examples

Example 1:
```
./build_ara_image.sh  ara svc/bdb2a
```

This builds the SVC firmware for BDB2. The build outputs are:

```
./build/ara-svc-bdb2a/image/nuttx      # ELF file
./build/ara-svc-bdb2a/image/nuttx.bin  # Raw binary
./build/ara-svc-bdb2a/image/System.map # Symbol table
```

Example 2:

```
./build_ara_image.sh  ara bridge/es2-debug-apbridgea
```

This builds a debuggable image for the APBridgeA for ES2 bridge ASICs, which will work on either BDB2 or SDB. The build outputs are:

```
./build/ara-bridge-es2-debug-apbridgea/image/nuttx      # ELF file
./build/ara-bridge-es2-debug-apbridgea/image/nuttx.bin  # Raw binary
./build/ara-bridge-es2-debug-apbridgea/image/System.map # Symbol table
```
