---
title: "[Comarch] Ch1. computer abstractions and tech"
categories:
  - Computer Architecture
tags:
  - computer architecture
  - lacture
  - computer science
last_modified_at: 2022-05-01T22:00:00 +09:00
---
computer architecture ch1 : computer, performance
{: .notice--accent}

{% include toc %}

Computer classes and component
=========

## computer revolution

- progeress in computer technology
    * underpinned by Moore's law : the density of transistors doubles every 2 years
    * but now, slowed down to 3 years
- makes novel applications feasible
- computers are pervasive

## classes of computers
Q : If you don't trust CPU : operate redundant operations in software, choose majority : too slow but high reliability 

### personal computers

- general purpose, variety of software 
    * good to any software, not to specific
- Subject to cost/performance trade-off
- CPU + GPU (e.g. i7) : require higher computation

### server computers
- network based
- high capacity, performance, **data reliability**
- range from small servers to building sized
- CPU + cache (e.g. xeon) : require higher memory
- security : HW-level security

### supercomputers
- high-end scientific and engineering calculations
- highest capability but represent a small fraction of the overall computer market

### embedded computers
- IoT, Edge device : network connection
    * lot of edge device : Edge cloud, fog computation
- hidden as components of systems
- stringent power, performance, cost constraints
    * high energe efficiency, lower cost : just meet the requirements
    
### postPC era
- Personal Mobile Device (PMD)
    * battery operated
    * connects to the Internet
    * hundreds of dollars
    * smart phones, tablets, etc
- Cloud computing
    * Warehouse Scale Computers (WSC)
    * Software as a Service (SaaS)
    * **Portion of software run on a PMD and a portion run in the Cloud**
    * Amazon and Google
    * SNS, machine learning

## components of computer

The larger the device size, the larger the battery and the better the cooling system

### touchscreen
- postPC device
- supersedes keyboard and mouse
- Resistive type : not support multi-touch
- capacitive type : allow multi-touch

### cpu
- datapath: performs operations on data : ALU
- control : sequences datapath, memory, etc : to efficient use of datapath
- cache memory : small fast SRAM for immediate access to data
- lowend ARM : simple control path
- highend X86 : complex control path : high portion of power consumed
- similar on datapath, but different in absolute performance, power efficiency

### memory
- volatile main memory(DRAM) : byte addressable
- Non-volatile secondary memory : large capacity
    * magnetic disk, ssd, ...
    * page
- new-memory (PRAM, RRAM) : non-volatile, byte addressable
    * require to change OS : performance, non-volatile
    
### network
- communication, resource sharing, nonlocal access
- Local Area Network : Ethernet
- Wide Area Network : the Internet
- Wireless network : WiFi, Bluetooth

FAST Data transfer protocol between GPUs from NVIDIA : nvlink<br>
faster than PCIE, implementing GPU base Server with NvSwitch
{: .notice--warning}

CPU vs GPU vs NPU<br>
CPU : not good branch instruction<br>
GPU : small CPUs : deep learning, SNS analysis(Big data) : #threads<br>
NPU : GPU + small APUs : 1:1 connect 2D array<br>
AP : CPU + GPU + NPU : heterogeneous system
{: .notice--accent}

Performance
========

## abstractions
- abstractin helps us deal with complexity
    * hide lower-level detail
- Instruction set architecture (ISA)
    * the hw/sw interface
    * implementing hw to support ISA
    * implementing sw based on ISA
- Application binary interface
    * the ISA plus system software interface (compiler, OS)
- Implementation
    * details underlying and interface

## understanding performance
- Algorithm
    * most important
    * independent on HW/SW
    * determines number of operations executed
- Proglamming language, compiler, architecture
    * determine number of machine instructions executed per operation
- processor and memory system
    * determine how fast instructions are executed
- I/O system (including OS)
    * determines how fast I/O operations are executed
    * network, storage
    * improve performance dramatically

## Eight Great Ideas
- design for Moore's law
- use abstraction to simplfy design
- make the common case fast
- performance via parallelism (processor)
- performance via pipelining (processor)
- performance via prediction (processor)
- hierarchy of memories
- dependability via redundancy

## below program
- application software : written in high-level-language(HLL)
- system software
    * compiler : translates HLL code to machine code(assembly)
    * Operating System : service code\
        - handling input/output
        - managing memory(virtual mem) and storage(file system)
        - scheduling tasks and sharing resources(process)
- hardware
    * processor, memory, I/O devices

### levels of program code
- HLL
    * level of abstraction closer to problem domain
    * provides for productivity and portability(retargetability)
    * can run any hardware
- Assembly language
    * textual representation of instructions
    * machine dependent
- hardware representation
    * binary digits(bits)
    * encoded instructions and data
    
