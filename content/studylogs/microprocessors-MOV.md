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

### Scenario 3: `MOV r6, #0xA400`
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

### Scenario 4: Move #4096 to r6

4096 --> 0x1000  --> 00000000 00000000 00010000 00000000B

### Solution for Scenario 4: `MOV r6, #0x40, 26`

```text
00000000 00000000 00000000 01000000 // 0x40 in binary form

                    // rotate to right by 26 bits, or to the left by 6 bits (adds up to 32 bits)
                      <--------
00000000 00000000 00010000 00000000 // now it becomes 0x1000, which is what we want to send originally
```


### What if numbers are still too large?

`LDR rd, =const`
