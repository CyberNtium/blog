+++
date = '2025-09-14T07:12:58+08:00'
title = 'Microprocessors Arithmetic'
+++

# Addition & Subtraction
#### Addition
- `ADD r1, r2, r3`
- - Add values of `r2` and `r3` then store them in `r1`
- - `r1 = r2 + r3`

#### Subtraction
- `SUB r1, r2, r3`
- - Subtract `r2` with `r3` and store it inside `r1`
- - `r1 = r2 - r3`
#### Reverse Subtraction
- `RSB r1, r2, r3`
- - Subtract `r3` with `r2` and store it inside `r1`
- - `r1 = r3 - r2`

## 64-bit Addition & Subtraction
#### 64-bit Addition

TODO

---

# Multiplication

- `MUL r4, r3, r1`
- - Multiply r3 and r1 then store inside r4
- - r4 = r3 * r1
TODO


## Multiplication by a Constant

- We can perform multiplication without using `MUL` by utilising the barrel shifter

- `MOV r1, r0, LSL#2`
- - Shifting values inside `r0` by `2^2=4` is the same as `r0 * 4` and store inside r1

#### Multiply by 5
- `ADD r0, r1, r1, LSL#2`
- - r0 = r1 + r1*4
- - r0 = r1 * 5

#### Multiply by 7
- `RSB r0, r1, r1, LSL#3`
- - r0 = r1 * 8 - r1
- - r0 = r1 * 7

---

## TUT5 Q3a - 135
- Line 1: `RSB r1, r4, r4, LSL#3`
- - `r1 = r4*8 - r4`
- - Shift r4 by 2^3 = 8 and minus r4 then store inside r1
- - `r1 = r4*7`
- Line 2: `ADD r0, r1, r4, LSL#7`
- - `r0 = r1 + r4*128`
- - Shift r4 again by 2^7=128 and add to r1 from just now
- - `r0 = r4*7 + r4*128`
- - Sum of these two becomes 135
- - `r0 = r4*135`

## TUT Q3b - 255
- `RSB r0, r4, r4, LSL#8`
- Reverse subtract: Shift r4 by 8 meaning times r4 by 2^8=256 and minus r4 itself 

## TUT Q3c - 18
- Line 1: `ADD r1, r4, r4`
- - `r1 = r4 + r4 = r4 * 2`
- Line 2: `ADD r0, r1, r4, LSL#4`
- - `r1 = r0 + r4*16`
- - `r1 = r4*2 + r4*16`
- - `r1 = r4*18`

## TUT Q3d - 16384
- `ADD r1, r4, r4, LSL#14`
- - r1 = r4 + r4*16384
- `SUB r0 , r1, r4`
- - r0 = r1 - r4
- - r0 = r4 * 16385 - r4
- - r0 = r4 * 16384