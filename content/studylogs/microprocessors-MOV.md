---
date: '2025-09-09T14:44:35+08:00'
title: 'Microprocessors MOV Function'
type: "studylogs"
---
{{< notice note >}}
_You should probably read this after you have read through the LDR STR Writeup for better understanding_
{{< /notice >}}

# What is MOV ?

Essentially, the MOV instruction copies data from a source to a destination:  
`MOV destination, source`  
Some important notes:  
- The data at the source is not modified
- The original data at the destination however is overwritten (if there is one)

For example:

#### 1.) `MOV r1, r2`  
This line will copy the value of r2 and overwrite whatever is in r1 with it.

#### 2.) `MOV r1, [r2]`  
This line will copy the value contained at the memory address of r2 into r1

#### 3.) `MOV [r1], r2`  
This line will copy the content of r2 and put it in the memory address contained by r1

---
## MOV Limitations
- No ARM instructions can contain a 32 bit immediate constant
- - All ARM instructins are fixed as 32 bits long
- The data processing instruction format has 12 bits avaible for operand2
- 4 bit rotate value (0 - 15) is multiplied by two to give range 0-30 in steps of 2
- Rule to remember is "8-bits shifted by an even number of bit positions"

{{< figure src="https://i.imgur.com/2lcw7Cj.png" alt="ROR" class="center">}}

---
# What is MOV with Rotation?

In Assembly (or in ARM architecture), every single instruction is 32 bits in size. The source register, destination register, shift amount, the main instruction itself (LDR, STR, MOV) and etc, all of these will ocupy bits in the 32 bits size.  

Specially for MOV, the max for immediate values is only 8 bits with a **_rotation_** bit of 4.  

So, the question is:

> What happens when we want to MOV a value that is 32 bits in size?

### Scenario 1: `MOV r6, #10`
In this case, we are moving the immediate value of 10 (1010B or 0xA) into r6. And in fact, we can just do that because 10 is a very small number.

### Scenario 2: `MOV r6, #0x8C`  
For this case, 0x8C is 140 in decimal, which is lesser than the max which is (2^8)-1 = 255.  

### Scenario 3: `#0xA400`
For this case, 0xA400 is 41984, which is, uh, bigger than 255. So, error occurs. What should we do here?  

### Solution for Scenario 3:  `MOV r6, #0xA4, 24`

Let's translate this line: First off we rotate A4 by 24 bits to the right, then we put it into r6.  
To get a clearer picture, we have to look from the binary angle:  
```text
00000000 00000000 00000000 10100100 // 0xA4 in binary form

                    // rotate to right by 24 bits, or to the left by 8 bits (adds up to 32 bits)
                      <--------
00000000 00000000 10100100 00000000 // now it becomes 0xA400, which is what we want to send originally
```

### Scenario 4: `#4096`

4096 --> 0x1000  --> 00000000 00000000 00010000 00000000B

### Solution for Scenario 4: `MOV r6, #0x40, 26`

```text
00000000 00000000 00000000 01000000 // 0x40 in binary form

                    // rotate to right by 26 bits, or to the left by 6 bits (adds up to 32 bits)
                      <--------
00000000 00000000 00010000 00000000 // now it becomes 0x1000, which is what we want to send originally
```

### Scenario 5: `#0x7D8`

0x7D8 --> 011111011000 B --> 00000000 00000000 00000111 11011000 B

### Solution for Scenario 5: `MOV r6, #0xFB`
```text
00000000 00000000 00000000 11111011 // 0x7D8 in binary form

                    // rotate to right by 29 bits, or to the left by 3 bits (adds up to 32 bits)
                      <--------
00000000 00000000 00000111 11011000 // now it becomes 0x7D8, which is what we want to send originally
```
But is this allow?   
_**NO**_  
{{< notice warning >}}
Remember that any 8-bits can only be shifted by an **EVEN** number of bit positions
{{< /notice >}}  

Since the only way to become 0x7DB is to shift an odd number of bit positions, this operation is impossible to perform for the `MOV` function.  

Instead, we can use: 
```ASM
LDR r2, =0x7D8
```
**remember that there's no hashtag behind the 0x7D8!*

### Scenario 6: `#0x17400`

0x17400 --> 00010111010000000000 B --> 00000000 00000001 01110100 00000000 B

### Solution for Scenario 6: `MOV r6, #0x5D`
```text
00000000 00000000 00000000 01011101 // 0x5D in binary form

                    // rotate to right by 22 bits, or to the left by 10 bits (adds up to 32 bits)
                      <--------
00000000 00000001 01110100 00000000 // now it becomes 0x17400, which is what we want to send originally
```

### Scenario 7: `#0x1980`

0x1980 --> 0001100110000000 B --> 00000000 00000000 00011001 10000000 B

### Solution for Scenario 7: `MOV r6, #0x66`
```text
00000000 00000000 00000000 01100110 B// 0x66 in binary form

                    // rotate to right by 26 bits, or to the left by 6 bits (adds up to 32 bits)
                      <--------
00000000 00000000 00011001 10000000 // now it becomes 0x1980, which is what we want to send originally
```

