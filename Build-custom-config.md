This page describes how to build the firmware by generating a custom configuration file, .config.

- [STEP 1. Select a defconfig to start from](#step-1-select-a-defconfig-to-start-from)
- [STEP 2. Make your changes](#step-2-make-your-changes)
- [STEP 3. Build and debug firmware images in-tree](#step-3-build-and-debug-firmware-images-in-tree)
- [STEP 4. (Optional) Save your config](#step-4-optional-save-your-config)

## STEP 1. Select a defconfig to start from

**IMPORTANT: If you have run this step before, you must run `make distclean` from the `nuttx/` directory before using a new `<configs-subdir>`. If you don't, the resulting firmware images can be subtly corrupted.**

First, select the defconfig you want to base your changes on:

```
cd nuttx/tools
./configure.sh <configs-subdir>
cd ..
```

where `<configs-subdir>` is the relative path to the desired configuration, versus the assumed base 
path of the `nuttx/configs` subdirectory of the nuttx repository. Here's a picture of where the different `<configs-subdir>s` are kept: 

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

## STEP 2. Make your changes

Make any changes to the .config from the base defconfig that you want. To start a terminal-based program for making .config changes, run

`make menuconfig`

Then use its menus to select custom options and set custom values. When finished, Save and Exit.

## STEP 3. Build and debug firmware images in-tree

Just run make (**"make -j N" does not work**):

```
make
```

The resulting images files are placed under ./nuttx:  
`./nuttx/nuttx.bin` - raw binary  
`./nuttx/nuttx    ` - ELF (includes debug info)

[Flash your image](Flashing-images) to your target and [debug](Debugging) it.

## STEP 4. (Optional) Save your config

If you are happy with your configuration, you can save your work so that it can be built as a defconfig.

To do so:

1. Make a new subdirectory under `nuttx/nuttx/configs/ara/bridge` or `nuttx/nuttx/configs/ara/svc`, depending on if the build target is a Toshiba bridge ASIC or an SVC.

2. Copy the `.config` into a file named `defconfig` in that directory.

3. Make a `Make.defs` file with any other build system changes you need. You can use the same Make.defs from the `<configs-subdir>` for the defconfig you based your `.config` on to start with.

4. Add any other README or documentation files to the subdirectory containing your new `defconfig` and `Make.defs` files.

Here are some examples from the existing configurations.

```
nuttx/nuttx/configs/ara/bridge
├── es2-debug-apbridgea
│   ├── defconfig
│   └── Make.defs
├── es2-debug-bringup
│   ├── defconfig
│   └── Make.defs
├── es2-debug-generic
│   ├── defconfig
└   └── Make.defs
nuttx/nuttx/configs/ara/svc
├── bdb2a
│   ├── defconfig
│   └── Make.defs
├── sdb
│   ├── defconfig
│   ├── Make.defs
│   ├── svc-bridge-pins.txt
└   └── svc-sma-daughter-board-pins.txt
```