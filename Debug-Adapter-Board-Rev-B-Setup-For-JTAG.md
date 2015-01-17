To enable JTAG debugging via a Debug Adapter Board Rev B, there are two essential prerequisites:

1. A jumper must be present between pins 1 and 2 of the header labeled JP15, located between CON2 (CPU) and CON3 (Bridge).  This provides power to the JTAG interface.  Pin 1 is denoted by a round solid dot on the board.

2. The switch labeled SW5 (RST_40uS) must be be positioned **away** from the SW5 label, towards the labels 3 and 6 on the board.  This allows the ARM processor in the bridge chip to run: otherwise, it's held in reset and JTAG debugging won't be possible.
