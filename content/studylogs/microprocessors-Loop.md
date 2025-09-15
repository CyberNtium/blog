+++
date = '2025-09-14T08:40:32+08:00'
title = 'Microprocessors Loop'
+++

# Loop Example
This is a program that counts the number of leading zeroes in a register
```ASM
        AREA MyCode, CODE, READONLY
        ENTRY

start
        MOV r3, #0x00F00000   ; test value
        MOV r4, #0            ; clear shift counter
        CMP r3, #0
        BLE finish

loop
        MOVS r3, r3, LSL #1   ; shift left one bit
        ADD r4, r4, #1        ; increment counter
        BPL loop              ; repeat if MSB still 0

finish
        B finish              ; stop here (infinite loop)

        END

finish
```
- `MOV r3, #0x00F00000`
- - This line first set the r3 value to a value that we want
- `MOV r4, #0`  
- - This line resets the counter, it's not really necessary, but a good practice
- `CMP r3, #0`
- `BLE finish`
- - This two lines *compares* r3 value with immediate constant zero, if yes, then branch to finish below; If no, then go into the loop
- - `BLE` means "branch if less than or equal" for the previous line
- - In this case, `BLE` will branch to finish if the previous `CMP` outputs <= 0 value, meaning, if r3 is less than or equal to 0, skip the loop and go the finish immediately

- `MOVS r3, r3, LSL #1`
- - r3 = 00000000 11110000 00000000 00000000 B = 0x00F00000 (original)
- - shift left by 1 bit
- - r3 = 00000001 11100000 00000000 00000000 B = 0x01E00000 (shifted)

- `ADD r4, r4, #1`
- - Update the counter for each bit shifted for r3
- - r4 = r4 + 1

- `BPL loop`
- - BPL means "**B**ranch if **PL**us", meaning it checks if the N flag is zero or one
- - If zero, repeat the loop
- - If one, exit loop
- - So, loop is repeated as long as the MSB of r3 is 0. 
- - This is because as we shift r3 left more and more, we will reach a point where the MSB is not zero, which is when we have counted all the leading zeroes
- - 11110000 00000000 00000000 00000000 B
- - <----------
---

# For Loop Example
#### Example in C language

```C
for (j=0; j<10; i++){
    ...
}
```

#### In Assembly

```ASM
        MOV r1, #0
loop
        CMP r1, #10
        BGE Done
        ...
        ADD r1, r1, #1
        B loop
Done
```

- `MOV r1, #0`
- - This line clears the loop counter
- `CMP r1, #10`
- `BGE Done`
- - First line compares r1 with the value 10. 
- - BGE means "Branch if greater or equal" 
- - So, if r1 is >= 10, branch to Done. If not, continue down
- `ADD r1, r1, #1`
- `B loop`
- - First line updates the loop counter, r1 = r1 + 1
- - Then branch back up to loop
---

# Count Down Loop
We should always use a count down loop if possible because it saves a CMP operation, meaning more efficient

```ASM
    MOV r1, #10
loop
    ...
    SUBS r1, r1, #1
    BGT Loop
Done
```

- `BGT Loop` means "Branch if greater than"
- - This means that if the previous operation outputs more than zero, it will branch to loop, repeating it
- - If the previous operation outputs less than zero, exit loop
- - In this case, r1 - 1 will eventually reach 0 or negative, which will trigger BGT to not branch, effectively exiting the loop
---

# Summation Loop Example

```ASM
        AREA SummationLoop, CODE, READONLY
        ENTRY

        MOV     r0, #0
        MOV     r1, #5
        ADR     r2, arrayA
loop  
        LDR r3, [r2, r1, LSL #2]
        ADD r0, r0, r3
        SUBS r1, r1, #1
        BGE loop

Done    B   Done

arrayA  DCD -1, -2, -3, -4, -5, -6

        END
```
- `MOV r0, #0`
- `MOV r1, #5`
- `ADR r2, arrayA`
- - First line is to initialise sum as 0
- - Second line is to initialise the counter as 5 since we are counting from 0 to 5 (6 numbers)
- - Third line is to load the address of the arrayA to r2

- `LDR r3, [r2, r1, LSL #2]`
- - We load the LSB number in the arrayA into r3. 
- - Remember LDR operation, r2 + r1*4, and since r1 is 5, total is r2 + 20
- - We do LSL #2 because each word inside arrayA occupies 4 bits

- `ADD r0, r0, r3`
- `SUBS r1, r1, #1`
- `BGE loop`
- - Then, we do r0 = r0 + r3
- - Update the counter, r1 = r1 - 1
- - BGE stands for "Branch if Greater Than or Equal". Since r1 is >= 0, repeat the loop

- `arrayA DCD -1, -2, -3, -4, -5, -6`
- - DCD stands for Define Constant Data (32-bit words)
---
# Greatest Common Divisor Algorithm Example