#### **When use assembly language : hard coding**
* HLL features are not lost when compiling <br>
* cool and fast hw to utilize directly the hw feature<br>
* e.g. rotating shift : ass(rs 1), c(`b=a>>4; a<<1; a|=b;`)<br>
    - rs 1 : `1`1011 -> 1110`1`
* good compiler for good portability; high understand in HW is required
{: .notice--warning}

### manufacturing IC : tested dies process
- wafer -> dicer -> dies
- increase yield : using redundancy -> cons : increase size
- make 4ghz cpu : high price, failure at 4ghz->medium price, other->low price
- DRAM : redundant cells -> replace failure cell
- in software : MUL : fail -> multiple ADD operation

## define performance
- What is important depends on the purpose
    * server => absolute perf : x86, expensive cooling sys->server farm in lake, beach
    * embedded => power efficiency : ARM

### response time and throughput
- response time : how long it takes to do a task
    * latency of a single program
    * good in CPU : complex control unit to accelate one program
    * depends on clock
- throughput : total work done per unit time
    * good in GPU : multiple low-end cores
    * depends on cores
- good latency != good throughput

### relative performance
- Performance = 1/Execution Time
- "X is n time faster than Y"
    * PerfX/PerfY = ExeTY/ExeTX = n

### Measuring Execution Time
- Elapsed time
    * total response time, including all aspects(processing,I/O,idle time...)
    * determines system performance
    * easy to measure
- CPU time
    * time spent processing a given job(discounts I/O, ...)
    * comprises user CPU time and system CPU time
    * different programs are affected differently by CPU and system performance
    * measure by HW counter inside CPU(Intel Vtune, Nvidia Visual Profiler)
    * HW counter register : counts Clock
    * more widely use for CPU performance

Clock period vs Clock frequency(rate)<br>
Clock period : duration of a clock cycle : 250ps = 250 x 10^12s<br>
Clock rate : cycles per second : 4.0GHz = 4.0 x 10^9Hz<br>
**inverse relationship**
{: .notice--warning}

## calculate execute time

- CPU Time : CPU Clock Cycles / Clock rate
    * improved by
    * reducing clock cycles
    * increasing clock rate
    * faster clock, but causes increasing number of clock cycle
    * because cycles per instruction(CPI) can change
- Instruction Count and CPI(IPC common used)
    * Clock cycles : IC x CPI(assume every instr. takes same num of cycles)
    * CPU Time : IC x CPI / Clock Rate
    * IC is determined by program(Algo.), ISA(CPU) and compiler(remove redundant code)
    * Avg. CPI : determined by CPU hardware, **different instr. have diff CPI**
    * affected by instr. mix
    * generally, ADD,SUB-> 1 cycle, MUL-> 3cycles, DIV-> 10cycles
- CPI in real : $$ \sum\limits_(i=1)^nCPI_i \times IC_i $$
- compare Perf : check total clock cycles, not Avg.CPI

### performance depedence
- Algorithm : affect IC, possibly CPI
- Programming Language : affects IC,CPI
- Compiler : affects IC,CPI
- Instruction Set Architecture : affects IC,CPI,$$T_c$$

## power
- important category in performance metrics
- POWER = Capacitive load(area, size of chip) x Voltage^2 x Frequency(clock)
- increasing F induces increasing C
- if we cannot reduce voltage and remove more head : increase num of cores

## multiprocessors
- more than one processor per chip
- theoretically, 2 core -> 2x perf, but practically, smaller than that
- requires explicitly parallel programming
    * compare with instruction level parallelism : execute multiple instr. at once
    * Hard to do
    * programming for perf. (single thread program -> multi-thread by compiler(automatic parallelism), programmer)
    * Load balancing(using multi-thread, one thread waiting other threads : each thread must have similar exe time)
    * Optimizing communication and synchronization
    * using multicore : each threads must have no interiteration dependency

## Programs for measure performance
- SPEC CPU Benchmark(standard programs set)
- ML perf. (in ML)

## Amdahl's Law
- Improving an aspect of a computer and expecting a proportional improvemnet in overall performance
- T = affected/improvement factor + unaffected
- make the common case fast : loop(the code region where is reused multiple times)

## low power at Idle
- dynamic power consumption(at execution) : decrease
- static power consumption(at Idle) : increase
- optimizing static power!

## MIPS as a Performance metric
- Millions of Instructions Per Second
- doesnot account for
    * differences in ISAs between computers
    * Differences in complexity between instructions
- not a good measure in diff architecture
- but same ISA, difference latency, so it still bad measure
- total execute time is important!

Conclusion
====
- cost/performance is improving due to underlying technology development
- hierarchical layers of abstraction in both hardware and software
- instruction set architecture : the hw/sw interface in target CPU
- execution time : the best performance measure
- power is a limiting factor : use parallelism to improve performance


    
