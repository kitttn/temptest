This page contains various issues you may encounter with the hardware, along with tips and tricks to resolve.

- ES1 AP Module
  - [Power-on Sequence](#power-on-sequence)
- BDB
  - [APBridge1 HUB oscillator power issue](#apb1-hub-oscillator-power-issue)
  - [APBridge1 HUB (power?) issue](#apb1-hub-power-issue)
- SDB
  - (None known as of this writing)

## ES1 AP Module

### Power-on Sequence

Hook the debug board up, and hit the ON\_KEY button to power the AP
Module from VCONN.

To get Unipro linkup:

1.  Remove power
2.  Hold SVC in reset
3.  Hit ON\_KEY on the debug module to turn on AP Module
4.  Release SVC reset

## BDB

### APB1 hub oscillator power issue

Issue on BDB1B, BDB2A, BDB2B.

HUB oscillator is powered by a GPB1 power line.
If GPB1 is not powered, then HUB will not work:
* HUB does not enumerate
* HUB disconnects
* HUB removes the HSIC device

To keep APB1 USB HUB working: never power off GPB1.

### APB1 hub (power?) issue

Issue on BDB2A, BDB2B with debug board rev b.

A debug board can disturb APB1's USB hub.

If the issue happens and APB1 can not work-around it, then the hub does not enumerate correctly.

The issue is caused by UART TX (from debug board). This signal seems to inject power in board even if the power was removed. Then, even without power applied, +VAPB1_1P8_IO voltage is almost 1V.

+VAPB1_1P8_IO is used to power the hub and I (Alexandre Bailon) suspect this voltage keep HUB in bad state causing enumeration issue.

Workaround:

* Remove debug board or Unplug and plug debug board on BDB power cycles
* Plug USB cable only few seconds after (let time to APBridge1 to reset HUB).
* Add resistor between UART signals from debug board and BDB (>= 10K)

## SDB

None known at this time.
