# Checksums

The EEPROM areas containing the k-value / "Distance Impulse Number" (i.e. number of pulses from the transmission per mile) and the speedometer map are protected by a checksum. The k-value can be modified via cluster soft coding but that only allows selecting between 4 (sometimes 6) values that are burned into the cluster ROM. If nonstandard tires are installed on the car, it will be necessary to adjust the k-value and maybe the speedometer map so that the odometer and speedometer read correctly, but unless a proper checksum is calculated, the cluster will display a "dEF" error.

Here are the addresses and lengths of the checksum and 2 checksum-protected regions, as well as important addresses in those regions:

| Region (Length)     | VWK501 Address | VWK503 Address |
|---------------------|----------------|----------------|
| Checksum (1)        | $14E           | $190           |
| Region 1 (8)        | $14F           | $191           |
| Region 2 ($20)      | $220           | $258           |
| K-Value (2)         | $150           | $192           |
| Speedo X-Axis ($10) | $220           | $258           |
| Speedo Y-Axis ($10) | $230           | $268           |

The checksum is calculated by adding up (modulo 256) the values of all the bytes in Region 1 and Region 2 and then invering the bits in the result by XORing it with $FF.

Here is some sample C# code for calculating the checksum of a VWK501 EEPROM:
```
byte[] eeprom = ReadAllBytesFromEeprom();

int region1Address = 0x14F;
int region1Length = 8;
int region2Address = 0x220;
int region2Length = 0x20;        

byte sum = 0;

for (var i = region1Address; i < region1Address + region1Length; i++)
{
    sum += bytes[i];
}

for (var i = region2Address; i < region2Address + region2Length; i++)
{
    sum += bytes[i];
}

byte checksum = (byte)(sum ^ 0xFF);
```
