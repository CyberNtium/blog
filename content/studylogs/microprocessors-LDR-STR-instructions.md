---
title: 'Microprocessors LDR STR Instructions'
date: '2025-09-03T07:21:55+08:00'
type: "studylogs"
---

# Assembly Load and Store Instructions

- `LDR`: **L**oad**D** from memory into a **R**egister
- `STR`: **ST**ore words from **R**egister into memory

### Some basic syntax:

- `LDR/STR {<cond>}{type} <Rd>, <addressing_mode>`

- where:

- `<Rd>` = destination for **LDR** but source for **STR**
- `cond` = condition flag
- `type` = byte, halfword, word(default), signed & unsigned

### Common LDR/STR Instructions

|  Loads  |  Stores |     Size & Type     |
| ------- | ------- | ------------------- |
| LDR     |   STR   | Word (32 bits)      |
| LDRB    |   STRB  | Byte (8 bits)       |
| LDRH    |   STRH  | Halfword (16 bits)  |
| LDRSB   |   N/A   | Signed Byte         |
| LDRSH   |   N/A   | Signed Halfword     |
| LDM     |   STM   | Multiple Words      |

### Basic Examples

- `STR r0, [r1]`
- Store content of r0 to the location pointed to by contents of r1

- `LDR r2, [r1]`
- Load r2 with contents of memory location pointed to by contents of r1


### Little Endian & Big Endian TODO


### Pre-Indexed Instructions
- Pre-indexed addressing basically adds an additional offset to the base address before executing LDR/STR

- `LDR r0, [r1, #8]`  
- Goes into address of `r1 + 8` and loads into r0

#### Example:
- `STR r0, [r1, #12]`
- `r01` has value of `0xABCDEF`
- `[r1]` has address of `0x200`
- Offset by 12 so we add 12 to `0x200` --> `0x20C` (hexadecimal)
- Finally we store whatever is inside `r0` into the address of `0x20C`

### Pre-Index with Logical Shift Left (LSL)
#### Example

- `LDR r9, [r1, r8, LSL #2]`
- Given that `[r1]` contains address value of `0x4000`, `[r8]` is `0x20`
- Let's start with a basic algebraic expression view:
- We load whatever is in `[x]` into `r9`
- We start with base address `[r1]` but offset by the value contained in the address `[r8]`
- Then we perform a _Logical Shift Left_ or LSL by 2 units on the offset `[r8]`
- How it looks like:
- - `0x20` (hexadecimal) = `0010 0000` (binary)
- - `0010 0000` --> LSL by 2 units
- - `1000 0000` (binary) = `0x80` (hexadecimal)

- Finally, add the offset to the base address:
- - `0x4000` + `0x80` = `0x4080`
- - Load whatever is in the address `0x4080` into `r9`

- - Another simple way to look at this is:
- - [r1, r8, LSL #2]
- - final address will be `[r1] + [r4]*2^2`

### Pre-Index with Write-Back `!`
#### Example

- `LDR r0, [r1, #4]!`
- Do everything as usual but at the end updates the `[r1]` address with the new one with offset included

### Post-Index 
#### Example

- Note that this is very similar to write-back but still a bit different
- `LDR r2, [r1], #1`
- Given that `[r1]` is `0x20`, 
- First we load whatever is in base address `r1` into `r2`
- Then we increase base address by offset `#1`, so `0x21`

- Difference with Write-back:
- `LDR r2, [r1, #1]!`
- Increment to base address by offset first then load into r1



