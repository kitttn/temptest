This page describes how to build the firmware by generating a custom configuration file, .config.

First, nuttx is set up to build in-tree using a .config that matches an existing [defconfig](Build-default-config). Then, `make menuconfig` is run, and the user modifies the configuration as required.  On completion, the generated configuration file is in `nuttx/.config`, relative to the root directory of the nuttx repository.

After the configuration step is complete, the firmware is built in-tree by running make.

## STEP 1. run configuration script

```
cd nuttx/tools
./configure.sh <configs-subdir>
cd ..
```

where `<configs-subdir>` is the relative path to the desired configuration, versus the assumed base 
path of the `nuttx/configs` subdirectory of the nuttx repository:

```
nuttx
├── apps
│   ├── [...]
├── misc
│   ├── [...]
├── nuttx
│   ├── [...]
│   ├── configs
│   │   ├── ara     <----- <configs-subdir> can be any ara/foo/bar/
                           you find here that contains a "defconfig"
                           file.
│   │   └── [...]
└── NxWidgets
    └── [...]
```

This step sets up the environment and copies some of the configuration files to ./nuttx so the build system can use them.

Some important `<configs-subdir>` values are:

- ara/bridge/es2-debug-apbridgea - [APBridgeA](Hardware-Overview#toshiba-bridge-asics)
- ara/bridge/es2-debug-generic   - A "generic" image suitable for any [APBridgeE or GPBridge](Hardware-Overview#toshiba-bridge-asics), which exposes various Greybus protocols to the AP in its interface manifest.
- ara/svc/bdb2a - SVC firmware for BDB2
- ara/svc/sdb - SVC firmware for SDB

## STEP 2. run 'make menuconfig'

`make menuconfig`

Use the config menu system to select custom options.
When finished, Save and Exit.

## STEP 3. Rebuild the image (in tree):

Just run make (**"make -j N" does not work**):

```
make
```

The resulting images files are placed under ./nuttx:  
`./nuttx/nuttx.bin` - raw binary  
`./nuttx/nuttx    ` - ELF (includes debug info)

