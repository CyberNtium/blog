+++
date = '2025-09-14T22:30:21+08:00'
title = 'Microprocessors TUT4'
+++

# Question 1
Describe the contents of register r13 after the following instructions complete, assuming that memory contains the values shown below. Register r0 contains 0x4000 1000, and the memory system is little-endian.

|   Address   |  Contents |    
| ----------- | --------- |
| 0x4000 1000 |   0x06    | 
| 0x4000 1001 |   0xFC    | 
| 0x4000 1002 |   0x03    | 
| 0x4000 1003 |   0xFF    | 

#### a.) LDRSB r13, [r0]
LDRSB means Load Register Signed Byte
- This instruction loads a Sign Byte from the address 0x4000 1000 into r13
- Since each address takes a byte, we only take 1 number and load into r13 and extend it to 32-bit word
- r13 now contains `0x00000006`

#### b.) LDRSH r13, [r0]
LDRSH means Load Register Signed Halfword
- Halfwords are 2 bytes, 16-bits, we take 2 number and load into r13
- Note that 0xFC is a negative number, s0 we need to do sign extension when we form the 32-bit word
- r13 now contains `0xFFFFFC06`

#### c.) LDR r13, [r0]
LDR means Load Register
- This instruction loads a whole 32-bit word into r13
- Thus, we take all the four numbers and load into r13
- R13 now contains `0xFF03FC06`

#### d.) LDRH r13, [r0]
LDRH means Load Register Halfword
- Same as LDRSH, but since its not signed, we do not need to perform sign extension
- r13 now contains `0x0000FC06`

---
# Question 2
Calculate the effective address of the following instructions if register r3 = 0x4000 1000 and register r4 = 0x20:

#### a.) STRH r9, [r3, r4]
STRH means Store Halfword
- Pre-index offset: r3 + r4
- Final address: 0x4000 1000 + 0x20 = 0x4000 1020

#### b.) LDRB r8, [r3, r4, LSL#3]
LDRB means Load Byte, so meaning we load a byte
- Pre-index with Logical Shift Left: r3 + r4*(2^3)
- Final address:   
```text
    0x20 = 0010 0000   
    LSL #8 Shift left by 3 bits
    0x100 = 00000000 00000000 00000001 00000000
    Add to offset:
    0x4000 1000 + 0x100 = 0x4000 1100
```

#### c.) LDR r7, [r3], r4
- Post indexing, update r3 by +r4 after loading [r3] into r7
- Final Address before post-index = 0x4000 1000
- Final Address after post-index = 0x4000 1020 for r3

#### d.) STRB r6, [r3], r4, ASR #2
- Post indexing with Asymptotic Shift Right by 2^2
```text
0x20 = 0010 0000
ASR by 2 bits
0x8 = 0000 1000
Effective Address Before Post-index = 0x4000 1000
Effective Address After Post-index  = 0x4000 1000 + 0x8
                                    = 0x4000 1008
```

# Question 3
Write a program that sums word-length values in memory, storing the result in register r2. Include the following table of values to sum in your code:
```text
TABLE   DCD 0xFEBBA, 0x1234, 0x8888  
        DCD 0x13, 0x8080808, 0xFFFF
```

```ASM
        AREA WordSum, CODE, READONLY
        ENTRY

sum     RN      0
counter RN      1

        MOV     counter, #6
        ADR     r3, TABLE

loop

        LDR     r4, [r3], #4
        ADD     sum, sum, r4
        SUBS    counter, counter, #1
        BNE     loop

Done    B       Done

        END

TABLE   DCD 0xFEBBA, 0x1234, 0x8888  
        DCD 0x13, 0x8080808, 0xFFFF
```

