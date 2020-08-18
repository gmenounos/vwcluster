# VW MKIV Instrument Clusters

This repository is about reverse engineering and internals of Volkswagen MKIV Golf and Jetta instrument clusters, specifically those made by VDO. Some of these clusters are nearly identical to those used in the Passat W8. These vehicles were produced in the late 1990's and early 2000's.

Unless otherwise noted, the following information applies to the Passat W8 FIS (full height red LCD display) cluster.

##### Hardware

The cluster consists of a main board and a small daughterboard that controls the LCD. The main board is powered by a Micronas CDC16xxF-E microcontroller which uses the 65C816 instruction set. It has 6KB of internal RAM and a bit more than 128KB of internal ROM.

Here is how to dump the 5 banks of ROM using the [KW1281Test tool](https://github.com/gmenounos/kw1281test):

KW1281Test COM4 10400 17 DumpMem $02000 $6000
KW1281Test COM4 10400 17 DumpMem $18000 $8000
KW1281Test COM4 10400 17 DumpMem $28000 $8000
KW1281Test COM4 10400 17 DumpMem $38000 $8000
KW1281Test COM4 10400 17 DumpMem $48000 $4000

##### Credits
Protocol Info: https://www.blafusel.de/obd/obd2_kw1281.html
VW Radio Reverse Engineering Info: https://github.com/mnaberez/vwradio
6502bench SourceGen: https://6502bench.com/
