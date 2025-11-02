+++
date = '2025-11-02T01:03:17+08:00'
title = 'Microprocessors TUT8'
+++

# Tutorial 8

## TUT7 Q2
Write an ARM subroutine in assembly to compute factorial of n, using a full descending stack, for each of the following methods of passing parameters. Assume that register r0 contains n (=10) and the result will be stored in register r1.  
- a) Passing parameters to/from the subroutine using pass-by-register.  
- b) Passing parameters to/from the subroutine using pass-by-stack.

### Pass by Register Method
```ASM
        AREA FactorialPassByRegister, CODE, READONLY

ramBase EQU  0x40000000

        ENTRY

        LDR     sp, =ramBase + 0x100        ; initial stack pointer
        MOV     r0, #10                     ; n = 10, pass by register
        BL      factSub                     ; call the factorial subroutine

stop    B       stop

factSub
        STMFD   sp!, {r4, r5, lr}           ; preserve r4, r5 and lr by pushing to stack
        MOV     r4, r0                      ; copy n to a temporary counter
        MOV     r1, r4                      ;

loop
        SUBS    r4, r4, #1                  ; decrease counter and upadate flag 
        MULNE   r5, r1, r4                  ; multiply if r1 and r4 not equal, store in r5
        MOVNE   r1, r5                      ; move r5 into r1 if not equal
        BNE     loop1                       ; branch back to loop if SUBS flag not equal zero
        LDMFD   sp!, {r4, r5, pc}           ; pop r4 and r5 from stack, also with pc so that the pc goes back to main sub

        END
```
### Explanation

- `ramBase EQU  0x40000000` - We first define a constant ramBase with value `0x40000000`. This is used as the base address

- `LDR sp, =ramBase + 0x100` - Load `0x40000000 + 0x100 = 0x40000100` into the stack pointer, sp
- - We do this so that when we branch into the subroutine, we can push and pop registers easily in the stack

- `MOV r0, #10` - Moves value #10 into r0, this is the input for `n` for the factorial function. Meaning, in this case, we will be outputing what is `n! = 10!`

- `BL factsub` - Finally, we branch into the factorial subroutine. 
- - Remember that BL does 2 things:
- - - The program counter, pc will point to the address of the first line of the subroutine
- - - The address of the immediate next instruction of `BL factsub`, which is `stop B stop`, will be store in lr. This serves as the returning address for pc

| Address (hex) | Instruction                | Comment                   |
| ------------- | -------------------------- | ------------------------- |
| `0x0000`      | `LDR sp, =ramBase + 0x100` | 4 bytes                   |
| `0x0004`      | `MOV r0, #10`              | 4 bytes                   |
| `0x0008`      | `BL factSub`               | 4 bytes                   |
| `0x000C`      | `stop B stop`              | next instruction after BL |

- `factSub`
- `STMFD sp!, {r4, r5, lr}` - Upon entering the subroutine, we perform a Store Multiple Full Descending (STMFD), this pushes r4, r5 and lr into the stack
- - Why? Because we will be using r4 and r5 in the subroutine, so it's always good to preserve the original value of them even if they are holding nothing

- `MOV r4, r0` - Copies the value in r0 into r4. r4 now serves as the loop counter
- `MOV r1, r4` - Copies the value of r4 to r1, which serves as the first and current factorial result

- `loop`
- `SUBS r4, r4, #1` - We now enter the loop. Subtract loop counter by 1 each loop, update the status flag for branching at the loop end

- `MULNE r5, r1, r4` - if r4 not zero, multiply r1 and r4 then store inside r5
- - Before loop, n = 10, r4 = 10, r1 = 10
- - First loop, r4 = 9, r1 = 10 --> r5 = r4 x r1 = 9 x 10

- `MOVNE r1, r5` - if r4 still not zero, store the product r5 into r1 to update it. 
- - Basically we keep doing this since we only update r4 minus subtracting 1, essentially creating a factorial of n*(n-1)*(n-2)...

- `BNE loop` - Branch back to loop if r4 not zero

- `LDMFD sp!, {r4, r5, pc}` - Load Multiple Full Descending, we pop r4, r5 and lr from the stack 
- - Why pc this time? Remember when we push r4, r5 and lr into the stack? Since we are popping them out in the reversed order, the lr value will be loaded into pc, meaning the return address just now is in pc, meaning we can go back to the main sub.


