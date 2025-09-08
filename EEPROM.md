# EEPROM Addresses

The following table shows the addresses and lengths of important fields in the EEPROM for different ROM versions. Cloning a cluster would involve copying all these fields from the old cluster to the new cluster. Kw1281test or VCDS would then need to be used to set the soft coding in the new cluster but the new cluster would not need to be paired to the ECU because copying the below fields effectively does that.

| Field (Length)                              | VWK501 | VWK503 | VQMJ07 | K5MJ07 |      |
| ------------------------------------------- | ------ | ------ | ------ | ------ | ---- |
| Key IDs (64)                                | 0x020  | 0x070  | 0x070  | 0x070  |      |
| Immo Key (24)                               | 0x072  | 0x0B0  | 0x0B0  | 0x0B0  |      |
| Key Count (3)                               | 0x08A  | 0x0C8  | 0x0C8  | 0x0C8  |      |
| Immo Status (3)                             | 0x08D  | 0x0CB  | 0x0CB  | 0x0CB  |      |
| Immo Id (42)                                | 0x0A2  | 0x0E0  | 0x0E0  | 0x0E0  |      |
| SKC (6)                                     | 0x0CC  | 0x10A  | 0x10A  | 0x10A  |      |
| VIN1 (17)                                   | 0x0D2  | 0x110  | 0x110  | 0x110  |      |
| VIN2 (17)                                   | 0x0E4  | 0x122  | 0x122  | 0x122  |      |
| [Odometer](Odometer.md) (23)                | 0x0F5  | 0x133  | 0x133  | 0x133  |      |
| [Cluster ID](RadioChallengeResponse.md) (2) | 0x398  | 0x3CC  | 0x45C  | 0x50A  |      |

The following table shows where various data is stored in the 2K-byte EEPROM and what RAM addresses the data is loaded into (VWK501MH 01.10).

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
