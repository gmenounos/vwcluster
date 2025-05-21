# VDO Seed/Key Authentication

Many VDO clusters require Seed/Key authentication in order to increase the [Access Level](./AccessLevel.md) so that the EEPROM can be read or written.

To perform Seed/Key authentication, the tester sends a custom command: "$1B $96 $01" and the cluster responds with "$1B S1 S2 S3 S4 S5 S6 S7 S8 V1 V2" where S1-S8 are the seed bytes and V1-V2 specify the version of the Seed/Key secret table that will be used to verify the key.[^1]

[^1]: Only S2, S4, S6 and S8 are used to calculate the key.

The tester then uses the seed bytes and the secret table to calculate a key, which it sends back to the cluster: "$1B $96 $02 A1 K1 K2 K3 K4 K5 K6" where A1 is the access level requested and K1-K6 are the key bytes.[^2]

[^2]: Only K1, K2, K4 and K6 are used to verify the key.

If the key was correctly calculated from the seed and the secret table then the access level will be increased to the level requested, though certain clusters require additional conditions to be true before the access level will be changed. The following table shows, for various [ROM versions](./RomVersion.md), which version of the Seed/Key secret table is used as well as any additional conditions required to allow the access level to be changed:

| ROM            | Part Number(s)            | Seed/Key Secret Table Version | Conditions to Allow Changing the Access Level                |
| -------------- | ------------------------- | ----------------------------- | ------------------------------------------------------------ |
| K5MJ07LM-08.10 | 5J0920810C                | $0B $00                       | Odometer Corrupt or < 100km                                  |
| KB5M07HH-09.00 | 3U0920842B                | $0D $00                       | Odometer Corrupt or < 100km                                  |
| V599LLA-01.00  | 1J0920800L                | ?                             | ?                                                            |
| VBKX00MH-01.00 | 3B0920925BX               | $01 $00                       | Always Allowed                                               |
| VCB07LL-09.00  | 1JD920826E                | $01 $00                       | Always Allowed                                               |
| VCLM09MH-09.00 | 3BD920848E                | $01 $00                       | Always Allowed                                               |
| VMMJ08MH-09.00 | 1J5920826L                | $01 $00                       | Always Allowed                                               |
| VQMJ07LM-09.00 | 6Y0920883M<br />6Q0920804 | $09 $00                       | Odometer Corrupt or < 100km or [valid checksum](#eeprom-checksum) |
| VQMJ09HH-05.10 | 6QE920827C                | $09 $00                       | Odometer Corrupt or < 100km or [valid checksum](#eeprom-checksum) |
| VSQX01LM-01.00 | 6Q0920800                 | $03 $00                       | Always Allowed                                               |
| VWK501MH-00.88 | 1J5920926C                | $01 $00                       | Always Allowed                                               |
| VWK501MH-01.00 |                           | $01 $00                       | Always Allowed                                               |
| VWK501MH-01.10 | 3B7920946E                | $01 $00                       | Always Allowed                                               |
| VWK503LL-09.00 | 1J5920906J                | $01 $00                       | Always Allowed                                               |
| VWK503MH-09.00 | 1J0920927                 | $01 $00                       | Always Allowed                                               |

## EEPROM Checksum

Some ROMs perform a checksum calculation based on the byte at EEPROM address $255 (of the [unencrypted](./VDO%20EEPROM%20Encryption.md) EEPROM) and will refuse to change the access level unless the bytes at EEPROM addresses $5E and $5F match the calculated values. The following C# code shows how the $5E and $5F values are calculated based on the value at address $255:

```c#
    /// <summary>
    /// If this checksum isn't correct, Seed/Key authentication won't work unless the odometer is
    /// less than 100km or the odometer is corrupted.
    /// </summary>
    public static (byte eeprom5E, byte eeprom5F) Calc5E5F(byte eeprom255)
    {
        byte[] secret = [0xA6, 0x63, 0xB4, 0xC5, 0x39];

        byte ix = (byte)((eeprom255 >> 4) % 5);

        byte eeprom5E = (byte)(eeprom255 ^ secret[ix]);
        byte eeprom5F = (byte)(eeprom5E ^ secret[(ix % 3) + 2]);

        return (eeprom5E, eeprom5F);
    }
```

The following table contains the eeprom5E and eeprom5F values for all possible values of eeprom255:

| eeprom255 | eeprom5E | eeprom5F |
| ---- | ---- | ---- |
|$00|$A6|$12|
|$01|$A7|$13|
|$02|$A4|$10|
|$03|$A5|$11|
|$04|$A2|$16|
|$05|$A3|$17|
|$06|$A0|$14|
|$07|$A1|$15|
|$08|$AE|$1A|
|$09|$AF|$1B|
|$0A|$AC|$18|
|$0B|$AD|$19|
|$0C|$AA|$1E|
|$0D|$AB|$1F|
|$0E|$A8|$1C|
|$0F|$A9|$1D|
|$10|$73|$B6|
|$11|$72|$B7|
|$12|$71|$B4|
|$13|$70|$B5|
|$14|$77|$B2|
|$15|$76|$B3|
|$16|$75|$B0|
|$17|$74|$B1|
|$18|$7B|$BE|
|$19|$7A|$BF|
|$1A|$79|$BC|
|$1B|$78|$BD|
|$1C|$7F|$BA|
|$1D|$7E|$BB|
|$1E|$7D|$B8|
|$1F|$7C|$B9|
|$20|$94|$AD|
|$21|$95|$AC|
|$22|$96|$AF|
|$23|$97|$AE|
|$24|$90|$A9|
|$25|$91|$A8|
|$26|$92|$AB|
|$27|$93|$AA|
|$28|$9C|$A5|
|$29|$9D|$A4|
|$2A|$9E|$A7|
|$2B|$9F|$A6|
|$2C|$98|$A1|
|$2D|$99|$A0|
|$2E|$9A|$A3|
|$2F|$9B|$A2|
|$30|$F5|$41|
|$31|$F4|$40|
|$32|$F7|$43|
|$33|$F6|$42|
|$34|$F1|$45|
|$35|$F0|$44|
|$36|$F3|$47|
|$37|$F2|$46|
|$38|$FD|$49|
|$39|$FC|$48|
|$3A|$FF|$4B|
|$3B|$FE|$4A|
|$3C|$F9|$4D|
|$3D|$F8|$4C|
|$3E|$FB|$4F|
|$3F|$FA|$4E|
|$40|$79|$BC|
|$41|$78|$BD|
|$42|$7B|$BE|
|$43|$7A|$BF|
|$44|$7D|$B8|
|$45|$7C|$B9|
|$46|$7F|$BA|
|$47|$7E|$BB|
|$48|$71|$B4|
|$49|$70|$B5|
|$4A|$73|$B6|
|$4B|$72|$B7|
|$4C|$75|$B0|
|$4D|$74|$B1|
|$4E|$77|$B2|
|$4F|$76|$B3|
|$50|$F6|$42|
|$51|$F7|$43|
|$52|$F4|$40|
|$53|$F5|$41|
|$54|$F2|$46|
|$55|$F3|$47|
|$56|$F0|$44|
|$57|$F1|$45|
|$58|$FE|$4A|
|$59|$FF|$4B|
|$5A|$FC|$48|
|$5B|$FD|$49|
|$5C|$FA|$4E|
|$5D|$FB|$4F|
|$5E|$F8|$4C|
|$5F|$F9|$4D|
|$60|$03|$C6|
|$61|$02|$C7|
|$62|$01|$C4|
|$63|$00|$C5|
|$64|$07|$C2|
|$65|$06|$C3|
|$66|$05|$C0|
|$67|$04|$C1|
|$68|$0B|$CE|
|$69|$0A|$CF|
|$6A|$09|$CC|
|$6B|$08|$CD|
|$6C|$0F|$CA|
|$6D|$0E|$CB|
|$6E|$0D|$C8|
|$6F|$0C|$C9|
|$70|$C4|$FD|
|$71|$C5|$FC|
|$72|$C6|$FF|
|$73|$C7|$FE|
|$74|$C0|$F9|
|$75|$C1|$F8|
|$76|$C2|$FB|
|$77|$C3|$FA|
|$78|$CC|$F5|
|$79|$CD|$F4|
|$7A|$CE|$F7|
|$7B|$CF|$F6|
|$7C|$C8|$F1|
|$7D|$C9|$F0|
|$7E|$CA|$F3|
|$7F|$CB|$F2|
|$80|$45|$F1|
|$81|$44|$F0|
|$82|$47|$F3|
|$83|$46|$F2|
|$84|$41|$F5|
|$85|$40|$F4|
|$86|$43|$F7|
|$87|$42|$F6|
|$88|$4D|$F9|
|$89|$4C|$F8|
|$8A|$4F|$FB|
|$8B|$4E|$FA|
|$8C|$49|$FD|
|$8D|$48|$FC|
|$8E|$4B|$FF|
|$8F|$4A|$FE|
|$90|$A9|$6C|
|$91|$A8|$6D|
|$92|$AB|$6E|
|$93|$AA|$6F|
|$94|$AD|$68|
|$95|$AC|$69|
|$96|$AF|$6A|
|$97|$AE|$6B|
|$98|$A1|$64|
|$99|$A0|$65|
|$9A|$A3|$66|
|$9B|$A2|$67|
|$9C|$A5|$60|
|$9D|$A4|$61|
|$9E|$A7|$62|
|$9F|$A6|$63|
|$A0|$06|$B2|
|$A1|$07|$B3|
|$A2|$04|$B0|
|$A3|$05|$B1|
|$A4|$02|$B6|
|$A5|$03|$B7|
|$A6|$00|$B4|
|$A7|$01|$B5|
|$A8|$0E|$BA|
|$A9|$0F|$BB|
|$AA|$0C|$B8|
|$AB|$0D|$B9|
|$AC|$0A|$BE|
|$AD|$0B|$BF|
|$AE|$08|$BC|
|$AF|$09|$BD|
|$B0|$D3|$16|
|$B1|$D2|$17|
|$B2|$D1|$14|
|$B3|$D0|$15|
|$B4|$D7|$12|
|$B5|$D6|$13|
|$B6|$D5|$10|
|$B7|$D4|$11|
|$B8|$DB|$1E|
|$B9|$DA|$1F|
|$BA|$D9|$1C|
|$BB|$D8|$1D|
|$BC|$DF|$1A|
|$BD|$DE|$1B|
|$BE|$DD|$18|
|$BF|$DC|$19|
|$C0|$74|$4D|
|$C1|$75|$4C|
|$C2|$76|$4F|
|$C3|$77|$4E|
|$C4|$70|$49|
|$C5|$71|$48|
|$C6|$72|$4B|
|$C7|$73|$4A|
|$C8|$7C|$45|
|$C9|$7D|$44|
|$CA|$7E|$47|
|$CB|$7F|$46|
|$CC|$78|$41|
|$CD|$79|$40|
|$CE|$7A|$43|
|$CF|$7B|$42|
|$D0|$15|$A1|
|$D1|$14|$A0|
|$D2|$17|$A3|
|$D3|$16|$A2|
|$D4|$11|$A5|
|$D5|$10|$A4|
|$D6|$13|$A7|
|$D7|$12|$A6|
|$D8|$1D|$A9|
|$D9|$1C|$A8|
|$DA|$1F|$AB|
|$DB|$1E|$AA|
|$DC|$19|$AD|
|$DD|$18|$AC|
|$DE|$1B|$AF|
|$DF|$1A|$AE|
|$E0|$D9|$1C|
|$E1|$D8|$1D|
|$E2|$DB|$1E|
|$E3|$DA|$1F|
|$E4|$DD|$18|
|$E5|$DC|$19|
|$E6|$DF|$1A|
|$E7|$DE|$1B|
|$E8|$D1|$14|
|$E9|$D0|$15|
|$EA|$D3|$16|
|$EB|$D2|$17|
|$EC|$D5|$10|
|$ED|$D4|$11|
|$EE|$D7|$12|
|$EF|$D6|$13|
|$F0|$56|$E2|
|$F1|$57|$E3|
|$F2|$54|$E0|
|$F3|$55|$E1|
|$F4|$52|$E6|
|$F5|$53|$E7|
|$F6|$50|$E4|
|$F7|$51|$E5|
|$F8|$5E|$EA|
|$F9|$5F|$EB|
|$FA|$5C|$E8|
|$FB|$5D|$E9|
|$FC|$5A|$EE|
|$FD|$5B|$EF|
|$FE|$58|$EC|
|$FF|$59|$ED|