+++
date = '2025-09-14T21:39:29+08:00'
title = 'Microprocessors TUT3'
+++

# Question 1
```ASM
        AREA Prog1, CODE, READONLY
        ENTRY

        MOV     r6, #10
        MOV     r4, r6
loop    SUBS    r4, r4, #1
        MULNE   r6, r4, r6
        BNE     loop
stop    B       stop
        
        END
```
`AREA Prog1, CODE, READONLY`  
`ENTRY`  
`END`  
These are called "Assembler Directives":
- AREA - for the assembler to create a block of code in the memory (reserving)
- CODE - The block created is a set of excecuted instructions
- AREA Prog1, CODE, READONLY - this means the assembler will create a block of code named "Prog1" which is only read only.
- ENTRY - the point where the program starts its execution
- END - the end of the program, all instructions after this point will be ignored by assembler

Line 1: `MOV r6, #10` 
- This instruction moves the immediate value 10 into register r6  

Line 2: `MOV r4, r6` 
- This instruction copies the values in r6 into r4
- Meaning, now both r4 and r6 have the value 10  

Line 3: `loop    SUBS r4, r4, #1` 
- SUBS means subtract with update to flags  
- The operation means subtracting r4 with 1, and stores the result back into r4  
- It also updates the condition flags  

Line 4: `MULNE   r6, r4, r6` 
- MULNE means multiply if not equal
- This means that the operation will only execute if the Zero flag is not set, meaning r4 =/= 0
- The operation would multiply r4 with r6, and stores it back in r6
- All together, it means, as long as r4 is not zero, multiply r4 and r6

Line 5: `BNE     loop` 
- BNE means Branch if Not Equal
- Remember the MULNE from just now? This is the same concept. As long as the zero flag is not set, aka r4 is not 0, branch back up to line 3 where you can see the loop in front

Line 6: `stop    B       stop`
- This is an infinite loop


> But what is the entire code telling us?

### Full Code Meaning  

- r6 starts with 10
- r4 is set also to 10
- Decrease r4 by 1, then multiply r6 with r4
- Stops loop until r4 becomes zero
- Final result is a Factorial of 10, of anything r6 has: 10! = 3628800
---

# Question 2
Show three different ways to clear all the bits in register r12 to zero. You may not use any registers other than r12.

`MOV r12, #0`  
Moves immediate value 0 into r12 and overwriting the value inside into zero

`SUB r12, r12, r12`  
Substract r12 with r12 itself and store it into itself again

`AND r12, 12, #0`  
Bitwise AND is basically doing the AND gate operation, thus, anything AND zero will give zero

`EOR r12, r12, r12`  
EOR is exclusive OR, meaning anything XOR with itself will give us zero again

# Question 3
Use an assembler directive to assign register r6 to the name bouncer

`name RN expr`  
RN directive defines a register name for a specified register  
`name` is the name to be assigned to the register
`expr` is a number from 0 to 15

ANSWER: `bouncer RN 6`

# Question 5
Give the directive to reserve a block of zeroed memory, holding 40 words and labeled coeffs.

The `SPACE` directive reserves a zeroed block of memory

`{label} SPACE expr`
where `expr` evaluates the number of zeroed bytes to reserve

ANSWER: `coeffs SPACE 160   ; 40 words is 160 bytes (40 x 4 = 160)`