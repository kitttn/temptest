####Building NuttX firmware images

This page describes how to build firmware images for the AP or GP bridges, and the SVC. There are some important differences between the AP/GP bridge firmware and the SVC firmware, however the build process is essentially the same.

There are two different ways to build the firmware: 
* Select an existing configuration (specify a defconfig) 
  Recommended if you're unfamiliar with the plethora of configuration options
* Generate a custom configuration (make menuconfig)
  Use this if you need to configure certain runtime or debugging scenarios, or 
  enable specific functionality.
 
The instructions assume you have cloned the nuttx and other related repositories into your $HOME directory,
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

####[Build using a defconfig file](Build-using-a-defconfig-file)
####[Build using a custom configuration file](Build-using-a-custom-configuration-file)
