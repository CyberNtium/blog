+++
date = '2025-10-08T09:42:27+08:00'
title = 'Microprocessors STM, LDM & Stack'
+++

# STM/LDM Instructions
- They just mean Store and Load Multiple Instructions
- Quite similar to STR/LDR
- STM: store content of affected registers before branching to a subroutine or handling exception
- LDM: Stored values to be restored to the respective registers upon returning from a subroutine 

- Good stuffs: A single STM/LDM instruction can load / store up to 15 registers instead of using 15 separate instructions
- Shorter execution time

**_For both LDM and STM, the lowest register will always be loaded from the lowest address, regardless of the stack operations_**

---

## STM Syntax

`STM{<cond>}<address-mode> <Rn>{!},<reg-list>{^}`
- `{<cond>}` is an optional condition
- `<address-mode>` specifies the addressing mode
- `<Rn>` is the base register
- `<reg-list>` is a comma delimited list of registers

All of these sounds like jargons, let's look at some examples:

### Example 1: `STM r9, {r4, r1-r3}`
- Given that r9 holds the base address of `0x40000100`
- This lines stores the content of register r1 to r4 into the memory address pointed by r9
- This single line is equivalent to 4 different instructions of:
- `STR r1, [r9, #0]`, address at 0x40000100
- `STR r2, [r9, #4]`, address at = 0x40000104
- `STR r3, [r9, #8]`, address at = 0x40000108
- `STR r4, [r9, #12]`, address at 0x4000010C

- r9 will remain unchanged after execution of the instruction


### Demo Code for STM
```ASM
ramBase EQU 0x40000000
        AREA STMExample, CODE, READONLY
        ENTRY

        LDR     r9, =ram_base + 0x100      ; r9 = 0x40000100
        MOV     r1, #1
        MOV     r2, #2
        MOV     r3, #3
        MOV     r4, #4
        STM     r9, {r4, r1-r3}

stop    B       stop

        END
```
{{< figure src="https://i.imgur.com/ZZ9kgP2.png" alt="STMexample" class="center" >}}

- As you can see from the memory column, the content of low register will transfer to low memory address. 

---


## LDM Syntax

`LDM{<cond>}<address-mode> <Rn>{!},<reg-list>{^}`
- `{<cond>}` is an optional condition
- `<address-mode>` specifies the addressing mode
- `<Rn>` is the base register
- `<reg-list>` is a comma delimited list of registers

Same shit as before, confusing as hell, let's look at some examples:

### Example 1: LDM r9, {r4, r1-r3}
- Load whatever is r9 address memory into register 1 to 4

- Same as writing 4 instructions:
- `LDR r1, [r9, #0]`, address at 0x40000100
- `LDR r2, [r9, #4]`, address at 0x40000104
- `LDR r3, [r9, #8]`, address at 0x40000108
- `LDR r4, [r9, #12]`, address at 0x4000010C



### STM Demo Code
```ASM
ramBase EQU 0x40000000
	
		AREA LDMExample, CODE, READONLY
			
		ENTRY
		
		LDR		r9, =ramBase + 0x100
		LDM		r9, {r4, r1-r3}
		
stop	B		stop
		
		AREA ramArea, DATA, READWRITE
		SPACE 0x100		; leave a space of 0x100 bytes
stack
		DCD 1,2,3,4
		
		END
```

Do note that you need an initialization file that has the values inside the memory first: 
```text
MAP 0x40000000, 0x40002000 EXEC READ WRITE
E LONG 0x40000100 = 1, 2, 3, 4
```
- Right click on Target 1
- Select "Options for target 'Target 1'"
- Switch over to the "Debug" tab
- Under Initialization File, click on the three dots and look for the directory where you saved the initialization file

---

### Differences

| Code          | `STMExample`                        | `LDMExample`                         |
| ------------- | ----------------------------------- | ------------------------------------ |
| Direction     | Registers → Memory                  | Memory → Registers                   |
| Instruction   | `STM` (Store Multiple)              | `LDM` (Load Multiple)                |
| Effect        | Saves register contents into memory | Loads memory contents into registers |

---


# Addressing Modes for LDM & STM 
Recall the syntax:

| Operation |                   General Syntax                             |
| --------- | ------------------------------------------------------------ |
|   LDM     |       `LDM{<cond>}<address-mode> <Rn>{!},<reg-list>{^}`      |
|   STM     |       `STM{<cond>}<address-mode> <Rn>{!},<reg-list>{^}`      |

