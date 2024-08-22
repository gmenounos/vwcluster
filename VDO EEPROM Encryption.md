# VDO EEPROM Encryption

Some newer VDO clusters (e.g. 2007+ Polo, SEAT, Škoda) encrypt the 2KB 93C86 EEPROM to prevent its data from being read or modified by knowledgeable users who have access to an EEPROM programmer. The algorithm moves the location of the data within the EEPROM as well as scrambling the bytes.

The algorithm works as follows:
- The EEPROM is addressed as 1024 2-byte blocks.
- The original address is first scrambled so that each 2-byte block will be stored at a new location.
- The 2 bytes of data are then also scrambled before they are written to the new location.

## Address Scrambling
- The 10-bit unscrambled address is XOR'd with a 10-bit constant (which varies by cluster type).
- Then the 10 bits of the source address are shuffled by swapping some of the bits with each other. For example, bits 0 and 1 could be swapped and bits 2 and 3 could be swapped. The actual bits that are swapped varies by cluster type.
- The result of the XOR and shuffling is the scrambled address within the EEPROM where the data is stored.

## Data Scrambling
- The first byte of the block is XOR'd with the high 2 bits of the 10-bit original address. It is then XOR'd with a constant which varies by cluster type.
- The second byte of the block is XOR'd with the low 8-bits of the 10-bit original address. It is then XOR'd with a constant which varies by cluster type.
- **Exception:** The bytes in one block (unscrambled address 0xFD in some clusters and 0x116 in others) are not scrambled at all. For unknown reasons, these bytes are almost always both 0x00.
- **Exception:** The 16 odometer bytes beginning at unscrambled address 0x9D are scrambled by XOR'ing them with a 16-byte constant sequence which varies by cluster type.
