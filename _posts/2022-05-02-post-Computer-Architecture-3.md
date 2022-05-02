---
title: "[Comarch] Ch4. the processor-1"
categories:
  - Computer Architecture
tags:
  - computer architecture
  - lacture
  - computer science
last_modified_at: 2022-05-02T22:00:00 +09:00
---
computer architecture ch4 : MIPS processor<br>
which is simplified version
{: .notice--accent}

{% include toc %}

Introduction
========

- CPU performance factors
    * Instruction count : Determined by ISA and compiler
    * CPI and Cycle time : determined by CPU hardware
    * IPC(1/CPI) widely use : proportional to performance
- simple subset, shows most aspects
    * memory reference: lw, sw
    * arithmetic/logical: add, sub, and, or, slt
    * control transfer: beq, j

CPU
======

## instruction execution
- Fetch
    * PC -> instruction memory, fetch instruction to IR
    * PC : program counter, pointer for next instr.
    * IR : instruction register
- Decode (decode and register read)
    * register numbers->register file, read registers
    * understanding instruction and generate control signal
- Execute : use ALU to calculate, perform real job
    * arithmetic result (normal instruction)
    * memory address for load/store
    * branch target address->need 2 ALUs to comparison(sub) and generate target addr
- Memory : access data memory for load/store(memory operations)
- write-back : target address or PC + 4 -> PC
    * register write + PC update
    * PC + 4 : next instruction in code

## CPU overview
- PC
- 2 adders for calculate next instruction
- registers set
- ALU
- 3 multiplexers for two different input
    * select bit sets by control path
- control path
    * generate by instruction

## logic design basics
- CPU is digital logic circuit
- information encoded in binary
    * low = 0, high = 1
    * one wire per bit
    * multi-bit data encoded on multi-wire buses
- combinational element
    * operate on data
    * output is a function of input
    * AND/OR/NOT
    * logic gates for data manipulation
- state(sequential) element
    * store information
    * data is changed in special condition(clock signal)
    * Flip-Flop(reg)
    * save data

## building a datapath
- datapath
    * elements that process data and addresses in the CPU
    * registers, ALUs, mux's, memories, ...

### instruction fetch
- PC : 32-bit register
- 1 adder : increment by 4 for next instruction

### R-format instructions
- read two register operands
    * 5bits(number) to 32bits(data)
    * decode
- perform arithmetic/logical operation
    * in ALU
    * execute
- write register result
    * write back

### Load/Store Instructions
- read register operands
- calculate address using 16-bit offset(Imm field)
    * use ALU, but sign-extend offset
- Load : read memory and update register
- Store : write register value to memory

### branch instructions
- read register operands
- compare operands
    * use main ALU, subtract and check Zero output
    * not use result, just generate 0 or 1
- calculate target address
    * use small adder(ALU)
    * sign-extend displacement : sign-bit wire replicated
    * shift left 2 places(word displacement) : just re-routes wires
    * add to PC + 4

### composing the elements
- first-cut datapath does an instructions in one clock cycle
    * each datapath element can only do one function at a time
    * Hence, we need separate instruction and data memories
- use multiplexers where alternate data sources are used for different instructions : avoid Data conflict

## ALU control
- ALU used for
    * L/S : F = add for generate address
    * branch : F = subtract for comparison
    * R-type : F depends on funct field(6bits) in instr.
- ALU control bit : 4bits (num of functions smaller than 16)

## Main Control Unit
- control signals derived from instruction
- use opcode
- register, memory R/W
- ALU op
- branch
- generate all control signal

## impleneting jumps
- jump uses word address
- update PC with **concatenation** of
    * top 4 bits of old PC
    * 26-bit jump address
    * 00
- not use add operation
- need an extra control signal decoded from opcode
- no mux, no adder

## Performance Issues
- Longest delay determines clock period
    * critical path : load instruction(slowest)
    * instruction mem -> register file -> ALU -> data mem -> register file
    * fetch, decode, execute, memory, write-back
    * go through all the process
- in Store instr. : no write-back process
- ALUop : fetch, decode, execute, write-back
- slowest instr. needs to be faster to shorten the clock period
- not feasible to vary period for different instructions
- violates design principle
    * making the common case fast
- improved by pipelining
- single cycle processor -> multi-cycle non-pipelined processor
    * do instr. exe process in each cycle
    * can skip not used stage