### Pass By Stack Method
```ASM
        AREA FactorialPassByStack, CODE, READONLY
ramBase EQU 0x40000000

        ENTRY

        LDR     sp, =ramBase + 0x100
        MOV     r0, #10
        STMFD   sp!, {r0, r1}

        BL      factSub

stop    B       stop


factSub
        STMFD   sp!, {r4-r6, lr}
        LDR     r4, [sp, #16]
        MOV     r5, r4

loop    
        SUBS    r4, r4, #1
        MULNE   r6, r5, r4
        MOVNE   r5, r6
        BNE     loop

        STR     r5, [sp, #20]

        LDMFD sp!, {r4-46, pc}

        END
```

### Explanation
Passing by stack is very confusing without using a table, so I recommend that you write it out in a table during practice or exams.  

Initial Stack Table:  
| Address (hex) | Instruction    | Comment   |
| ------------- | ------------   | ----------|
| `0x0000`      |                | <--- sp   |

There's nothing here yet since we have not pushed anything in.


- `LDR sp, =ramBase + 0x100`
- - Similar to passing by register, we first define where our stack will be at, which is pointed by the sp at `0x40000000 + 0x100 = 0x40000100`
- - This means from now on, all shit we push into the stack will start at `0x40000100`

- `MOV r0, #10` - Input of factorial, `n! = 10!`

- `STMFD sp!, {r0, r1}`
- - Here we push r0 and r1 into the stack as parameters that we will use in the subroutine. Note that this is Full Descending post increment for sp, this means that sp will decrement (go down) after pushing in a value
- - As such:

| Address (hex) | Registers        | Stack Pointer   |
| ------------- | ------------     | --------- |
| `0x40000100`  |      --          |           |
| `0x400000FC`  |      r1          |           |
| `0x400000F8`  |      r0          | <--- sp   |

- Stack will always have the smallest register at the bottom, that's why r1 is pushed in first and then sp goes down, then push in r0
- I recommend doing everything in decimal first, then change to hexadecimal. For example:
- - 256 = 0x100 
- - 252 = 0x0FC , decrement 4 bytes
- - 248 = 0x0F8 , decrement 4 bytes

- `BL factSub`  Branch into the factorial subroutine

- `factSub`
- `STMFD sp!, {r4-r6, lr}` 
- After we branch into the subroutine, we also push r4, r5, r6 and lr into the stack. But, this time, the purpose is to perserve the value of these registers as compared to r0 and r1 which are used for parameters of the factorial function

| Address (hex) | Registers        | Stack Pointer   |
| ------------- | ------------     | --------- |
| `0x40000100`  |      --          |           |
| `0x400000FC`  |      r1          |           |
| `0x400000F8`  |      r0          |           |
| `0x400000F4`  |      lr          |           |
| `0x400000F0`  |      r6          |           |
| `0x400000EC`  |      r5          |           |
| `0x400000E8`  |      r4          | <--- sp   |

- Same convention as before, bigger register then smaller register

