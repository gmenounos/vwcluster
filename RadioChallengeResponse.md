# Radio/Cluster Challenge/Response

VW MKIV radios have an anti-theft mechanism the prevents the radio from operating if it's moved to a different vehicle unless a unique-to-the-radio 4-digit "SAFE" code is entered on the radio front panel. This code is generated when the radio is manufactured and is stored in the radio's EEPROM and also in a database maintained by VW. If the owner of a radio forgets the code, a VW dealer is able to retrieve it from the database. It's also possible to retrieve the codes for certain radios via reverse engineering: https://github.com/mnaberez/vwradio

The way that the radio determines whether it's been moved to a new vehicle is by communicating with the instrument cluster. For single DIN radios, this is done via the K-Line using the KW1281 protocol.

## Protocol

During cluster manufacturing, a 2-byte random number (Cluster ID) is generated and stored in the cluster's EEPROM. When the radio is turned on, it sends a 4-byte "challenge" block with a $D7 block title. The cluster responds with a $3D block containing a 4-byte response. The 2-byte Cluster ID is encoded in the response. The radio decodes it and compares it with its last known Cluster ID (stored in the radio's EEPROM). If the Cluster ID doesn’t match then the radio assumes it’s been put in a different vehicle so it requires entering the SAFE code. When a correct SAFE code has been entered, the radio stores the Cluster ID in the EEPROM and then begins operating.

The 4-bytes that the radio sends are just random and the cluster ignores them. In order to generate the response, the cluster reads the 2-byte Cluster ID from the EEPROM (radio1 and radio2 below) and then generates 2 random bytes (random1 and random2 below) by reading a free-running counter value. It then does some bit twiddling involving XORing the radio bytes with the random bytes, counting the number of 0s in the random bytes, left-rotating the radio bytes that number of times, XORing again and finally adding some obfuscation bytes in order to generate the 4-byte reply to the radio.

```
(byte, byte, byte, byte) EncodeClusterId(
    byte cluster1, byte cluster2, byte random1, byte random2)
{
    cluster1 ^= random1;
    cluster2 ^= random2;

    byte zeroCount = 0;
    for (int i = 0; i < 8; i++)
    {
        if (((random1 >> i) & 1) == 0)
        {
            zeroCount++;
        }
        if (((random2 >> i) & 1) == 0)
        {
            zeroCount++;
        }
    }

    bool carry;

    byte rotated1 = cluster1;
    byte rotated2 = cluster2;

    while (zeroCount > 0)
    {
        carry = (rotated2 & 0x80) != 0;
        rotated1 = LeftRotate(rotated1, ref carry);
        rotated2 = LeftRotate(rotated2, ref carry);
        zeroCount--;
    }

    cluster1 = (byte)(rotated1 ^ random1);
    cluster2 = (byte)(rotated2 ^ random2);

    carry = false;
    cluster1 = AddWithCarry(cluster1, 0xe7, ref carry);
    cluster2 = AddWithCarry(cluster2, 0xbd, ref carry);
    random1 = AddWithCarry(rotated1, 0x18, ref carry);
    random2 = AddWithCarry(rotated2, 0x00, ref carry);

    return (cluster1, cluster2, random1, random2);
}

byte LeftRotate(byte value, ref bool carry)
{
    var newCarry = (value & 0x80) != 0;
    if (carry)
    {
        carry = newCarry;
        return (byte)((value << 1) | 0x01);
    }
    else
    {
        carry = newCarry;
        return (byte)(value << 1);
    }
}

byte AddWithCarry(byte value1, byte value2, ref bool carry)
{
    int result = value1 + value2;
    if (carry)
    {
        result++;
    }

    carry = result > 0xFF;

    return (byte)(result & 0xFF);
}
```