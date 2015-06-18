The touchscreen demo currently requires NuttX firmware from Alexandre
Bailon’s “touchscreen” branch:

[https://github.com/anobli/nuttx/tree/touchscreen](https://www.google.com/url?q=https%3A%2F%2Fgithub.com%2Fanobli%2Fnuttx%2Ftree%2Ftouchscreen&sa=D&sntz=1&usg=AFQjCNFlgYEvzL6nrtgYT_TtdiwSIjPnFA)

Instructions to build the demo firmware images are in this section. Be
sure to run “make distclean” from the nuttx/ directory each time you
change configuration!

First, cd nuttx from the top-level source tree checkout.

To build the image for the Marvell AP module AP Bridge:

        (cd tools; ./configure.sh ara/lgd/apb1)
        make

The resulting “nuttx.bin” image is ready to run out of SPI ROM. Flash it
to the Marvell AP module AP bridge using the SPI ROM programming fixture
and [these
instructions](https://docs.google.com/document/d/1d68W2nLDZwh282D2T9Nowak4H8B4dWgzJwvWEz82XNE/edit#heading=h.8nx43qmfs4tz).

To build the image for the LG display module AP Bridge, first run make
distclean, then

        (cd tools; ./configure.sh ara/lgd/apb2)
        make

The resulting nuttx.bin image is also ready to run out of SPI ROM, and
can be flashed to the LG display module AP bridge using the SPI ROM
programming fixture and [these
instructions](https://docs.google.com/document/d/1d68W2nLDZwh282D2T9Nowak4H8B4dWgzJwvWEz82XNE/edit#heading=h.8nx43qmfs4tz).

For use with BDB1B, [instructions (with pictures) for how to connect the
panel to APB2 on the BDB1B are
here](https://docs.google.com/a/projectara.com/document/d/1YDJbBpohPDCQqdfWjO3gRTrTg4M4enilLKFhjUMs50s/edit#heading=h.n5fz9s31tygp).

To build the image for the SVC on the endo, first run make distclean,
then
        (cd tools; ./configure.sh endo/svc)
        make

Then [upload the “nuttx” (NOT nuttx.bin) ELF image to the SVC built-in
flash via
JTAG](https://docs.google.com/document/d/1d68W2nLDZwh282D2T9Nowak4H8B4dWgzJwvWEz82XNE/edit#heading=h.4n54ifb74ddc).

To build the image for the SVC on the BDB1B, first run make distclean,
then

        (cd tools; ./configure.sh ara/svc)
        make menuconfig
        Board Selection  ---\>
          Select a route config (APB1 to APB2)  ---\>
   ( ) APB1 to APB2
   (X) AP Module to APB2
        make

Then [upload the “nuttx” (NOT nuttx.bin) ELF image to the SVC built-in
flash via JTAG](https://docs.google.com/document/d/1d68W2nLDZwh282D2T9Nowak4H8B4dWgzJwvWEz82XNE/edit#heading=h.4n54ifb74ddc).

To build Android images, please refer to [Ara Android Build
Instructions](https://docs.google.com/a/projectara.com/document/d/14ernk1psEHG7q6uPNuwmf7aiWc8yQ7QUY4lHURp9yug/edit#heading=h.2mjf5mdfcduf).

Note: Before to build android, we have to apply some
[patches](https://drive.google.com/a/projectara.com/folderview?id=0ByBwHSpgwUOjfkhpSG1hYXNsTVdzSWNCVUZIazAyeDFWSEVMLW51VWdVT05rWFE5MVo0SlU&usp=sharing) to
make the touchscreen demo work.

  cd mydroid-ara
  cd kernel/mrvl-3.1\
  git am 0001-Revert-video-mmp-clean-up-lg4892-driver.patch
  git am 0002-aps\_ts-set-info-irq-to-valid-irq-number.patch
  git am 0003-lg4892-magic-fix-to-have-display.patch
  cd external/greybus
  git am 0001-greybus-i2c-gb-add-support-to-instantiate-an-M-2xxx-.patch
  cd ../../device/ara/common
  git am 0001-greybus-add-a-script-to-load-greybus-modules.patch
  cd ../..

###To flash the Marvell AP
        fastboot flash bootloader u-boot.bin
        fastboot reboot
        fastboot oem format
        fastboot flash boot boot.img
        fastboot flash dtb pxa1928-ara.dtb
        fastboot flash system system.img
        fastboot flash vendor vendor.img
        fastboot flash userdata userdata.img
        fastboot flash cache cache.img

###Known issues
-   The USB connector of AP module must be plug to a computer (only a
    computer, not usb power supply), whatever you are working on BDB1B
    or endo.\
    If you are working with a form factor module, you will have to do
    hole in AP module covert to have access to USB port.
-   Always have USB plugged will force u-boot to start fastboot, then
    you have to run every time you are booting android “fastboot
    continue”. \
    Sometime, you can plug USB on hub (hub must be plugged on compter),
    u-boot will continue to boot without running fastboot (may not work,
    depends on hub).
-   USB connector of USB module (endo) or power supply (BDB1B) must be
    plug after USB connector on AP module.
-   Once display is working, you can remove USB.
-   Firmwares doesn’t handle reset. You must always switch on / off the
    power.
-   There are mechanicals issue on endo with LCD module. A workaround is
    to put scotch (several layers) on top border of the module.
-   LCD module is hard to position in endo. If you have JTAG, you can
    use look the color of up arrow LED: red mean LCD module is powered,
    green mean LCD module is powered (endo must be powered by USB
    module).\
    You can also use the LED on endo: util the LED is green, your
    modules are not positioned correctly (LCD or AP modules).
-   Sometime, the AP module dosn’t start at all. Unplug USB from AP
    module, unplug the power supply, wait few seconds and retry.
-   UART console must be disabled (default) for LCD module because there
    is no UART on module, otherwise, will not work.
-   Some touchpanel (LCD module or LCD panel on BDB1B) doesn’t work.
    They are generating some random touch events and / or have the half
    bottom of panel that almost never generate touch event when we are
    touching it.
-   Volume and power keys are not supported
