# ROM Version

Near the beginning of the ROM (address $2100 for VWK501 clusters), there is a 10-byte block that identifies the exact ROM version. The first 8-bytes are ASCII (e.g. "VWK501MH") and the last 2 are numeric (e.g. $10 $01). The last 2 bytes are typically printed on the cluster label in reverse order (e.g. $10 $01 would be printed as "01.10"). If the label is missing, the [kw1281test](https://github.com/gmenounos/kw1281test) ReadSoftwareVersion command can be used to display the version:

```
.\kw1281test.exe COM2 10400 17 ReadSoftwareVersion
...
ECU: 3B7920946E  KOMBI+WEGFAHRSP VDO V09
00: VWK501MH $10 $01
01: $81 $19
02: $18 $08 $01 $0B $1C $09
03: VW/Sk MH Serie/Flash
```
