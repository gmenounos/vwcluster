# Odometer

The odometer is stored in 8 consecutive blocks of 2 bytes each (least signifiant byte first). Each block is initialized at the factory to $FFFF (65535). After the car travels its first 2 km, the first block is decremented by 1. After the second 2 km, the second block is decremented by 1—and so on up to block 8. Then it repeats the cycle beginning with block 1.

```
ffff ffff ffff ffff ffff ffff ffff ffff (0 km)
feff ffff ffff ffff ffff ffff ffff ffff (2 km)
feff feff ffff ffff ffff ffff ffff ffff (4 km)
feff feff feff ffff ffff ffff ffff ffff (6 km)
feff feff feff feff ffff ffff ffff ffff (8 km)
feff feff feff feff feff ffff ffff ffff (10 km)
feff feff feff feff feff feff ffff ffff (12 km)
feff feff feff feff feff feff feff ffff (14 km)
feff feff feff feff feff feff feff feff (16 km)
fdff feff feff feff feff feff feff feff (18 km)
fdff fdff feff feff feff feff feff feff (20 km)
etc.
```

This uses 128 bits to effectively store a 19-bit odometer value. Presumably this is to reduce writes to the EEPROM, which may have a limited number of write cycles, as well as to provide protection against EEPROM corruption. Because the counters are decremented in a regular pattern, it's easy to detect a random byte change in any of the odometer EEPROM locations.

Since the odometer is 19 bits, it can only store values from 0 to 2*(2^19-1) (1048574) kilometers. However, since the odometer display is limited to 6 digits, the software in the cluster stops counting at 999998 km.

Even though the EEPROM can only store odometer values with a resolution of 2 km, the cluster displays odometer values in 1 km increments. This is done by updating the value in cluster RAM and on the display every 1 km and updating the EEPROM every 2 km. The cluster RAM maintains its contents as long as the vehicle battery is not disconnected. If the cluster does lose power, when power is restored the odometer will always display an even number of kilometers.

When displaying the odometer in miles, the cluster uses a slightly incorrect conversion factor of 1.609375[^1] (presumably for ease of binary arithmetic since 1.609375 = 103/64). Thus the maximum number of miles that the cluster will display is 999998 / 1.609375 = 621358.

[^1]: The correct conversion factor is 1.609344.