- `LDR r4, [sp, #16]`
- - Read up on `LDR` for more details. [Link](https://cyberntium.github.io/studylogs/microprocessors-ldr-str-instructions/)
- - Essentially, this is pre-indexing of LDR, meaning we offset sp by a set number and load the stuff its pointing at into r4
- - In this case, `[sp, #16]`, we will be offsetting by 16 bytes. If you look at the table above, sp is currently at `0x400000E8`. So, we increase that by #16, which lands at `0x400000F8`, which houses value of r0.
- - Finally, store that value in r4. Now r4 holds the value #10

- `MOV r5, r4` - Copies r4 value into r5 

- `loop`
- `SUBS r4, r4, #1` - Decrement loop counter and update status flag

- `MULNE r6, r5, r4` - If r4 is not equal to zero, multiply r5 and r4, store the product in r6

- `MOVNE r5, r6` - If r4 still not equal to zero, copy product of r6 into r5 again. Essentially update r5

- `BNE loop` - If r4 not equal to zero, branch back to loop

- `STR r5, [sp, #20]` - Stores values held by r5 into the memory address pointed at by sp

| Address (hex) | Registers        | Stack Pointer   |
| ------------- | ------------     | --------- |
| `0x40000100`  |      --          |           |
| `0x400000FC`  |      r1          |           |
| `0x400000F8`  |      r0          |           |
| `0x400000F4`  |      lr          |           |
| `0x400000F0`  |      r6          |           |
| `0x400000EC`  |      r5          |           |
| `0x400000E8`  |      r4          | <--- sp   |

- - Let's take a look at the table again:
- - `[sp, #20]` - This time its 20 bytes, so we increase by 20, which gives us:
- - `232 (0xE8) + 20 (0x14) = 252 (0xFC)` - Which is r1
- - Essentially, we store the product of r5 into r1


- `LDMFD sp!, {r4-r6, pc}` - Finally, pop out the registers we pushed in at the start of the subroutine. Most importantly, the sp will go up after popping, meaning the order will stay the same,
- After popping:
| Address (hex) | Registers        | Stack Pointer   |
| ------------- | ------------     | --------- |
| `0x40000100`  |      --          |           |
| `0x400000FC`  |      r1          |           |
| `0x400000F8`  |      r0          | <--- sp   |

- `LDMFD sp!, {r0, r1}` - We made it back to the main routine. But notice that the stack still have shit inside, so pop them out accordingly.
- Final table:
| Address (hex) | Registers        | Stack Pointer   |
| ------------- | ------------     | --------- |
| `0x40000100`  |      --          | <--- sp   |
- Perfectly balanced, as all things should be

{{< figure src="https://i.imgur.com/JOPpyrM.jpeg" alt="thanos" class=center >}}

---

## TUT7 Q3
Write an ARM assembly program to sum the square of 4 numbers (sum = x1*x1 + x2*x2 + x3*x3 + x4*x4). The main program should call a subroutine to add a list of number. This subroutine would then call another subroutine to perform the square operation. Test it out using Keil uVision and observe the content of registers sp, lr and pc during subroutine calls and returns. Let register r0 be the pointer to the numbers and register r1 contains the final result.

```ASM
        AREA TwoSubroutineCall, CODE, READONLY
stackBase EQU 0x40000000

        ENTRY

        LDR     sp, =stackBase + 0x100
        ADR     r0, dataTable

        BL      AddSqNum

stop    B       stop

dataTable       DCD     1, 2, 3, 4

AddSqNum
        STMFD   sp!, {r4-r6, lr}
        MOV     r1, #0
        MOV     r4, #4

loop
        LDR     r5, [r0], #4

        STMFD   sp!, {r5, r6}
        BL      squareSub
        LDMFD   sp!, {r5, r6}

        ADD     r1, r1, r6
        SUBS    r4, r4, #1
        BNE     loop
        LDMFD   sp!, {r4-r6, pc}

squareSub
        STMFD   sp!, {r4, r5, lr}
        
        LDR     r4, [sp, #12]
        MUL     r5, r4, r4
        STR     r5, [sp, #16]

        LDMFD   sp!, {r4, r5, pc}

        END
```

### Explanation
We will be skipping over to the subroutine code immediately since the first few lines are pretty simple to understand, refer to question 2 for easy examples.  

Initial Stack:
| Address (hex) | Registers        | Stack Pointer   |
| ------------- | ------------     | --------- |
| `0x40000100`  |      --          | <--- sp   |
#### AddSqNum Subroutine
```ASM
AddSqNum
        STMFD   sp!, {r4-r6, lr}
        MOV     r1, #0  
        MOV     r4, #4
```
- `STMFD sp!, {r4-r6, lr}` - As usual, we push the original registers into stack to preserve their values. In this case, r4, r5 and r6. lr is the returning address thus we also need it. (refer to TUT7Q2 to see why lr is here)
- `MOV r1, #0` - Initialise the sum value in r1 as zero
- `MOV r4, #4` - Initialise loop counter, in this case we have 4 numbers in the dataTable, thus 4 loops

Current Stack:
| Address (hex) | Registers        | Stack Pointer   |
| ------------- | ------------     | --------- |
| `0x40000100`  |      --          |           |
| `0x400000FC`  |      lr          |           |
| `0x400000F8`  |      r6          |           |
| `0x400000F4`  |      r5          |           |
| `0x400000F0`  |      r4          | <--- sp   |

```ASM
loop
        LDR     r5, [r0], #4
```
- We now enter the loop of the AddSqNum subroutine.
- `LDR r5, [r0], #4` - This is post-indexing LOAD, meaning after we load the value in the address of `[r0]`, increase by 4 bytes
- - A very easy way to visualise this is, again, draw it out:
- dataTable:
- {{< figure src="https://i.imgur.com/ciItX5e.jpeg" alt="LDR">}}

```ASM
STMFD sp!, {r5, r6}
BL      squareSub
```
- Before we branch into the second subroutine, we need to push in some parameters. In this case, r5 and r6. Not gonna lie, I have no idea why we are pushing r6 but it's the prof's code, so let's just go with it.

Current Stack:
Current Stack:
| Address (hex) | Registers        | Stack Pointer   |
| ------------- | ------------     | --------- |
| `0x40000100`  |      --          |           |
| `0x400000FC`  |      lr          |           |
| `0x400000F8`  |      r6          |           |
| `0x400000F4`  |      r5          |           |
| `0x400000F0`  |      r4          |           |
| `0x400000EC`  |      r6          |           |
| `0x400000E8`  |      r5          | <--- sp   |

- Then we branch into the squareSub subroutine

#### squareSub Subroutine
```ASM
STMFD   sp!, {r4, r5, lr}
```
- As usual, every time we branch into a subroutine, push to preserve the initial register values:
| Address (hex) | Registers        | Stack Pointer   |
| ------------- | ------------     | --------- |
| `0x40000100`  |      --          |           |
| `0x400000FC`  |      lr          |           |
| `0x400000F8`  |      r6          |           |
| `0x400000F4`  |      r5          |           |
| `0x400000F0`  |      r4          |           |
| `0x400000EC`  |      r6          |           |
| `0x400000E8`  |      r5          |           |
| `0x400000E4`  |      lr          |           |
| `0x400000E0`  |      l5          |           |
| `0x400000DC`  |      r4          | <--- sp   |

```ASM
LDR     r4, [sp, #12] 
MUL     r5, r4, r4 
STR     r5, [sp,#16] 
LDMFD   sp!,{r4, r5, pc}
END
```
- `LDR r4, [sp, #12]` - Take a look at the stack table: We offset the sp by 12 bytes, which will land at `0x400000E8`, which houses r5. Thus, we load the r5 value into r4.
- `MUL r5, r4, r4` - This is essentially the square function since we are multiplying r4 by itself, then storing it inside r5
- `STR r5, [sp, #16]` - Again, offset by 16 bytes, this times it lands at `0x400000EC` which houses r6. Since this is STR, we store the product r5 into r6 pointed at by the address.
- `LDMFD sp!, {r4, r5, pc}` - Finally, when the subroutine ends, pop out the values. In this case, r4 and r5 since we pushed them in just now. But pushing out lr, we push it into pc, because it functions as the returning address to AddSqNum subroutine.

Current Stack:
| Address (hex) | Registers        | Stack Pointer   |
| ------------- | ------------     | --------- |
| `0x40000100`  |      --          |           |
| `0x400000FC`  |      lr          |           |
| `0x400000F8`  |      r6          |           |
| `0x400000F4`  |      r5          |           |
| `0x400000F0`  |      r4          |           |
| `0x400000EC`  |      r6          |           |
| `0x400000E8`  |      r5          | <--- sp   |

#### Back to AddSqNum
```ASM
...
LDMFD   sp!, {r5, r6}
ADD     r1, r1, r6 
SUBS    r4, r4, #1 
BNE     loop
LDMFD   sp!, {r4-r6, pc} 
```
- `LDMFD sp!, {r5, r6}` - After returning from squareSub, remember to also pop out the original r5 and r6 that we pushed in before entering sqaureSub
Current Stack:
| Address (hex) | Registers        | Stack Pointer   |
| ------------- | ------------     | --------- |
| `0x40000100`  |      --          |           |
| `0x400000FC`  |      lr          |           |
| `0x400000F8`  |      r6          |           |
| `0x400000F4`  |      r5          |           |
| `0x400000F0`  |      r4          | <--- sp   |

- Since we are still inside the loop, let's continue:
- `ADD r1, r1, r6` - We add r6 with r1, and store inside r1 itself. Now, if you remember from the squareSub, we stored the product of the square inside the offset, which happens to be r6. Essentially, we are adding r6 to the culmulative sum, r1 which currently is just zero. When we update the loop counter, r5 which is the input parameter will produce a different r6 square value in the squareSub. Then again, back in AddSqNum, add to r1, rinse and repeat. Take some time to understand this. 
- `SUBS r4, r4, #1` - Decrement loop counter
- `BNE loop` - If r4 not equal to zero, branch back to loop

- `LDMFD sp!, {r4-r6, pc}` - Finally, before we go back to the main routine, pop out the registers we preserved at the start of AddSqNum.
Current Stack:
| Address (hex) | Registers        | Stack Pointer   |
| ------------- | ------------     | --------- |
| `0x40000100`  |      --          | <--- sp   |


_Magnificent, isn't it?_