So far we have discussed through the base register, `Rn` and the register list `reg-list`.  
Now let's continue with the addressing modes:

| Addressing Mode | Description        | Stack Growth Direction | 
|-----------------|--------------------|------------------------|
| **IA**          | Increment After    | Grows **upward**       | 
| **IB**          | Increment Before   | Grows **upward**       | 
| **DA**          | Decrement After    | Grows **downward**     | 
| **DB**          | Decrement Before   | Grows **downward**     | 

---

## STMDB Example

### Example: STMDB r9, {r4, r1, r2-r3}
`STMDB` stands for `STM Decrement Before`, meaning that r9 is decremented first, before storing each register content into the memory.  
Stores content of register r1-r4 into memory address pointed by r9  
Given that r9 holds the address `0x40000100`  
The difference is that, 

Same thing as 4 separated STR operations:
- `STR r1, [r9, #-16]`
- `STR r2, [r9, #-12]`
- `STR r3, [r9, #-8]`
- `STR r4, [r9, #-4]`

### Demo Code
```ASM
ramBase EQU 0x40000000
        AREA STMDBExample, CODE, READONLY
        ENTRY

        LDR     r9, =ramBase + 0x100
        MOV     r1, #1
        MOV     r2, #2
        MOV     r3, #3
        MOV     r4, #4

        STMDB   r9, {r4, r1, r2-r3}

stop    B       stop

        END
```

| Registers       | Value       | Memory Address | Value      |
|-----------------|-------------|----------------|-------     |
| r9              | 0x40000100  | 0x40000100     |         |
| r4              | 4           | 0x400000FC     | 4       |
| r3              | 3           | 0x400000F8     | 3       |
| r2              | 2           | 0x400000F4     | 2       |
| r1              | 1           | 0x400000F0     | 1       |

- As we can see, initial start point is pointed at `0x40000100` by r9.
- However, since its `STM Decrement Before`, we decrement 4 bytes down to `0x400000FC` before storing. And same stuff with the rest of the register.
- Do keep in mind that any STM and LDM operations still follows the convention of lower register occupies lower address. 

---

## LDMDA Example:
### Example: LDMDA r9, {r4, r1, r2, r3}
- `LDMDA` stands for `LDM Decrement After`, meaning it will only decrement after loading the content from memory into register
- Load content from memory pointed by r9 into registers r1 to r4
- Same as doing 4 separated instructions using LDR
- - `LDR r1, [r9, #-12]`
- - `LDR r2, [r9, #-8]`
- - `LDR r3, [r9, #-4]`
- - `LDR r4, [r9, #0]`

### Demo Code
```ASM
ramBase EQU 0x40000000
        AREA LDMDAExample, CODE, READONLY

        LDR     r9, =ramBase + 0x100
        LDMDA   r9, {r4, r1, r2, r3}

stop    B       stop

        AREA ramArea, DATA, READWRITE
        SPACE 0xF4

stack
        DCD 1, 2, 3, 4
        
        END
```

| Registers       | Value       | Memory Address | Value      |
|-----------------|-------------|----------------|-------     |
| r9              | 0x40000100  | 0x40000100     | 4       |
| r4              | 4           | 0x400000FC     | 3       |
| r3              | 3           | 0x400000F8     | 2       |
| r2              | 2           | 0x400000F4     | 1       |
| r1              | 1           | 0x400000F0     |         |


---

# STACK
- Usually used to handle a call to a subroutine
- - Types, _**ascending**_ and **_descending_** stack, **_Full_** or **_Empty_** (which we can refer to the sp, Stack Pointer)
- - Basic Operations, **PUSH** = store, **POP** = load

- Stack is only READWRITE memory, used to store data temporarily
- However, ARM does not have specific PUSH and POP operations, instead we have to use STM and LDM with register r13 as the base register
- r13 is the stack point (sp) which points to the address of the: 
- - next empty word (Empty)
- - last pushed word (Full)

## Type of Stack
|                  |              |
|----------------- |------------- |
| Full Descending  | FD           | 
| Full Ascending   | FA           | 
| Empty Descending | ED           | 
| Empty Ascending  | DA           | 

- Initially, all stack must be empty
- **Full** and **Empty** here do not refer to a full or empty stack, instead, it refers to where the sp is pointing at
- Full --> sp is pointing at the last word pushed into the stack
- Empty --> sp is pointing at the next empty

{{< figure src="https://i.imgur.com/ujpvyxu.jpeg" alt="sp" class="center" >}}

---

### Full Descending (FD) Stack

