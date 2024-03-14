# Access Level

The cluster supports 8 access levels (0-7) which determine which diagnostic commands the cluster will accept. The default access level is stored in obfuscated format at EEPROM offsets $0436-0437. When the cluster starts up, that value is loaded from the EEPROM, decoded and stored in RAM address $0AFA. The default access level is 4 (encoded as $A6 $84) and does not allow access to all EEPROM locations (e.g. the SKC is not accessible). The access level can be temporarily increased via Seed/Key authentication. Most diagnostic tools that need full EEPROM access attempt to increase the access level to 7.

## Obfuscated Format
The following C# method calculates the 2 obfuscated EEPROM bytes based on the access level. This code supports up to 16 access levels but the cluster only supports 8.

```
static byte[] ObfuscateAccessLevel(byte accessLevel)
{
    accessLevel &= 0x0F;
    var byte1 = (byte)(0xE2 - (byte)(accessLevel * 0x0F));
    var byte2 = (byte)(0xC0 - (byte)(accessLevel * 0x0F));
    
    return [byte1, byte2];
}
```

Note that either byte could be used to decode the access level. Presumably 2 bytes are used to provide redundancy in case one gets corrupted in the EEPROM. Newer VDO clusters (e.g. VSQX01) store these 2 bytes in 3 consecutive EEPROM locations to provide even more redundancy.