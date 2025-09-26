# Needle Sweep Patches

Since these VDO clusters contain a [memory patch module](../PatchModule.md), it's possible to load a small program into the EEPROM that will sweep the tachometer and speedometer needles quickly from min to max and back when the cluster is first powered up. This effect is sometimes called "staging" or "ceremony".

## Bodie

The first needle sweep patches were written by Bodie Royle and made available on Graeme's Webspace for the following [ROM versions](../RomVersion.md):

- [VWK501MH-01.00](https://www.graeme86.com/sweep501mh/sweep501mh.html)
- [VWK501MH-01.10](https://www.graeme86.com/sweep501mh/sweep501mh.html)
- [VWK501MH-00.92](https://www.graeme86.com/sweep501mh/sweep501mh.html)
- [VWK503MH-09.00](https://www.graeme86.com/sweep503mh/sweep503mh.html)

## Feche

Several other needle/LED sweep patches have been independently developed by Feche and can be found on [his web site](https://feche.ar/).

## BitFab

I have also developed some needle sweep patches. Mine are based on Bodie's patches, but with memory addresses updated to support other ROMS.

**Note** that there is some danger to installing a patch. At best it will simply not work. At worst your cluster will fail to boot up and you will be unable to repair it without taking it apart and using an EEPROM programmer to restore it from a backup.

Before installing a patch, verify your [ROM version](../RomVersion.md) with the kw1281test ReadSoftwareVersion command. If your ROM version does not *exactly* match the ROM version of the patch, do not go any further!

Next, back up your cluster's EEPROM so that if something goes wrong, you can restore it.

```
kw1281test COM1 10400 17 DumpEeprom 0 2048 ClusterBackup.bin
```

Next, download the patch and load it into the cluster with the kw1281test LoadEeprom command. Note that different ROMs have different EEPROM addresses where the patch needs to be loaded. Each section below contains a download link for the patch and the kw1281test command to load the patch, including the correct EEPROM address. You may need to change "COM1" to the appropriate COM port for you KKL cable.

Finally, reset the cluster with the kw1281test Reset command:

```
kw1281test COM1 10400 17 Reset
```

Then turn the ignition key off, wait 30 seconds, turn it on again and hopefully you'll see the needles sweep.

### KB5M07HH-09.00

[NeedleSweep-KB5M07HH-09.00.bin](./NeedleSweep-KB5M07HH-09.00.bin)

```
kw1281test COM1 10400 17 LoadEeprom 0x4F6 NeedleSweep-KB5M07HH-09.00.bin
```

### VSQX01LM-01.00

[NeedleSweep-VSQX01LM-01.00.bin](./NeedleSweep-VSQX01LM-01.00.bin)

```
kw1281test COM1 10400 17 LoadEeprom 0x552 NeedleSweep-VSQX01LM-01.00.bin
```

### VWK501MH-00.88

[NeedleSweep-VWK501MH-00.88.bin](./NeedleSweep-VWK501MH-00.88.bin)

```
kw1281test COM1 10400 17 LoadEeprom 0x4F4 NeedleSweep-VWK501MH-00.88.bin
```

### VWK502MH-09.00

[NeedleSweep-VWK502MH-09.00.bin](./NeedleSweep-VWK502MH-09.00.bin)

```
kw1281test COM1 10400 17 LoadEeprom 0x4F4 NeedleSweep-VWK502MH-09.00.bin
```

### VWK503LL-09.00

[NeedleSweep-VWK503LL-09.00.bin](./NeedleSweep-VWK503LL-09.00.bin)

```
kw1281test COM1 10400 17 LoadEeprom 0x4F6 NeedleSweep-VWK503LL-09.00.bin
```
