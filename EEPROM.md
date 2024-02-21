# EEPROM Addresses

The following table shows where various data is stored in the 2K-byte EEPROM and what RAM addresses the data is loaded into.

| Start | End | RAM | Contents |
| ------| ----| --- | -------- |
| $020 | $05F | $0100 | Keys |
| $064 | $065 | $15A4 | ? |
| $072 | $081 | $0010 | ImmoKey |
| $082 | $089 | $0140 | ? |
| $08A | $08F | $15A6 | KeyCount/ImmoStatus |
| $090 | $0A1 | $15AC | ? |
| $0A2 | $0AF | $15BE | ImmoId |
| $0B0 | $0B7 | $0010 | ? |
| $0B8 | $0BD | $0010 | ? |
| $0BE | $0C5 | $0018 | ? |
| $0C6 | $0CB | $0018 | ? |
| $0CC | $0CD | $15CC | SKC |
| $0CE | $0D1 | $0010 | ? |
| $0D2 | $0E3 | $0C7F | VIN1 |
| $0E4 | $0F5 | $0C7F | VIN2 |
| $0F4 | $0FB | $0BFB | Odometer Checksum |
| $0FC | $10B | $0010 | Odometer |
| $13C | $13D | $15CE | GWLogin |
| $13E | $145 | $0048 | ? |
| $146 | $183 | $15D0 | ? |
| $1CE | $31D | $160E | ? |
| $35E | $361 | $175E | ? |
| $398 | $399 |       | Cluster ID for Radio |
| $39A | $3E3 | $1764 | ? |
| $436 | $437 | $17AE | ? |
| $438 | $485 | $17B0 | ? |
| $4F4 | $4F9 | $0014 | Patch Header |
| $4FA | | $0E7C | Patch Code + Data |
