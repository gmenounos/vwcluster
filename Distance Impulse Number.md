# Distance Impulse Number (K-Value)

The instrument cluster calculates vehicle speed and distance travelled by counting pulses sent from either the transmission or the ABS controller. The number of pulses per km travelled is called the Distance Impulse Number, also known as the K-Value. By adjusting the last digit of the cluster's soft coding, the K-value can be set to one of several (usually 4-6) hardcoded values.

VDO VWK501 and VWK503 clusters contain several K-Value tables in their ROMs. The table that gets used depends on which vehicle family the cluster is configured for:

| Last Digit of Soft Coding | Golf/Jetta (0x0) | Škoda (0x5 or 0xB) | Passat (0x1 or 0xA) |
| ------------------------- | ---------------- | ------------------ | ------------------- |
| 1                         | 4345             | 4297               | 4345                |
| 2                         | 3528             | 3480               | 3528                |
| 3                         | 4134             | 4078               | 4134                |
| 4                         | 3648             | 3599               | 3648                |
| 5                         |                  | 4090               | 4182                |
| 6                         |                  |                    | 4128                |
| 7                         |                  |                    | 4202 (VWK503 only)  |

In VWK501 clusters, the least significant 4 bits of the EEPROM address 0x1F7 determine the family. In VWK503 clusters, the EEPROM address is 0x223.

For example, in a VWK501 cluster, if the value in the EEPROM at address 0x1F7 is 0x3B, that means the family is 0xB. If the last digit of the cluster soft coding was 3 then the K-Value would be 4078 according to the table above.