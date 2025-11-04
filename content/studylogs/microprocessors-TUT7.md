+++
date = '2025-11-02T01:03:17+08:00'
title = 'Microprocessors TUT7'
+++

# Tutorial 7

## TUT7 Q1
State the validity for each of the following instructions. Explain why for those that is not valid.

### a.) STM r4, {r4-r6}
*_Valid_*.
- Given that r4 is the base register with a given base address, let's say, `0x40000100`:
- - | Address (hex) | Stored Value | 
| ------------- | ------------     | 
| `0x40000100`  |      r4         | 
| `0x40000104`  |      r5          |          
| `0x40000108`  |      r6          | 
- - Funny thing about this is that, we are storing the value of r4 which is `0x40000100`, into the address of r4 which is also `0x40000100`
- - The rest is just normal

### b.) LDM r4, {r4, r7}
*_Valid_*.
- Given that r4 is also the base register with the same base address, `0x40000100`, now we load whatever is in that address into r4 and r7:
- - | Address (hex) | Loaded into  | 
| ------------- | ------------     | 
| `0x40000100`  |      r4         | 
| `0x40000104`  |      r7          |  
- - Similar to (a), we are loading whatever is in `0x40000100` into r4 again.
- - r7 is just normal LDR operation.

### c.) STMIA r5!, {r5, r4, r9}
*_Invalid_*. Technically it is possible but the prof said it's not, so i dunno
- Recall that STMIA = Store Multiple Increment After. Increment After means after you store something, then you increment the stack pointer
- But in this case, we are not using sp, we are using r5 as the pointer, indicated by the "!" symbol. 
- This is not encouraged as sp is used generally for pointing, and using normal registers will result in unpredictable stuff:

- - What's happening here is just updating the r5 address after storing everything:
- - | Address (hex) | updated r5 address  | stored value 
| ------------- | ------------     | ------------
| `0x40000100`  |      `0x40000104`         | value of r5
| `0x40000104`  |      `0x40000108`          | value of r4  
| `0x40000108`  |      `0x4000010C`          | value of r9  
- - As you can see, what ends up happening is just updating r5's address after storing each register, so at the end r5 will have `0x40000100 + 12 bytes = 0x4000010C`

### LDMDA r2, {}
*_Invalid_*
Very simple, there must be at least one register to be loaded into, unpredictable results

### STMDB r15!, {r0-r3, r4, lr}
*_Invalid_*.
I'm just gonna take the prof's words:
- Using the program counter as the base pointer will results in unpredictable behaviour


## TUT7 Q2
If register r6 holds the address 0x8000 and you execute the instruction `STMIA r6, {r7, r4, r0, lr}`. Write the memory address that now holds the value that was in registers r0, r4, r7 and lr respectively.  
 (Assume r0=0x10, r4=0x14, r7=0x17, lr=0xAB)

- STMIA = Store Multiple Increment After, so store something first then increment

| Address (hex) | Registers        | Stored Value   |
| ------------- | ------------     | --------- |
| `0x8010`  |      --          |   ----     |
| `0x800C`  |      lr          | 0x000000AB |
| `0x8008`  |      r7          | 0x00000017 |
| `0x8004`  |      r4          | 0x00000014 |
| `0x8000`  |      r0          | 0x00000010 |

## TUT7 Q3
Assume that the memory and registers r0-r3 of an ARM processor appears as follows: 
Given that r3 has the base address of 0x40000000
| Address (hex) |Value   |
|----------|------------|
| `0x40000010`  | 0x00000001 |
| `0x4000000C`  | 0xFEEDDEAF |
| `0x40000008`  | 0x00008888 |
| `0x40000004`  | 0x12340000 |
| `0x40000000`  | 0xBABE0000 |

Give the contents of the memory and registers that have been changed after executing the instruction `LDMIA r3!, {r0, r1, r2}`

LDMIA = Load Multiple Increment After, load from memory into the registers then increment

- In this case, r3 is the pointer as we need to update its address after each increment.
- Final register content and r3 address
- | register | value |
|---------|---------|
| r0         | 0xBABE0000|
| r1         | 0x12340000|
| r2         | 0x00008888|
| r3         | 0x4000000C|