+++
date = '2025-10-13T14:30:31+08:00'
title = 'Microprocessors Subroutines'
+++

# Subroutines

- If you are familiar to class in OOP languages like Java or C++, subroutine is very similar to it.  
- Subroutines are basically reusing code to simplify your code structure. You can kinda think of it as a mini-program within your program

## Requirements For Subroutines
- We must use Stack to save and restore affected registers and return address 
- Our trusty `LDM` and `STM` to save and restore
- We can call a subroutine using the `BL`, "Branch and Link" instruction:
- - We then first need to transfer the return address to the Link Register, r14, also known as "lr"
- - lr = pc - 4, pc is program counter
- - We then transfer the branch target (starting address of subroutine) to the program counter, pc

{{< figure src="https://i.imgur.com/cKyzTPe.jpeg" alt="subroutine" class=-"center" >}}

### Branch Link (BL) Instruction
- As mentioned above, to call a subroutine, we usually use a Branch Link, BL instruction
- `BL label` - where **_label_** is the target address of the first instruction of the subroutine
- Actions by BL:
- - Copy the return address, as in the the address of the instruction immeddiate *after* the "BL Subroutine1" instruction to link register, lr (pc - 4)
- - Copy the target address of the subroutine (first line) to program counter, pc

--- 

# Subroutine Example
```ASM
        AREA simpleSub, CODE, READONLY
        ENTRY

        MOV     r0, #1
        BL      simple
        MOV     r2, #2

stop    B       stop

simple  
        ADD     r3, r2, #1
        MOV     pc, lr
        ; or we can do 'BX lr'

        END
```
- Below is the table form of the instruction with their respective instructions:


|   Address  |  Instructions |
|------------| ------------- |
|     0x0    | MOV r0, #1    | 
|     0x4    | BL simple     | 
|     0x8    | MOV r2, #2    | 
|     0xC    | stop B stop   | 
|     0x10   | simple ADD r3, r2, #1 | 
|     0x14   | MOV pc, lr    | 
|     0x1C   |             | 

- Basically all we need to know is, pc will also point at the next instruction that will be executed. 
- When we execute `BL simple` at `0x4`, pc will point to the address of the first line of `simple` which is `0x10`, meaning the next line ARM will execute is at `0x10`
- At the same time, the instruction immediate after `BL simple` at `0x8` will be stored in lr. This will serve as the returning address for pc.

- After going through the subroutine's body, we end with `MOV pc, lr` basically copying the returning address to pc, meaning the next instruction will be back at the main code.


## Nested Subroutines ?

> What if we have another subroutine nested within the first one? Will the pc and lr still work as intended?

{{< figure src="https://i.imgur.com/klHEVcE.jpeg" alt="nestedSubroutine" class="center" >}}

- As we can see, when we call the first subroutine, the initial address that `lr` holds is the address for the instruction immediately after the `BL sub1`
- In Sub1 however, the moment we call the 2nd subroutine, we will override the initial `lr` value, and now `lr` points to the immediately instruction after `BL sub2`. Thus, we can never go back to main, resulting with an endless loop

### Correct Implementation

{{< figure src="https://i.imgur.com/sTBsvH8.jpeg" alt="CorrectNestedSubroutine" class="center" >}}

- When we call the first subroutine, everything goes normally with `lr` and `pc`
- The moment we enter Sub1, we need to store the initial `lr` pointed address into a stack
- Then we do the nested stuff, and come back to Sub1
- Now, we need to load the store `lr` pointed address into `pc`, so that `pc` can point back at main.


## Passing Parameters Through Registers

```ASM
ramBase EQU 0x40000000
        AREA PassParamatersRegister, CODE, READONLY

        ENTRY

main
                LDR     sp, =ramBase + 0x100
                MOV     r0, #0
                LDR     r1, =0x00012340
                MOV     r2, #4
                MOV     r4, #0xA

                BL      shiftfunction

stop            B       stop

shiftfunction   

                STMFD   sp!, {r4, lr}
                
                MOV     r4, #0
                CMP     r0, r4
                MOVEQ   r3, r1, LSL r2
                MOVNE   r3, r1, LSR r2
                LDMFD   sp!, {r4, pc}

        
        END
```

This is a weird ass example from the lecture notes, I do not particularly enjoy it but it's good to include here I guess:

- The only thing we need to focus on is the `r4` value before `BL` and after `BL`, and also `r3`
- The moment we enter the subroutine, we do:
- - `STMDB sp!, {r4, lr}`
- - We need to store `lr` because that's the returning address
- - We also need to store previous the `r4` value because we will be using the register again in the subroutine.
- - Meaning, if we don't store the previous `r4` value, it will be overwritten.
- - `MOV r4, #0`
- - As such, `r4` value is now changed.
- - We continue about whatever is the subroutine body
- - `LDMFD sp!, {r4, pc}`
- - Load the returning address of `lr` into `pc` because we need to go back to main
- - We also need to load the value initial `r4` back to current `r4`

{{< figure src="https://i.imgur.com/kTGRBLN.png" alt="register" class="center" >}}

- As we can see, after running the entire thing, r4 still remains the same since we use a stack to retain its value
- We can also see that register initialise inside a subroutine can be pass out into main, that's why we can see the shifted `0x00123400` in r3

## Passing Parameters Through Stack

```ASM
ramBase EQU 0x40000000
        AREA PassParaStack, CODE, READONLY
        ENTRY

main
                LDR     sp, =ramBase + 0x100
                MOV     r0, #0     
                LDr     r1, =0x00001234
                MOV     r2, #4

                STMFD   sp!, {r0-r3}
                BL      shiftFunc
                LDMFD   sp!, {r0-r3}

stop            B       stop

shiftfunc
                STMFD   sp!, {r4-r7, lr}
                LDR     r4, [sp, #20]
                LDR     r5, [sp, #24]
                LDR     r6, [sp, #28]
                MOV     r7, #0
                CMP     r4, r7

                MOVEQ   r7, r5, LSL r6
                MOVNE   r7, r5, LSR r6

                STR     r7, [sp, #32]
                LDMFD   sp!, {r4-r7, pc}


                END
```