```ASM
gcd     
        CMP     r0, r1
        BEQ     finish
        BLT     less

        SUB    r0, r0, r1
        B       gcd

less    
        SUB    r1, r1, r0
        B       gcd

finish
```
- `CMP r0, r1`
- - Compare if r0 is equal to r1

- `BEQ finish`
- - BEQ means "Branch if Equal", so branch to "finish" if r0 = r1

- `BLT less`
- - BLT stands for "Branch if less than", so branch to the "less" loop if r0 < r1

- `SUB r0, r1, r1`
- `B gcd`
- - r0 = r0 - 1
- - branch back to "gcd", repeating the loop

- `SUB r1, r1, r0`
- - This is in the "less" loop because r0 < r1
- - r1 = r1 - r0

- `B gcd`
- - branch back to "gcd"

#### Numbered Example:
```text
a = 18, b = 6
First Loop:  a > b
             a = 18 - 6 = 12
             b = 6
Second Loop: a > b
             a = 12 - 6 = 6
             b = 6
Third Loop:  a = b
             Done
```
## But the loop can be better:

```ASM
gcd
        CMP     r0, r1
        SUBGT   r0, r0, r1
        SUBLT   r1, r1, r0
        BNE     gcd
Done
```
- Compare if r0 is equal to r1
- r0 - r1 if r0 > r1
- r1 - r0 if r0 < r1
- BNE, branch if r0 is not equal to r1, once equal go next
---

# Other Useful Condition Codes
{{< figure src="https://i.imgur.com/4yJINf2.png" alt="conditionCode" class="center" >}}

---

# TUT5 Q4
Find the maximum value in a list of 32-bit values located in memory. Assume the values are in two’s complement representations. Your program should have 50 values in the list.

```ASM
        AREA MaxFinder, CODE READONLY
        ENTRY

max     RN      1
counter RN      0

        LDR     counter, =49    ; initialise counter as 49 since we start at 0
        ADR     r2, Values      ; load the address of the array below into r2
        LDR     max, [r2], #4   ; load the first element of array as max, post-index increment by 4

loop
        LDR     r4, [r2], #4   ; load the 2nd element which was performed by the post-index just now into r4, and post-index increase by #4 again
        CMP     max, r4         
        MOVLT   max, r4
        SUBS    counter, counter, #1
        BNE     loop

Done    B       Done

Values
        DCD     -847   321  -456   789  -123   654  -987   234   567  -890
        DCD     412  -678   901  -345   876  -234   543  -789   123   456
        DCD     -567   890  -321   678  -901   345  -876   234  -543   789
        DCD     234  -456   789  -123   567  -890   321  -678   901  -345
        DCD     -789   123  -654   987  -234   456  -789   567   890  -321

        END
```

- `max RN 1`
- `counter RN 0`
- - Name register 1 as max to contain the maximum and register 0 as counter for loop counter
- - RN (i think) stands for "register name", could be wrong though\

- `LDR  counter, =49`
- `ADR  r2, Values`
- `LDR  max, [r2], #4`
- - Set counter to be 49 because we start counting from 0 to 49, aka 50 times
- - Load the starting address of the array into r2
- - Load the first index of the array into r2, then perform post-index increment by 4 because each word in the DCD array is 4 bits. Now r2 points to the 2nd element

- `LDR r4, [r2], #4`
- `CMP max, r4`
- `MOVLT max, r4`
- `SUBS counter, counter, #1`
- `BNE loop`
- - Inside the loop, we load the second element of the array into a new register, r4. Then post-index increment by #4. r2 now points at the 3rd element
- - Compare max and r4
- - If max is less than r4, update max by replacing with r4
- - Update counter by subtracting one, update the S Flag
- - BNE, branch to loop again if counter is not equal to zero

# TUT5 Q5
Convert the following C iteration statements into assembly language.

## TUT5 Q5a
### C Code
```C
for (i=0; i<10; i++) {
        ...
}
```

### Assembly Code
```ASM
        MOV r1, #0
loop
        ...
        ADD r1, r1, #1
        CMP r1, #10
        BLT loop
```

## TUT5 Q5b
### C Code
```C
for (i = 1; i < 11; i ++) {
        for (j = 5; j > 0; j --) {
                ...
        }
}
```

### Assembly Code
```ASM
        MOV r0, #0
loop0
        MOV r1, #5

loop1   ...
        SUBS r1, r1, #1
        BNE loop1

        ADD r0, r0, #1
        CMP r0, #11
        BLT loop0
```

## TUT5 Q5c
### C Code
```C
for (i = 18; i > 3; i --) {
        for (j = 16; j < 40; j ++) {
                for (k = 0; k < 30; k ++) {
                        ...
                }
        }
}
```

### Assembly Code
```ASM
        MOV r0, #18
loop0   
        MOV r1, #16
loop1
        MOV r3, #0
loop2   ...
        ADD r3, r3, #1
        CMP r3, #30
        BLT loop2

        ADD r1, r1, #1
        CMP r1, #40
        BLT loop1

        SUB r0, r0, #1
        CMP r0, #3
        BGE loop0
```





