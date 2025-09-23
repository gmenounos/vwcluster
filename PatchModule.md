# Memory Patch Module
The CDC16xxF-E contains a Memory Patch Module that can replace a few (6 to possibly 10) bytes in ROM with patch data in real time. As part of the boot process, the cluster ROM loads a patch block from EEPROM into RAM and initializes the Memory Patch Module registers so that buggy ROM code will either be replaced with the correct code (for tiny patches) or branch into corrected code in the patch block (for larger patches).

The patch block starts at EEPROM address $4F4 (VWK501) or $4F6 (VWK503) and consists of a 6-byte header followed by a code block and a data block.

## Header
The header consists of the following 6 bytes:

- H0 - Checksum - The sum modulo 256 of all bytes in the patch block (excluding H0 and the high bit of H1). If the sum is equal to the lower 7 bits of H1 then the complement of the sum is used.
- H1 - The low 7 bits indicate the number of bytes in the data block.
- H2 - The number of bytes in the code block (least significant byte).
- H3 - The number of bytes in the code block (most significant byte).
- H4 - Used to match target ROM version (see below).
- H5 - Used to match target ROM version (see below).

Bytes H4 and H5 are used to identify the exact [ROM version](./RomVersion.md) that the patch targets. If the EEPROM contains a patch block that does not match the ROM version then the patch will not be loaded. The matching is done as follows (ROM_VERSION[i] represents the i'th byte (starting at 0) of the ROM version):

- H4 must equal ROM_VERSION[8].
- If the most significant bit of H5 is 0 then H5 must equal ROM_VERSION[9].
- If the most significant bit of H5 is 1 then H5 must equal (the sum modulo 256 of all bytes of the ROM version except for ROM_VERSION[8]) OR'd with 0x80.

The cluster performs a few additional checks to ensure the patch is valid:

- H0 and H1 must be different (presumably to detect that no patch is present when that portion of the EEPROM is empty).
- If the high bit of H1 is 1 then the patch will be installed always. If the high bit of H1 is 0 then the patch will be installed after cluster reset but not in some other situation (TBD).
- The sum modulo 256 of H2, H3 and the lower 7 bits of H1 must be non-zero (i.e. the code block size added to the data block size must be non-zero).
- The sum of the number of bytes in the code block and the number of bytes in the data block must be even.
- The number of bytes in the data block must be greater than 3.
- The number of bytes in the data block modulo 4 must be 2 (i.e. there are 2 trailer bytes after all the 4-byte data block sequences).

## Code Block
The code block consists of 6502 instructions (and possibly data) used to implement the patch.

## Data Block
The data block consists of 1-6 (possibly up to 10) patches. Each patch is a 4-byte sequence:

- A2 - The most significant byte of the physical ROM address.
- A1 - The middle byte of the physical ROM address.
- A0 - The least significant byte of the physical ROM address.
- D0 - The ROM data replacement byte.

After all the 4-byte sequences there is a 2-byte trailer that is loaded into the PER1 and PER0 registers of the memory patch module in order to specify how many patches should be applied. In order to apply all patches in the data block, raise 2 to the power of one more than the number of patches and subtract 1. The first byte of the trailer stores the high byte of this result and the second byte stores the low byte of this result. For example, if there are 4 patches then the result would be:
$$
2^{4+1}-1 = 31
$$
So the first byte of the trailer would be $00 and the second byte would be $1F.

## ROM Mapping

The memory addresses configured in the patch module are physical addresses. When the cluster boots up, an Alternative Banking Mode is enabled which remaps these addresses to be compatible with the CPU, which is running in 8-bit W65C02 emulation mode. This mapping is detailed in the CDC16xxF-E Automotive Controller Family User Manual.

| Physical Address Range | Mapped Address Range |
| ---------------------- | -------------------- |
| $00000-$07FFF          | $00000-$07FFF        |
| $08000-$0FFFF          | $18000-$1FFFF        |
| $10000-$17FFF          | $28000-$2FFFF        |
| $18000-$18FFF          | $38000-$3FFFF        |
| $20000-$28FFF          | $48000-$4FFFF        |

## Patch Loading

The cluster first loads the patch header and checks its integrity. Assuming that it's identified as a valid patch, the cluster loads the remainder of the patch (beginning at EEPROM address $4FA (VWK501) or $4FC (VWK503)) into RAM (beginning at address $0E7C (VWK501MH 01.10).

After loading the code and data blocks into RAM, the cluster initializes the Memory Patch Module with the contents of the data block. Each 4-byte sequence consists of a data byte that should replace the contents of a particular ROM address.

## Patch Disassembly

To disassemble a patch to reverse engineer how it works, first dump the entire EEPROM to a file and inspect the patch header to determine the length of the code and data blocks. Then copy the entire EEPROM region containing the patch header, code block and data block to a new .bin file. You can then use any 6502 disassembler such as [6502bench SourceGen](https://6502bench.com/) to disassemble the code block (Set the start address of the code block to $0E7C). The code block may have multiple entry points if it contains more than one patch. You'll have to decode the data block and disassemble the portions of the ROM that it patches in order to find what addresses in RAM that it branches to. Those addresses will be the entry points in the code block.
