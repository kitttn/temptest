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