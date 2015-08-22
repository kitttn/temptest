While there are some differences between the AP/GP bridge firmware and the SVC firmware, the basic build process is the same for all. The output of the build is a binary firmware image that can be loaded and run on the microcontroller, along with an ELF format file that contains symbolic debugging information.

These build instructions assume you have cloned the nuttx and related repositories into your $HOME directory,
i.e. 
```
$HOME
   +--nuttx
   +--flashrom
   +--manifesto  
   ... etc
```

If you've installed the repositories in another folder, you'll need to modify the paths and commands accordingly.

Project Ara firmware builds are configured using the same [KConfig mechanism used for Linux](http://www.linuxjournal.com/article/6568).

There are two distinct build methods: 
- [Build using a default config or 'defconfig'](Build-default-config)
- [Build using a custom .config](Build-custom-config)
 
When [building using a defconfig](Build-default-config), you run a 
build script and specify an existing default configuration. This is 
recommended if you're just getting started, or are otherwise unfamiliar with the 
plethora of configuration options. A side benefit of this method is 
that it builds out-of-tree, i.e. the repository is not "dirtied".

When [building using a custom .config](Build-custom-config) method, 
you generate a custom firmware configuration, optionally by basing it on an existing defconfig. This is 
required if you need to configure certain runtime or debugging scenarios, or
enable specific driver functionality or other options. To build using a 
custom configuration, see [this page](Build-custom-config).  



