# EEPROM Addresses

The following table shows where various data is stored in the 2K-byte EEPROM and what RAM addresses the data is loaded into.

| EEPROM      | RAM           | Contents |
| ----------- | ------------- | -------- |
| $020 - $05F | $0100 - $013F | Keys |
| $064 - $065 | $15A4 - $15A5 | ? |
| $072 - $081 |  | ImmoKey (2 copies) |
| $082 - $089 | $0140 - $0147 | ImmoKey |
| $08A - $08F | $15A6 - $15AB | KeyCount/ImmoStatus |
| $090 - $0A1 | $15AC - $15BD | ? |
| $0A2 - $0AF | $15BE - $15CB | ImmoId |
| $0B0 - $0BD |  | ImmoId (copy 1) |
| $0BE - $0CB |  | ImmoId (copy 2) |
| $0CC - $0CD | $15CC - $15CD | [SKC](Logins.md) |
| $0CE - $0D1 |               | SKC (2 copies) |
| $0D2 - $0E3 | $0C7F - $0C90 | VIN1 |
| $0E4 - $0F5 | $0C7F - $0C90 | VIN2 |
| $0F4 - $0FB | $0BFB - $0C02 | Odometer Checksum |
| $0FC - $10B |  | [Odometer](Odometer.md) |
| $13C - $13D | $15CE - $15CF | [GWLogin](Logins.md) |
| $13E - $145 | $0048 - $004F | ? |
| $146 - $183 | $15D0 - $160D | ? |
| $1CE - $31D | $160E - $175D | ? |
| $35E - $361 | $175E - $1761 | ? |
| $398 - $399 |               | [Cluster ID for Radio](RadioChallengeResponse.md) |
| $39A - $3E3 | $1764 - $17AD | ? |
| $436 - $437 | $17AE - $17AF | [Access Level](AccessLevel.md) |
| $438 - $485 | $17B0 - $17FD | ? |
| $4F4 - $4F9 |  | [Patch Header](PatchModule.md) |
| $4FA - $XXX | $0E7C - $XXXX | [Patch Code + Data](PatchModule.md) |
