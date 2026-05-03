**Pipeline**

# 04-07

## Overview
Reading: processors §1-3
1. TLS outline 
2. review: single-cycle CPU 
3. pipelining idea 
4. pipelining for the CPU 
5. pipelining tradeoffs 
6. hazards (start)

------------------------------------------------------------------------------------------


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
   * Stalling/nop pipeline diagram ... going in more detail in next lect

------------------------------------------------------------------------------------------

# 4-09

## Overview
Reading: processors §2-3
1. hazards and hazard handling
   1. stalling
   2. branch prediction, briefly
   3. forwarding
2. some pipeline exercises 
3. idea of multiple issue + out-of-order

------------------------------------------------------------------------------------------
# hazards and hazard handling

## stalling / nops
![stalling](stalling_1.png)
1. what do you notice about this stalling / nop pipeline diagram? {{ the addq instruction can read from the old %r9 despite the use of nops }}
{{ ![stalling](stalling2.png) }}
2. what's the assumption causing this? {{ if writing register value, the register file will return that value for reads }}

**fix options:**
1. add another nop --> {{ but this adds a full cycle }}
2. hardware solution: {{ hardware adds nops }} which is called {{ stalling }}
   * extra logic:
     * sometimes don't change {{ PC }}
     * sometimes put {{ do-nothing values in pipeline registers }}

### control hazard
* jump depending on result comparison, so you don't know what you need to fetch next
* ideally, fetch something immediately after jump
* to fio what to fetch, get result of compare then use that to find addr etc but that just TOO lATE

### jXX: stalling
* need to get the full result of comparison to complete the jump
* so every jump wastes 2 cycles
* every use of a value wastes some number of cycles while waiting for value to be written back

_how to fill the do nothing slots?_ --> {{ making guesses }}

### branch prediction --> making guesses
* idea: guess: jne won't go to LABEL
* 2 cases: guess right or wrong
* make any performance advantage (2 cycles faster!) if the guess is right, and no cost if the guess is wrong (undo guess before too late)

1. jxx: speculating right
   2. compute jump doesnt go to label
   3. great! done! already did this and if we're right, just verify that
4. jxx: speculating wrong
   1. findout during execute that jne goes to label, but as we're finding that out we've already started 2 instructions we shouldn't be doing
   2. BUT we have only fetched and beginning decoding registers, so all we need to do is prevent it from going to execute
   3. insert a nop to execute, preventing the instruction from executing during that cycle
   4. extra logic to get rid of instruction = {{ squashing }}

_this process of guessing is called branch prediction_

### suppose data hazards
Suppose you have an opportunity where the correct value is available in the ALU you just need to get it to read the correct value
* just need a way to get the correct value
* replacing value we would've otherwise gotten from the register file in cases where data could be corrupt
  * how to make this decision? a MUX
    * choose either to take value from reg fil or output of ALU
    * this path is called forwarding

MUX will end up having multiple options:
* figure out all possible cases where reading from a register that hasn't been written yet

### forwarding
* definition: using output of ALU to replace the value taken directly from the register

### exercise: forwarding paths
![image](ex_forwardingpaths.png)

in subq, %r8 is {{ A }} addq.

in xorq, %r9 is {{ C }} addq.

in andq, %r9 is {{ A --> because while we could forward it, this would be unnecessary because this would still be the wrong value because andq should read from the xorq, so it just should get the register file value relative to addq }} addq.

in andq, %r9 is {{ B }} xorq.

* assume regfile cannot read value while being written
* A: not forwarded from
* B-D: forwarded to decode from {execute,memory,writeback} stage of

### exercise: predict+forward
![](ex_predictforward.png)
* assume regfile cannot read value while being written
* A: not forwarded from
* B-D: forwarded to decode from {execute,memory,writeback} stage of


1. if jle is correctly predicted:
* in andq, %r9 is {{ D }} addq
  * explanation: {{ when you fetch in jle, it takes a second after you fetch dle, so foo is one cycle behind and D occurs during the writeback stage }}
* in andq, %r8 is {{ C }} subq

2. if jle is mispredicted + resolved after jle's execute:
* in andq, %r9 is {{ A }} addq
* in andq, %r8 is {{ A }} subq
* explanation:
  * {{ you fetch, then resolve _after_ jle's execute, so that means fetch during cycle 5, then decode during cycle 6, and at that point just a direct register read }} 

### unsolved problem
* value from mem stage too late for forwarding alone
* _combine_ stalling and forwarding to resolve hazard
  * stall until you can forward
  * ie, hazard detected late, so just repeat F or D to push back execute

### solveable problem
* might not need an execute, so can forward later
* ie, read rbx, but can't get correct value till later, but don't need execute, so you can go through cycle stages as normal because it doesn't affect anything
* key point: don't have to forward after you read, can forward anywhere

if you can forward anywhere, why wouldn't you?
* doesn't actually solve the problem that forward is meant to solve as in getting val from decode stage and putting it into math
* accessing data cache and also doing math on that value, so we would have to adjust cycle time to be able to do both, and if we're gonna do that, why not do those two things in separate stages? and that would erase the advantage of pipelining bc while yea you stall less you increase your cycle time to erase all of the advantage you got from stalling less

### exercise
_suppose 5-stage pipeline_

_using forwarding, stalling like we discussed_

_1 ns cycle time_ -- 1 billion cycles/sec => no hazards: 1 billion instr/sec but thats not realistic

_5% are branches_

_60% correct predictions_

_1% are uses of value just after loading it from memory_

_assume negligible cache misses_

_estimated throughput?_

### hazards v. dependencies
* dependency: X needs result of instruction Y? --> has potential for being messed up by pipeline (since part of X may run before Y finishes)
* hazard: will it not work in some pipeline? --> _before_ extra work is done to "resolve" hazards; multiple kinds: so far, data hazards, control hazards
![](ex_dependencieshazards.png)
1. where are dependencies? 
2. which are hazards in our pipeline?
3. which are resolved with forwarding?
