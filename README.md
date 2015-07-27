# Ensemble

Assembler of CAVEAT bytecode

```assembly
set.high r1, #0, #00A0 ; set the upper 16 bits of register 1 to #00A0, clear the lower 16 bits
set.low  r1, #1, #5600 ; set the lower 16 bits of register 1 to #5600, keep the upper 16 bits
load     r2, r1, -16    ; load the contents of memory address #00A055F0 (#00A05600 - #10) into register 2
add.u    r3, r2, 1      ; add 1 to register 2 and store it in register 3
store    r1, r3         ; store the contents of register 3 at the memory address contained in register 1 (#00A05600)
```

## Instructions

* opcode (8 bits)
* operands (24 bits)
* for `set.high` and `set.low`, the second operand, `mode`, determines what happens to the other 16 bits of the register.
  * 0000 = clear all
  * 1111 = set all
  * 0001 = keep unaltered
* in general an instruction suffix determines how operands are treated.
  * instructions with the suffix `.s` treats all their operands as signed.
  * instructions with the suffix `.u` treats all their operands as unsigned.
  * instructions with the suffix `.f` treats all their operands as float (32 bits).
* address and memory manipulation (load, store, branch) treats the registers as unsigned, but the offset immediate value as signed.  

| Mnemonic | Opcode         | Operands                       | Comment                                             |
|:---------|:---------------|:-------------------------------|:----------------------------------------------------|
| hcf      | 00000000 (0)   | ignored                        | Halt and catch fire                                 |
|          | 00000001 (1)   |                                | Reserved                                            |
| ...      | ...            | ...                            | ...                                                 |
|          | 00001111 (15)  |                                | Reserved                                            |
| set.high | 00010000 (16)  | Rd, mode, C (16-bit)           | Rd (upper 16 bits) = C, mode affects the other bits |
| set.low  | 00010001 (17)  | Rd, mode, C (16-bit)           | Rd (lower 16 bits) = C, mode affects the other bits |
| copy     | 00010010 (18)  | Rd, Ra, 0000000000000000       | Rd = Ra                                             |
|          | 00010011 (19)  |                                | Reserved                                            |
| load     | 00010100 (20)  | Rd, Ra, C (16-bit, signed)     | Load Rd with the four bytes addressed by Ra + C     |
|          | 00010101 (21)  |                                | Reserved                                            |
|          | 00010110 (22)  |                                | Reserved                                            |
|          | 00010111 (23)  |                                | Reserved                                            |
| store    | 00011000 (24)  | Rd, Ra, C (16-bit, signed)     | Store the contents of Rd at the address Ra + C      |
|          | 00011001 (25)  |                                | Reserved                                            |
| ...      | ...            | ...                            | ...                                                 |
|          | 00011111 (31)  |                                | Reserved                                            |
| add.s    | 00100000 (32)  | Rd, Ra, Rb, C (12-bit)         | Rd = Ra + Rb + C (signed)                           |
| add.u    | 00100001 (33)  | Rd, Ra, Rb, C (12-bit)         | Rd = Ra + Rb + C (unsigned)                         |
| sub.s    | 00100010 (34)  | Rd, Ra, Rb, C (12-bit)         | Rd = Ra - Rb - C (signed)                           |
| sub.u    | 00100011 (35)  | Rd, Ra, Rb, C (12-bit)         | Rd = Ra - Rb - C (unsigned)                         |
| mul.s    | 00100100 (36)  | RdHi, RdLo, Ra, Rb, 00000000   | RdHi, RdLo = Ra * Rb (signed)                       |
| mul.u    | 00100101 (37)  | RdHi, RdLo, Ra, Rb, 00000000   | RdHi, RdLo = Ra * Rb (unsigned)                     |
| div.s    | 00100110 (38)  | RdQu, RdRe, Ra, Rb, 00000000   | RdQu, RdRe = Ra / Rb (signed)                       |
| div.u    | 00100111 (39)  | RdQu, RdRe, Ra, Rb, 00000000   | RdQu, RdRe = Ra / Rb (unsigned)                     |
|          | 00101000 (40)  |                                | Reserved                                            |
| ...      | ...            | ...                            | ...                                                 |
|          | 00101111 (47)  |                                | Reserved                                            |
| add.f    | 00110000 (48)  |                                | Rd = Ra + Rb (float)                                |
| sub.f    | 00110001 (49)  |                                | Rd = Ra - Rb (float)                                |
| mul.f    | 00110010 (50)  |                                | Rd = Ra * Rb (float)                                |
| div.f    | 00110011 (51)  |                                | Rd = Ra / Rb (float)                                |
|          | 00110100 (52)  |                                | Reserved                                            |
| ...      | ...            | ...                            | ...                                                 |
|          | 00111111 (63)  |                                | Reserved                                            |
| and      | 01000000 (64)  | Rd, Ra, Rb, 000000000000       | Rd = Ra AND Rb (bitwise)                            |
| or       | 01000001 (65)  | Rd, Ra, Rb, 000000000000       | Rd = Ra OR Rb (bitwise)                             |
| xor      | 01000010 (66)  | Rd, Ra, Rb, 000000000000       | Rd = Ra EXCLUSIVE OR Rb (bitwise)                   |
| not      | 01000011 (67)  | Rd, Ra, 0000000000000000       | Rd = NOT Ra (bitwise)                               |
|          | 01000100 (68)  |                                | Reserved                                            |
| ...      | ...            | ...                            | ...                                                 |
|          | 01000111 (71)  |                                | Reserved                                            |
| asl      | 01001000 (72)  | Rd, Ra, Rb, 000000000000       | Rd = Ra << Rb                                       |
| asr      | 01001001 (73)  | Rd, Ra, Rb, 000000000000       | Rd = Ra >> Rb                                       |
| lsl      | 01001010 (74)  | Rd, Ra, Rb, 000000000000       | Rd = Ra <<< Rb                                      |
| lsr      | 01001011 (75)  | Rd, Ra, Rb, 000000000000       | Rd = Ra >>> Rb                                      |
|          | 01001100 (76)  |                                | Reserved                                            |
| ...      | ...            | ...                            | ...                                                 |
|          | 01001111 (79)  |                                | Reserved                                            |
| beq      | 01010000 (80)  | Rd, Ra, Rb, C (12-bit, signed) | Go to address in Rd + C if Ra = Rb                  |
| bne      | 01010001 (81)  | Rd, Ra, Rb, C (12-bit, signed) | Go to address in Rd + C if Ra != Rb                 |
| bgt      | 01010010 (82)  | Rd, Ra, Rb, C (12-bit, signed) | Go to address in Rd + C if Ra > Rb                  |
|          | 01010011 (83)  |                                | Reserved                                            |
| ...      | ...            | ...                            | ...                                                 |
|          | 01111111 (127) |                                | Reserved                                            |
|          | 10000000 (128) |                                | User-defined                                        |
| ...      | ...            | ...                            | ...                                                 |
|          | 11111111 (255) |                                | User-defined                                        |
