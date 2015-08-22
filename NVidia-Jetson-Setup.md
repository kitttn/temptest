This page explains how to use the NVidia Jetson development board as an AP dev board for Project Ara.

### Step 1. Set up UART console

Jetson has a 9-pin serial port which is used for the u-boot console, boot log, and kernel console. Connect a 9-pin female-female null modem and USB serial adapter. Run a terminal program on the host at 115200 baud, 8n1. The Jetson serial port is circled in orange in the following image. Click the image for a larger version.

<a href="http://releases-ara-mdk.linaro.org/static/wiki-images/Ports.jpg"><img src="http://releases-ara-mdk.linaro.org/static/wiki-images/Ports.jpg" width="640">

### Step 2. Flash Jetson with base Android image

The Android image is part of the "Android_for_Jetson NVFlash Package". Flash Jetson with the Android image by following the "Instructions for using the NVflash package" section on [this page](https://github.com/projectara/Android-wiki/wiki/Getting-Started-with-the-Jetson-reference-platform#instructions-for-using-the-nvflash-package)

### Step 3. Reflash Jetson with Greybus support 

Rebuild the Jetson kernel and Greybus modules, and update the Jetson with newboot.img, by following the steps on [this page](https://github.com/projectara/Android-wiki/wiki/Kernel-Only-Build-Instructions-for-Jetson-reference-platform).
