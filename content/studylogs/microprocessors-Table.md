+++
date = '2025-10-12T10:22:52+08:00'
title = 'Microprocessors Table'
+++

## Arithmetic Progression

To enter to the theme park costs $5 and each ride cost $1.50. How much to play 10 games? And how many gamesc an I play for $35?


```ASM
        AREA APProgression, CODE

TABLE EQU 0x40000000
counter RN 4

        ENTRY

        MOV     r0, #5          ; n value = 5
        MOV     r1, #1          ; initial value a0 = 1
        MOV     r2, #4          ;  difference d value = 4
        MOV     counter, #0     ; initialize the counter as 0
        MOV     r3, #TABLE      ; point r3 at the starting memory address of TABLE

        STR     r1, [r3]        ; store r1 in memory of r3, which is the TABLE

loop
        ADD     counter, counter, #1
        CMP     counter, r0
        BGE     done

        ADD     r1, r1, r2      
        STR     r1, [r3, counter, LSL #2]

        B       loop

done    B       done

        END
```

### What's the difference between the table on top and the one we define at the bottom using DCD ?
- When we define a table using DCD, we are reserving that particular address as our table
- When we use EQU on top, we just define that address as TABLE, we do not actually reserve it

### Main Code Logic
- `ADD counter, counter #1`
- `CMP counter, r0`
- `BGE done`
- We increase counter by #1 every time we run the loop. 
- We then compare counter with r0 which is the n value (the term index of AP)
- If counter < r0, then we continue with the loop body, else we branch to done

- `ADD r1, r1, r2`
- This is the one that explains the AP: an ​= 1 + (n−1)*4

| Iteration | counter | r1 (value stored) | Explanation |
| --------- | ------- | ----------------- | ----------- |
| start     | 0       | 1                 | a₀ = 1      |
| 1st       | 1       | 1 + 4 = 5         | a₁ = a₀ + d |
| 2nd       | 2       | 5 + 4 = 9         | a₂ = a₁ + d |
| 3rd       | 3       | 9 + 4 = 13        | a₃ = a₂ + d |
| 4th       | 4       | 13 + 4 = 17       | a₄ = a₃ + d |

- `STR r1, [r3, counter, LSL#2]`
- We then need to store the result of r1 into r3 address, but since each number is a 32 bit word, and 1 word is 4 bytes
- We need to move 4 bytes up every time we store a new element in the TABLE
- Address = `[r3] + [counter] * 2^2`
