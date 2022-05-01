---
title: "[Comarch] Ch2. Instructions: Language of the computer"
categories:
  - Computer Architecture
tags:
  - computer architecture
  - lacture
  - computer science
last_modified_at: 2022-05-01T22:00:00 +09:00
---
computer architecture ch2 : MIPS instructions<br>
which are operations executed by CPU
{: .notice--accent}

{% include toc %}

Basic
=====

## instruction Set
- the set of instructions of a computer
- different computers have different instruction set
    * but with many aspects in common
- early computers have very simple instruction set
    * simplified implementation (8bits)
- Many modern computers also have simple instruction sets
    * 32 or 64 bits
    * power efficiency
    
### 2015 cpu vs 2021 cpu
- number of instructions is different : more in newest, add new hw feature logics
- binary in 2015 : runnable in 2021
- binary in 2021 : maybe runnable 2015(if not use new feature)
- compile in 2015 vs compile in 2021 : running in 2021, compile in 2021 maybe fast

### application vs OS
- app : same instruction set-> can use same binary
- os  : underlying hw is different, if same in all hw(dram, storage ...), use same binary

## RISC vs CISC
- RISC : Reduced Instruction Set Computer
    * power efficient, but maybe slow
    * ARM, MIPS, RISC-V
    * simple ALU, small size instructions
    * used in small HW -> strong compiler support
    * MUL + ADD : 3 cycles
- CISC : Complex Instruction Set Computer
    * fast, but low power efficient
    * x86 : intel, amd : but inside INTEL cpu, use RISC HW : power eff. (CISC instr -> instruction buffer -> RISC HW(simple ALU) use small instr.(micro code))
    * many complex instr.
    * Large HW
    * MULADD : 2cycles
    
## stored program computers
- instructions represented in binary, just like data
- instructions and data stored in memory
- programs can operate on programs(e.g. compilers, linkers ...)
- binary compatibility allows compiled programs to work on different computers
    * standardized ISAs
    * binary in x86 -> execute in other x86
- can create ARM binary on x86 system : cross-compile
    * for reduce compile time (x86 has high performance)
    * PC has more memory and fast cpu
    * Edge device has small memory; so cannot compile big program
    
## Design principles
1. Somplicity favors regularity
    - Regularity makes implementation simpler(simple HW)
    - Simplicity enables higher performance at lower cost(energy efficiency)
2. Smaller is faster
    - register vs main memory
    - main mem has millions of locations
3. Make the common case fast
    - Small constants are common
    - Immediate operand avoids a load instruction
    - RISC
4. Good design demands good compromises
    * different formats complicate decoding, but allow 32-bit instructions uniformly : complex HW but...
    * Keep formats as similar as possible : make HW simple

MIPS instruction set
========
- similar to ARM
- using in embedded(e.g. router)

## representing instructions

- instructions are encoded binary : machine code
- MIPS instructions encoded as 32-bit instruction words
- small number of formats encoding
    * operation code(opcode)
    * register numbers(5bits)
    * **regularity!**
    
### MIPS R-format instructions

op rs rt rd shamt funct

- op(6bits) : operation code (opcode)
- rs(5bits) : first source register number
- rt(5bits) : second source register number
- rd(5bits) : destination register number
- shamt(5bits) : shift amount
    * max shift amount : 31
    * because not use more than 32 : MIPS use 32 bit
- funct(6bits) : function code (extends opcode) used in ALU

### MIPS I-format instructions

op rs rt (constant or address)

- Immediate arithmetic(addi ...) and load/store instructions
- op(6bits)
- rs(5bits) : source register number
- rt(5bits) : destination(output) or source(input) register number
- constant : -2^15 to 2^15-1
- address : offset added to base address in `rs`(L/S)

### register usage

