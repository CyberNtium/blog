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
- So what's the difference here between passing by stack versus passing by register?

### The ARM APCS (AAPCS) - Application Procedure Call Standard
Don't be afraid of the long ass name, it is just a standard, like any rules and regulation.  
Imagine a Standard Operating Procedure (SOP) during the the Covid-19 era, where only half the class is allowed to come to school while the other half stays online.  
AAPCS is essentially similar.

- AAPCS defines how subroutines should be written 
- Most importantly, which registers are parameters and which registers should be preserved
| Registers  |  Implications |
|------------| ------------- |
|   r0 - r3  | Parameters into and results from subroutines    | 
|   r4 - r11 |  Perserved    | 
|     r12    | Scratch Register   | 
|     r13    | stack pointer (sp) | 
|     r14    | link register (lr)    | 
|     r15    | Program counter (pc)    | 
- - Basically:
- - `r0 - r3` is used as parameters to send into subroutines. If we only were to use 4 registers max, then it's okay just use passing by register method. 
- - However, when programs get complicated, we tend to use more than 4 registers, thus why we will be using passing by stack, which is when we need to use `r4 - r11`
- - But at the end, it still depends on the question, if they ask for stack then do stack and vice versa

Anyway, that was kinda boring, let's get back to passing by stack:

```ASM
                LDR     sp, =ramBase + 0x100
                MOV     r0, #0     
                LDr     r1, =0x00001234
                MOV     r2, #4
```
The initial part remains totally similar to passing by register

```ASM
                STMFD   sp!, {r0-r3}
                BL      shiftFunc
```
- This part is more important, let's take a closer look:
- - `STMFD sp!, {r0-r3}`
- - Initial stack:
- - | Address (hex) | Registers        | Stack Pointer   |
| ------------- | ------------     | --------- |
| `0x40000100`  |      --          | <--- sp   |
- - STORE MULTIPLE FULL DESCENDING, with post indexing of your sp for r0 to r3
- - What we are doing here is pushing r0 to r3 into the stack but also updating the sp location with the "!" symbol:
- - | Address (hex) | Registers        | Stack Pointer   |
| ------------- | ------------     | --------- |
| `0x40000100`  |      --          |           |
| `0x400000FC`  |      r3          |           |
| `0x400000F8`  |      r2          |           |
| `0x400000F4`  |      r1          |           |
| `0x400000F0`  |      r0          | <--- sp   |
- As you can see, full descending means that whenever we want to push something in, sp will decrement first then it pushes shit in. Right now, our stack contains these registers which will be the parameters that will be used in the subroutine
- Note that everything you push anything in stack, bigger register number stays top and smaller stays bottom

```ASM
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
- Now that we are in the subroutine, it's healthy practice to perserve registers that we will be using inside the subroutine:
- - `STMFD sp!, {r4-r7, lr}`
- - We push r4 to r7 into the stack for preserving, but lr is pushed also because it is the return address, thats why it has to be reserved
- - Current stack:
- - | Address (hex) | Registers        | Stack Pointer   |
| ------------- | ------------     | --------- |
| `0x40000100`  |      --          |           |
| `0x400000FC`  |      r3          |           |
| `0x400000F8`  |      r2          |           |
| `0x400000F4`  |      r1          |           |
| `0x400000F0`  |      r0          |           |
| `0x400000EC`  |      lr          |           |
| `0x400000E8`  |      r7          |           |
| `0x400000E4`  |      r6          |           |
| `0x400000E0`  |      r5          |           |
| `0x400000DC`  |      r4          | <--- sp   |
- Same convention, lr is bigger so it goes in first and so on and so forth
- - So back to the parameters thing, what does that mean actually? 
- - `LDR r4, [sp, #20]`
- - `LDR r5, [sp, #24]`
- - `LDR r6, [sp, #28]`
- - As we learnt from LDR with preindexing offset, we add the #number to the sp and load it into register
- - First case: since sp is at `0x400000DC`, we add 20 bytes, which becomes, `0x400000F0` which houses r0. Thus, we load r0 value into r4.
- - Second case, same thing, `0x400000DC`, add 24 bytes -> `0x400000F4` which houses r1, load it into r5
- - Third case, add 28 bytes, `0x400000F8` which houses r2, load it into r6
- So essentially, we are "using" r0, r1 and r2 as parameters, just that we copy their values into r4, r5 and r6
- And that's it. After this is just your normal operations
- Then before we go back to main routine:
- - `STR r7, [sp, #32]`
- - add 32 bytes -> `0x400000FC` which houses r3, store the output r7 into r3 
- - `LDMFD sp!, {r4-r7, pc}`
- - Finally, when subroutine is done, pop up all preserved registers:
- - Current Stack:
- - | Address (hex) | Registers        | Stack Pointer   |
| ------------- | ------------     | --------- |
| `0x40000100`  |      --          |           |
| `0x400000FC`  |      r3          |           |
| `0x400000F8`  |      r2          |           |
| `0x400000F4`  |      r1          |           |
| `0x400000F0`  |      r0          | <--- sp   |
- - Note that lr is pop into pc, because remember, lr has the returning addressing, and pc now knows where to return to, back to main

```ASM
                STMFD   sp!, {r0-r3}
                BL      shiftFunc
                LDMFD   sp!, {r0-r3}
```
- Finally, we reached `LDMFD sp!, {r0-r3}`
- We pop out the registers that we initially pass as parameters through stack:
- Current stack:
- - | Address (hex) | Registers        | Stack Pointer   |
| ------------- | ------------     | --------- |
| `0x40000100`  |      --          |           |
- - Note that r0, r1 and r2 still has the same value as before. but r3 now has the final output we did inside the subroutine