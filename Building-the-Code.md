####Building NuttX firmware images

This page describes how to build firmware images for the AP bridges, GP bridges, and SVC. There are some significant differences between the AP/GP bridge firmware and the SVC firmware, but the build process is  the same for each.

Build instructions assume you have cloned the nuttx and other related repositories into your $HOME directory,
i.e. 
```
$HOME
   +--nuttx
   +--flashrom
   +--manifesto  
   ... etc
```

####Firmware build configuration files
As of this writing, the available defconfigs are:
./nuttx/configs/ara/bridge/es2-debug-apbridgea/defconfig  
./nuttx/configs/ara/bridge/es1-debug-apbridgea/defconfig  
./nuttx/configs/ara/bridge/es2-debug-generic/defconfig  
./nuttx/configs/ara/bridge/es1-debug-generic/defconfig  
./nuttx/configs/ara/lgd/apb1/defconfig  
./nuttx/configs/ara/lgd/apb2/defconfig  
./nuttx/configs/ara/svc/bdb2a/defconfig  
./nuttx/configs/ara/svc/bdb1b/defconfig  

There are two distinct build methods: 
- [Build using a default config or 'defconfig'](Build-default-config)
- [Build using a custom config](Build-custom-config)
  
In the [defconfig](Build-default-config) method, you run a 
build script and specify an existing default configuration. This is 
recommended if you're just getting started, or are otherwise unfamiliar with the 
plethora of configuration options. A side benefit of this method is 
that it builds out-of-tree, i.e. the repository is not "dirtied".
To build using this method, see [this page](Build-default-config).

In the [custom config](Build-custom-config) method, 
you generate a custom configuration using 'make menuconfig'. This is 
required if you need to configure certain runtime or debugging scenarios, or
enable specific driver functionality or other options. To build using a 
custom configuration, see [this page](Build-custom-config).  



