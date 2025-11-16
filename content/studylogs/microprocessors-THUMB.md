+++
date = '2025-11-16T14:37:56+08:00'
title = 'Microprocessors THUMB'
+++

# What is THUMB?
As we have learnt throughout the entire IE3102 course, ARM instructions are 32 bits.  
For this part of the course, we are using THUMB instructions, which are 16 bits.

## But why?
Apparently, since obviously 16-bits instruction is half of that of 32-bit instructions, you can fit more code into the same amount of 32-bit memory. 

## Register Sets in ARM vs THUMB

ARM | THUMB
--- | ------
r0  |   r0
r1  |   r1
r2  |   r2
r3  |   r3
r4  |   r4
r5  |   r5
r6  |   r6
r7  |   r7
r8  |   -
r9  |   -
r10  |   -
r11  |   -
r12  |   -
r13 (sp)  |   sp
r14 (lr)  |   lr
r15 (pc)  |   pc

As we can see clearly, for THUMB, only r0 to r7 are full accessible. This makes sense because:
- ARM has access to all 16 registers, it can use log2(16) = 4 bits (0000 to 1111)
- but THUMB only has access to 8 registers, half of ARM, log2(8) = 3 bits (000 to 111)

For r8 to r12, its only accessible for MOV, ADD and CMP. r13, r14 and r15 also has limited accessibility. Unfortunately, CPSR / SPSR cannot be accessed in THUMB mode

---

# How to enter THUMB?
Recall `BX lr` instruction when we want to branch to a subroutine from the subroutine chapter.  
Well, `BX` stands for _*Branch and Exchange*_. And, we can use `BX rd` to branch to THUMB state where `rd` stands for the target address  
As such, by controlling the Least Significant Bit (LSB) of `rd`, we can effectively branch to THUMB or ARM with :
- LSB = 1, ARM to THUMB
- LSB = 0, THUMB to ARM

## What the heck is this LSB thing?
Let's look at the ARM architecture as a whole:
- The fundamental unit of memory access in a 32-bit architecture is usually a **word**, which is 4 bytes (32 bits):
- -  ARM -> 32 bits or 4 bytes
- - THUMB -> 16 bits or 2 bytes

But since both ARM and THUMB instructions are multiples of 2 bytes, they must begin at a memory address that is a mutliple of 2.

### The Least Significant Bit (LSB)
if we consider how addresses are represented in binary like so:

| Address (Decimal) | Address (Binary) | LSB (Bit 0) |
|-------------------|------------------|-------------|
| 0                 | ...00000         | 0           |
| 1                 | ...00001         | 1           |
| 2                 | ...00010         | 0           |
| 3                 | ...00011         | 1           |
| 4                 | ...00100         | 0           |

...address that are multiple of 2 always have an LSB of 0.  
As such, for all valid instructions that is a multiple of 2 (both ARM and THUMB), their LSB is always going to be '0'.

### How to switch mode?
Example:
```ASM
    LDR     r0, = 0x2001    ; LSB = 1
    BX      r0
```
- Your CPU check the LSB bit, which is 1, switches to THUMB mode.
- But it clears the LSB bit again, back to zero so everything aligns normally
- Essentially, you are jumping to 0x2000 address to do THUMB code not 0x2001. The extra 1 bit is just to tell your CPU its time to switch to THUMB mode
- Think of the 1 bit as setting the T flag

---

# THUMB Limitation 
Here is a summary of the stuff for THUMB mode:
- Conditional execution is not used (no stuff like `MULEQ` or `ADDEQ`) but branch conditions are still allowed
- Source and destination register can be identical (ADD r1, #1  --> r1 = r1 + 1)
- Only low registers can used ( r0 to r7 )
- No barrel shifter
- Limited size for constants 
- For stack pushing and popping,  THUMB uses
- - PUSH {reg, lr} for storing
- - POP {reg, pc} for loading
- - Keep in mind that by default, THUMB PUSH POP is a Full Descending stack (FD)

---

# Example 1
Give the mnemonic for a THUMB instruction that is equivalent to the ARM instruction:   
`SUB r3, r2, r1, LSL #2`

- The original code can be understood as `r3 = r2 - r1 * 2^2`

``` ASM
    ; THUMB version
    LSL     r1, #2
    SUB     r2, r1
    MOV     r3, r2
```
- First we do LSL on r1 by 2 bits, or in other words, multiplying by 4
- Substract r2 by r1, and storing it into r2 again. Remember for THUMB, there's no need to mention the stored register like in ARM (SUB r2, r2, r1)
- Finally, we move the result of r2 into r3


## ARM Version
{{< figure src="https://i.imgur.com/LriCvdI.png" alt="armcode" class=center >}}
- Notice how its 16 bytes, from the first `MOV` to `stop B stop`, its `4 x 4 bytes = 16 bytes`

## THUMB Version
{{< figure src="https://i.imgur.com/PLiKRIh.png" alt="thumbcode" class=center >}}
- CODE32: 4 bytes x 2 = 8 bytes
- CODE16: 2 bytes x 6 = 12 bytes
- Total code size = 8 bytes + 12 bytes + 4 (literal pool) = 24 bytes

- Looking at the T flag when we run `BX r0`:
{{< figure src="https://i.imgur.com/IgTfbHi.png" alt="thumbcode" class=center >}}
- T flag becomes '1', we are in THUMB mode

# Example 2 - Branch from ARM to THUMB and back to ARM

``` ASM
        AREA arm2thumb, CODE, READONLY
        ENTRY

main    
        LDR     r0, =thumbProg + 1      ; Set LSB to 1
        BX      r0                      ; branch to thumbProg


        CODE16
thumbProg
        MOV     r2, #20                 ; Move #20 into r2
        MOV     r3, #30                 ; Move #30 into r3
        ADD     r2, r3                  ; r2 = r2 + r3 = 20 + 30 = 50
        LDR     r0, =armProg            ; LSB is 0, so we are back to ARM state
        BX      r0                      ; Branch to ARM state


        CODE32
armProg
        MOV     r4, #40                 ; Move #40 into r4
        MOV     r5, #50                 ; Move $50 into r5
        ADD     r5, r5, r4              ; Notice how we need to mention to storing register
                                        ; in CODE32
stop    B       stop

        END

```

- But what if we want to return to the main routine? `BX rd` never actually intend to return to the main routine, we need to use `BL`, but BL cannot be used to switch to THUMB. So how?

# Example 3 - Jump into THUMB subroutine and return to main
We need to introduce a new thing called veneer. Essentially, we `BL` into this veneer subroutine, which we will then do the usual THUMB mode switch in the veneer:

```ASM
        AREA    ToThumbSub, CODE, READONLY
        ENTRY

main
        BL      veneer                  ; call veneer

stop    B       stop

veneer                                  ; Put the switch to THUMB code in here
        LDR     r0, =thumbSub + 1       ; Set LSB to 1
        BX      r0                      ; Branch to thumbSub subroutine

        CODE16
thumbSub
        MOV     r4, #40
        MOV     r5, #50
        SUB     r5, r4

        BX      lr                      ; return to main and switch back to ARM
        END
```

