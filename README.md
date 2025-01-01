# VW MKIV Instrument Clusters
This repository is about reverse engineering and internals of Volkswagen MKIV Golf and Jetta instrument clusters, specifically those made by VDO. Some of these clusters are nearly identical to those used in the Passat W8. These vehicles were produced in the late 1990's and early 2000's.

Unless otherwise noted, the following information applies to the Passat W8 FIS (full height red LCD display) cluster (VW part number 3B7 920 946E) running VWK501 software.

## Hardware
The cluster consists of a main board and a small daughterboard that controls the LCD. The main board is powered by a Micronas CDC16xxF-E microcontroller which uses the 65C816 instruction set. It has 6KB of internal RAM and a bit more than 128KB of internal ROM.

Because the microcontroller can only directly address 64KB of memory, a bank switching scheme is used. By loading a byte into the Alternative Banking Register (ABR, address $1F0F), different banks of memory will be mapped into the upper 32K of address space. Bank 0 is always mapped into the lower 32K of address space.

Bank 0 consists of 6KB RAM, 2KB microcontroller registers and 24KB ROM. Banks 1-4 are additional 32KB ROM banks.

Assuming your KKL cable is attached to serial port COM4, here is how to dump the 5 banks of ROM using the [KW1281Test tool](https://github.com/gmenounos/kw1281test):

```cmd
.\kw1281test.exe COM4 10400 17 DumpMem 0x02000 0x6000
.\kw1281test.exe COM4 10400 17 DumpMem 0x18000 0x8000
.\kw1281test.exe COM4 10400 17 DumpMem 0x28000 0x8000
.\kw1281test.exe COM4 10400 17 DumpMem 0x38000 0x8000
.\kw1281test.exe COM4 10400 17 DumpMem 0x48000 0x4000
```
This will create 5 files, which can be concatenated and then used to create a [6502bench SourceGen](https://6502bench.com/) project:

#### Windows Cmd
```cmd
copy /b cluster_mem_$002000.bin + cluster_mem_$018000.bin + cluster_mem_$028000.bin + cluster_mem_$038000.bin + cluster_mem_$048000.bin cluster_rom.bin
```

#### Linux/macOS
```shell
cat cluster_mem_\$002000.bin cluster_mem_\$018000.bin cluster_mem_\$028000.bin cluster_mem_\$038000.bin cluster_mem_\$048000.bin > cluster_rom.bin
```

## Credits
Protocol Info: https://www.blafusel.de/obd/obd2_kw1281.html<br/>
VW Radio Reverse Engineering Info: https://github.com/mnaberez/vwradio<br/>
6502bench SourceGen: https://6502bench.com/
