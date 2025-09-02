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

### Examples

- `STR r0, [r1]`
- Store content of r0 to the location pointed to by contents of r1

- `LDR r2, [r1]`
- Load r2 with contents of memory location pointed to by contents of r1