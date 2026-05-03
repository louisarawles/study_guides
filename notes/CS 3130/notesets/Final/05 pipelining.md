**Pipeline**

# 04-05

## CSO 1 Review Intro
* Simple CPU problem = I$ wastes time doing nothing waiting for PC to update

### Latency -- Time for One
* pipelined latency of one might be greater than normal latency of just completing it sequentially
* pipelined latency makes each operation slower, but we don't care about each individual task

### Throughput -- Rate of Many
* what we care about for a processor
* once program is started, how fast can you get through instructions
* how often will things finish, ie the time between finishes
* time between starts and finishes will be the same, because everything is waiting on the slowest component
  *   * note you will have to store things in between and you will ALWAYS have to wait on the slowest component
* makes each individual task slower, but optimizes getting everything done as fast as possible

### Adding Stages (one way)
* modifying CPU to be a pipeline processor -- stages:
  * I$ + + instr len = fetch
    * fetch next instruction
    * ALSO the stage where you compute the address of the next instruction because you need to know the address in advance to be able to fetch it right away in the next cycle
      * this will cause an issue we will address later: it cannot compute jump / call with a function pointer instructions
      * for common case of just advancing youre fine
  * read = decode
    * instruction sets where machine code is difficult to figure out
    * also register read
  * execute = math
    * add values together, compute mem addr for mov, value for jump instr, etc ...
  * D$ = memory
    * a lot of instructions dont use D$
    * some portion of instrs use D$ so not worth skipping
    * so instrs will always pass through the memory stage to keep timing in sync w instrs that actually use the data cache (ie, read, write, etc)
  * write = writeback

need to put values somewhere in between stages so that we can compute values for instruction 1 while inputting instr 2
* same thing applies as the CPU where second instr won't take affect till we advance everything to the next stage

## exercise 1: throughput / latency
* instructions:
  * 0x100: add %r8, %r9
  * 0x108: mov 0x1234(%r10), %r11
  * 0x110: ...
* pipelining task v. cycle # graph

| Cycle # | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|---------|---|---|---|---|---|---|---|---|---|
|         | F | D | E | M | W |   |   |   |   |
|         |   | F | D | E | M | W |   |   |   |

suppose cycle time is 500 ps. 
1. what is the latency of one instruction? {{ 2500 ps }}
2. throughput overall? {{ 1 instr / 500 ps }}

*Exercise 2: throughput/latency*
* ![alt text](ex_throughput_latency.png){size=medium}
double number of pipeline stages (to 10) + decrease cycle time from 500 ps to 250 ps — throughput?
1. 1 instr/100 ps
2. 1 instr/250 ps
3. 1 instr/1000 ps
4. 1 instr/5000 ps
5. something else

Answer: {{ 1 instr/250 ps }}

What's the problem with this? {{ you aren't actually making anything faster because of register delays, aka diminishing returns }}

*Diminishing returns*

1. Register delays
2. Uneven split
   * ![alt text](uneven_split1.png){size=medium}
   * ![alt text](uneven_split2.png){size=medium}
   * ![alt text](uneven_split3.png){size=medium}
   * limit on how many stages you should have because you can't split up register delays

*Data hazard*

* idea: pipeline reads an older value
  * basically we violated the specification of following reading then writing

1. Read registers 8 and 9
2. Read registers 9 and 8 while adding 8 and 9

So we're reading the old value instead of the value ISA says was just written

_Solutions:_
1. Compiler solution: change the ISA, make it the compiler's job; no ops
   * Overview:
       * Basically saying the manual is wrong
       * All addqs take effect three instructions later (add instruction doesn't write value right away)
       * So compiler takes the value from our perspective
       * Basically using dummy instructions
       * Practical solution
   * nop: go through the pipeline like any instruction

2. Hardware solution: hardware adds nops, _stalling_; adds extra logic
   * Overview:
     * Hardware inserts 2 nops (no operations, which means its slow)
     * Works, but it's slow
   * Stalling/nop pipeline diagram
       * 
       * 

