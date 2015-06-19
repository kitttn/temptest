####NuttX
Project Ara firmware is based on the
[NuttX RTOS](http://www.nuttx.org).
<!-- 
While this provides us with a number of useful features and a useful
base of code to build on, in general we would like to minimize the
number of NuttX interfaces that we rely on. The quality of the code base
is somewhat lacking; there is only a single author, the commits are
loose and fast, there is an insane amount of duplicated code, and we
have not reviewed or tested many of the code paths or features. In
general we would like the ability to migrate our code base off of this
RTOS if we find that NuttX does not provide us the interfaces we need,
or if we find any horrible bugs that waste our debugging time.
-->

####Git repositories
Read access is public, push access authorization is per GitHub user. SSH and two-factor authentication are required for push.

#####Firmware source
[https://github.com/projectara/nuttx](https://github.com/projectara/nuttx)  

#####'manifesto' Manifest generator
[https://github.com/projectara/manifesto](https://github.com/projectara/manifesto)  

#####'flashrom' SPI ROM programming tool
[https://github.com/projectara/flashrom](https://github.com/projectara/flashrom)  

#####Greybus specification
[https://github.com/projectara/greybus-spec](https://github.com/projectara/greybus-spec)  

####Default Repository Installation Path

Throughout the wiki, it is assumed you have cloned the nuttx and related repositories into your $HOME directory,
i.e. 
```
$HOME
   +--nuttx
   +--flashrom
   +--manifesto  
   ... etc
```

If you install the repositories in a different location, you'll need to modify the paths and commands accordingly.