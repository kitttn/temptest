This page contains various issues you may encounter with the hardware, along with tips and tricks to resolve.

####AP Module Power-on Sequence

Hook the debug board up, and hit the ON\_KEY button to power the AP
Module from VCONN.

To get Unipro linkup:

1.  Remove power
2.  Hold SVC in reset
3.  Hit ON\_KEY on the debug module to turn on AP Module
4.  Release SVC reset

####APBridge1 HUB power issue

Issue on BDB1B, BDB2A, BDB2B.

HUB oscillator is powered by a GPB1 power line.
If GPB1 is not powered, then HUB will not work:
* HUB does not enumerate
* HUB disconnects
* HUB removes the HSIC device

To keep APB1 USB HUB working: never power off GPB1.

####APBridge1 HUB (power?) issue

Issue on BDB2A, BDB2B with debug board rev b.

A debug board can disturb APBridge1 HUB.
If the issue happen and APBridge1 can not workaround it then HUB does not enumerate correctly.
The issue is caused by UART TX (from debug board). This signal seems to inject power in board even if the power was removed. Then, even without power, +VAPB1_1P8_IO voltage is almost 1V.
+VAPB1_1P8_IO is used to power the HUB and I suspect this voltage keep HUB in bad state causing enumeration issue.

Workaround:
* Remove debug board or Unplug and plug debug board on BDB power cycles
* Plug USB cable only few seconds after (let time to APBridge1 to reset HUB).
* Add resistor between UART signals from debug board and BDB (>= 10K)