- $a0 - $a3 : arguments (reg# : 4-7)
- $v0, $v1 : result values (2,3)
- temporaries : can be overwritten by callee
    * $t0 - $t7 : 8-15
    * $t8 - $t9 : 24-25
- saved : must be saved/restored by callee
    * $s0 - $s7 : 16-23
- $gp : global pointer for static data (28)
- $sp : stack pointer (29)
- $fp : frame pointer (30)
- $ra : return address (31)

## Arithmetic Operations

- add and subtract, R-format
- `add a, b, c` -> a = b + c
- All arithmetic operations have this form
- Design Principle 1

## register operands
- Arithmetic instructions use register operands
- MIPS has a 32 x 32-bit register file
- ARM has a 16 x 32-bit register file
    * use for frequently accessed data
    * Numbered 0 to 31(0 to 15 on the ARM)
    * 32-bit data called a **word**
- Assembler names
    * t0-t9 for temporary values
    * s0-s7 for saved variables
- Design Principle 2

### *In function call : register save problem*
- HW : small num of reg.<br>
- SW : increase num of regs to keep data for a long time<br>
    * If save all reg, then slow!<br>
- C-lang(SW) level solution : inlining function<br>
- ISA(HW) level solution : only save saved reg.<br>
- MIPS : s-reg is important, and considered by compiler
{: .notice--warning}

## memory operands

- Main memory used for composite data (e.g. Arrays, structures, dynamic data)
- To apply arithmetic operations
    * Load values from memory into registers
    * Store result from register to memory
- Memory is byte addressed (vs word(4bytes))
- Words are aligned in memory
- MIPS is Big Endian
- ARM is Big Endian, but recent ARM can run in either little or big
    * most-significant byte at least address of a word
    * Intel use Little Endian

**0xabcdef12**<br>
address : 0 1 2 3<br>
Big Endian : ab cd ef 12<br>
Little Endian : 12 ef cd ab<br>
{: .notice--warning}

## immediate operands

- constant data specified in an instruction (16 bits)
- `addi $s3, $s3, 4`
- No subtract immediate instruction : just use a negative constant
- Design Principle 3
- large constant : use addi multiple times (high, low)

## register vs memory

- Register : small, fast
    * store variable
- Memory : slow, large
    * require load/store operation
    * array, structure
- immediate : constant values, embedded in instructions
    * cannot be 32bit data
- compiler must use registers for variables as much as possible
    * only spill to memory for less frequently used variables
    * register optimization is important!
    
## binary integers

- unsigned binary integers
    * range : 0 to 2^32-1
    * 32bits : 0 to 4294967295
- 2s-complement signed integers
    * range : -2^(n-1) to 2^(n-1) - 1
    * 32 bits : -2147483648 to 2147483647
    * bit 31(MSB) is sign bit : 1->negative
    * make negative : invert all the bits and add 1

### sign Extension
- representing a number using more bits
- in MIPS instruction set
    * addi : **extend immediate value**
    * lb, lh : extend loaded byte/halfword
    * beq, bne : extend the displacement
- replicate the sign bit to the left
    * 8bit : 1111 1110
    * 16bit : 1111 1111 1111 1110
    
## Logical Operations
- shift left : `<<` in C, `sll` in MIPS
- shift right : `>>` in C, `srl` in MIPS
- AND : `&` in C, `and` in MIPS
- OR : `|` in C, `or` in MIPS
- NOT : `~` in C, `nor` with $0 in MIPS

### Shift Operations
- R-format
- use shamt field : how many positions to shift
- shift left logical : fill with 0
    * sll i : multiplies by 2^i
- shift right logical : fill with 0
    * srl i : divides by 2^i(unsigned only)
- arithmetic : shift right-fill with sign bit

### AND Operation
- R-format
- useful to mask bits in a word
    * select some bits, clear others to 0
- `and t0, t1, t2`

### OR Operation
- R-format
- useful to include bits in a word
    * set some bits to 1, leave others unchanged
- `or t0, t1, t2`

### NOT operation
- R-format
- useful to invert bits in a word
    * change 0 to 1, 1 to 0 : compliment
- pesudo instr. : use `nor`
    * no HW for `NOT`
    * call not op as pseudo op
- `nor t0, t1, zero`
- because design principle to make system simple : regularity
 
## Conditional Operations
- for loop, if-else
- branch to labeled instruction if a condition is true
    * otherwise, continue sequentially
- `beq rs, rt, L1`
    * if (rs == rt) branch to instruction labeled L1
- `bne rs, rt, L1`
    * if (rs != rt) branch to instruction labeled L1
- `j L1`
    * unconditional jump to instruction labeled L1
- in C code, use `==` -> in MIPS code, use `bne`
    * to keep original code order

### forward branch vs backward branch
- forward branch : skip instruction
    * if-then-else
- backward branch : reuse(reexecute) instruction
    * loop(for, while)

### basic blocks
- a sequence of instructions with
    * no embedded branches(except at end)
    * no branch targets (except at beginning)
- compiler identifies basic blocks for optimization
- advanced processor can accelerate execution of basic blocks

### more conditional operations
- set result to 1 if a condition is true
    * otherwise, set 0
- slt : set on less than(set means 1, reset means 0)
- `slt rd, rs, rt`
    * if (`rs < rt`) rd = 1; else rd = 0;
- `slti rd, rs, constant`
    * if (`rs < cons`) rt = 1; else rt = 0;
- use in combination with `beq`,`bne`
    
## Procedure Calling
- function(high level) == procedure(low level)
- calling convention, subroutine handling
- return addr is automatically knew : PC register
    * PC : next address of the function call instr.
- target addr : location of first instr. of callee
- time difference knowing and using `ra` is large

### steps
1. place parameters in registers(CPU)
    * when do 2. callee cannot access caller
2. transfer control to procedure(callee)
3. acquire storage for procedure
    * both register and memory
    * allocate some space to variables of function(local variable)
4. perform procedure's operations
5. place result in register for caller
    * because caller cannot access callee 
6. return to place of call

who and why store caller's reg. values? -> depends on compiler because prevent conflict
{: .notice--accent}

- why use functions? -> reuse code : optimize only this code<br>
- sometimes return value stored in stack<br>
- function call cannot be replaced to jump : must store return addr
{: .notice--warning}

### procedure call instruction
- jump and link : to save return addr
- `jal procedureLabel`
    * address of following instruction put in $ra
    * jumps to target address
    * why using $ra : stack(memory) is too slow
- procedure return : jump register
- `jr $ra`
    * copies $ra to program counter
    * can also be used for computed jumps(e.g. case/switch)

### leaf procedure
- save reg. to use on stack(PUSH)
    * main memory
    * decreasing sp
- procedure body
- save result
- restore reg. to used on stack(POP)
- return

### non-leaf procedure
- procedures that call other procedures
- include recursive call
- for nested call, caller needs to save on the stack
    * its return address(`$ra`)
    * any arguments(`$An`) and temporaries needed after the call(`$Sn`)
- restore from the stack after the call

### Local Data on the Stack
- local data allocated by callee
- procedure frame (activation record)
    * used by some compilers to manage stack storage
- Why use `$fp` : for stable
    * fp : base addr of frame
    
### memory layout
- text : program code
- static data : global variables
- dynamic data : heap (e.g. malloc in C, new in java)
- stack : automatic storage
- reserved : meta data
- each program has this

stack overflow : access over the stack<br>
segmentation fault : access disallowed area<br>
change test field by program : not allowed ingeneral<br>
used for code optimizing : self modifying code, allowed when we know the process<br>
unicode : 32-bit character set -> char is byte in general
{: .notice--warning}

### byte/halfword operations
- could use bitwise operations
- MIPS byte/halfword L/S
    * string processing is a a common case
- `lb rt, offset(rs)`, `lh rt, offset(rs)`
    * load byte/halfword
    * sign extend to 32 bits in rt
- `lbu rt, offset(rs)`, `lhu rt, offset(rs)`
    * load byte/halfword unsigned
    * zero extend to 32 bits in rt
- `sb rt, offset(rs)`, `sh rt, offset(rs)`
    * store just rightmost byte/halfword
- How to generate lb from C code? : depends on datatype
    * int:lw, char:lb, short:lh
    * mem addr. : lw:0,4,8,12 ...; lb:0,1,2,3 ...
    
### 32-bit constants
- most constants are small
    * 16-bit immediate is sufficient
- for the occasional 32-bit constant
- `lui rt, constant`
    * copies 16-bit cons to left 16 bits of rt
    * clears right 16bits of rt to 0
- next, use `ori rt, rt, constant`

## addressing mode
- immediate addressing : immediate op
- register addressing : add,sub, ...
- base addressing : load/store
    * reg + addr
- PC-relative addressing : branch
    * PC + addr
- Pseudodirect addressing : jump
    * PC : addr
    
### branch addressing
- impossible to use target addr directly
- Branch instructions specify
    * Opcode, two registers, target addr(16bits)
- Most branch targets are near branch
    * forward or backword
- PC-relative addressing
    * target address = PC + offset x 4
    * PC already incremented by 4 by this time
    * all instructions are 4 bytes
    * so, low 2 bits are alwasy 0
- range : -2^17+4 to 2^17+4
    * 16 - 1(offset) + 2(x4)
    * based on branch instr.

### jump addressing
- Jump(j and jal) targets could be anywhere in text segment
    * encode full address in instruction
    * op(6bits) addr(26bits)
- (Pseudo)Direct jump addressing
    * target address = PC(31-28):(addr x 4)
    * high 4 bit of PC is used

### branching far away
- if branch target is too far to encode with 16-bit offset
- assembler rewrites the code
- use multiple branch

## synchronization
- two processors sharing an area of memory
    * P1 writes, then P2 reads
    * race condition
- Hardware support required
    * **atomic** read/write memory operation : special HW
    * no other access to the location allowed
    * for support locking system
- could be a single instruction
    * e.g. atomic swap of register with memory
    * or an atomic pair of instructions
    
## assembler pseudoinstructions
- instruction but not implemented in real HW
- e.g. not -> nor, move -> add, blt -> slt,bne
- most assembler instructions
    * represent machine instructions one-to-one
- Pseudoinstructions : figments of the assembler's imagination
- blt->slt,bne : use `$at` (reg 1) : assembler temporary
- used for HW efficiency and instr format efficiency
- So, assembly code ->(ass) binary ->(disass) assembly code
    * not exactly same due to pseudoinstructions
    * there aer so many `add` : HW restriction; mov -> add


Conclusion
=======
- design principles
- layers of software/hardware
- MIPS : typical of RISC ISAs
- complex instructions are hard to implement
    * compilers are good at making fast code from simple instr.
    * compilers are good at optimizing : gcc -On : doubles Perf
- backward compatibility : instr set doesnot change
    * just do accrete more instructions


