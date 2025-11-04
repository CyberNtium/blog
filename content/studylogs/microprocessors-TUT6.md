+++
date = '2025-11-04'
title = 'Microprocessors TUT6'
+++

# Tutorial 6

## Question 1
Write an assembly program to construct a table of Fibonacci numbers for n = 0 to 20. The starting address of the table in memory is 0x40000000. In mathematics, the Fibonacci numbers or Fibonacci series or Fibonacci sequence are the numbers in the following integer sequence. Assume that the values are 32-bit data. 
> 0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, ...

By definition, the first two numbers in the Fibonacci sequence are 0 and 1, and each subsequent number is the sum of the previous two. Hence, the sequence Fn of Fibonacci numbers is defined by the recurrence relation:
`Fn = F(n-1) + F(n-2)`
where F0 = 1 and F1 = 1.  

Wow this question sure is a mouthful. Let's just break it down into simpler tasks:

- Write an Assembly program that can compute the Fibonacci Sequence from n=0 until n=20. Store the results into a table.
- The starting address of the table is `0x40000000`
- The Fibonacci Sequence is such that a given F(n) is equal to the sum of its previous two iteration, F(n-1) and F(n-2)
- F0 = 0 and F1 = 1
- And that's it, let's go:

```ASM
        AREA Fibonacci, CODE, READONLY
N       RN  0   ; n value
base    RN  1   ; starting address of table 
FN_2    RN  2   ; F(n-2)
FN_1    RN  3   ; F(n-1)
FN      RN  4   ; F(n)

tableBase   EQU     0x40000000

        ENTRY

        MOV     N, #0
        MOV     base, #tableBase
        MOV     FN_2, #0                    ; value of F(0)                    
        STR     FN_2, [base, N, LSL#2]      ; Store F(0), offset, N + 2^2

        ADD     N, N, #1                    ; n = n + 1 = 0 + 1 = 1
        MOV     FN_1, #1                    ; Value of F(1) 
        STR     FN_1, [base, N, LSL#2]      ; Store F(1)
        ADD     N, N, #1                    ; n = n + 1 = 1 + 1 = 2

loop
        ADD     FN, FN_2, FN_1              ; F(n) = F(n-2) + F(n-1)
        STR     FN, [base, N, LSL#2]        ; Store F(n)
        MOV     FN_2, FN_1                  ; Update F(n-2)
        MOV     FN_1, FN                    ; Update F(n-1)
        ADD     N, N, #1                    ; Increment n, n = n + 1
        CMP     N, #21                      ; compare N with #21
        BLT     loop                        ; if N less than 21, branch back to loop

stop    B       stop

        END
```
Final Ouput at `0x40000000`:
{{< figure src="https://i.imgur.com/nQhAJxD.png" alt="fibonacci" class=center >}}

---

## Question 2
Write an assembly program to examine a table (a list of 32-bit values) sequentially for a match with a search key. Store the search key as a new entry if no match is found by adding it to the end of the list (highest address). Assume that the first value in the list indicates the length of the list and load it to register r2 (assume =4). Register r0 initially point to the starting address of the list. It will eventually point to the address of the matched item if there is a match. Register r1 contains the search item (assume =0x9ABCDEF0). You need to include the initialization file: `tut7q2_L2104.ini`.  

Again let's break down this question into simpler tasks:
- Given a search key, examine a table for a matching value
- Store the search key as a new entry if there's no matching value by adding to the end of the list
- The first value of the list is the length of the list, load it into r2 (assume it's 4)
- r0 points at the starting address of the list, but will point at the address of the matched item if there is one
- r1 contains the search key (assume =0x9ABCDEF0)
- We are required to include the initialization file, Tut7q2_L2104.ini:
```ini
MAP 0x40000000, 0x40002000 EXEC READ WRITE 
E LONG 0x40000000 = 0x4, 0x12345678, 0x56789ABC, 0x9ABCDEF1, 0xDEF01234 
```
- Let's start:

```ASM
        AREA SearchTable, CODE, READONLY

        ENTRY

pointer     RN  0                               ; pointer
key         RN  1                               ; search key
length      RN  2                               ; length of the list
counter     RN  3                               ; loop counter
value       RN  4                               ; item value to be compared with
base        RN  5                               ; base address of the list

main
        LDR     pointer, =list                  ; pointer points at starting address of the list
        LDR     base, =list                     ; keep a copy in case of updating length
        LDR     key, =0x9ABCDEF0                ; load search key value 
        LDR     length, [pointer]               ; load list length, keep for updating
        LDR     counter, [pointer], #4          ; initialise counter and increment pointer by 4 bytes
        LDR     value, [pointer]                ; load 1st data item value after pointer has increment by 4 bytes
                                                ; look at the list to understand what this means

loop
        CMP     key, value                      ; compare key and first value
        BEQ     stop                            ; if equal stop loop, end of program, r0 points at matched item
                                                ; if not equal, continue:    
        SUBS    counter, counter, #1            ; decrement counter by 1, update flag

        LDRNE   value, [pointer, #4]!           ; Load if counter not equal to 0, load next data item into value
                                                ; also updates pointer to the address of the next data item
        BNE     loop                            ; branch back loop if counter not equal zero

                                                ; if there is no match, go into new routine to update the list 
add_new
        ADD     length, length, #1              ; update the length by increment of #1
        STR     length, [base]                  ; Store back to base address to update length
        STR     key, [pointer, #4]!             ; Store unmatched key into the end of list
                                                ; also update pointer address to it

stop    B       stop


        AREA    Data1, DATA, READWRITE

list    DCD     4                               ; length, no. of items in the list
        DCD     0x12345678                      ; 1st item
        DCD     0x56789ABC                      ; 2nd item
        DCD     0x9ABCDEF0                      ; 3nd item
        DCD     0xDEF01234                      ; 4th item

store   SPACE   20                              ; reserves 20 bytes of storage, 4 x 5 = 20 bytes

        END
        






