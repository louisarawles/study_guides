## Week 14 Out-of-Order Processor Quiz

**Question 1**

Instruction sequence:

1. movq (%rax), %r8
2. addq %r8, %r9
3. subq %r10, %r11
4. xorq %r11, %r9
5. orq  %r8, %r13
6. andq %r8, %r9

On an out-of-order processor, depending on what previous instructions do, the instruction subq %r10, %r11 may execute the same time as ___. Select all that apply.

1. movq (%rax), %r8
{{ ✅ yes — subq does not depend on %r8, so it can run while the load runs }}
2. addq %r8, %r9
{{ ✅ yes — depending on previous instructions, subq may be ready at same time as addq }}
3. xorq %r11, %r9
{{ ❌ no — xorq needs the value produced by subq, so it cannot execute at the same time }}
4. orq %r8, %r13
{{ ✅ yes — orq depends on %r8, not %r11, so it can overlap with subq }}
5. andq %r8, %r9
{{ ❌ generally no — andq depends on %r9, which is updated by earlier instructions including xorq, which depends on subq }}

Answer:
{{ movq (%rax), %r8, addq %r8, %r9, and orq %r8, %r13 }}

**Question 2**

Suppose an out-of-order processor has:

a memory access unit (which interfaces with the data cache) that is pipelined and takes 3 cycles to load a value;
two pipelined ALUs (which can handle all arithmetic/bitwise instructions), each of which can produces its results 2 cycles later.
Assume the processor implements forwarding so it can use a value that is produced in a cycle for a computation that starts in the following cycle.

What is the minimum number of cycles the processor needs to execute (perform the arithmetic and load/store operations) the above instruction sequence? (Do not include time for fetching instructions, renaming instructions, committing, etc.)

Given:

* memory access unit takes {{ 3 cycles }}
* two ALUs
* ALU results take {{ 2 cycles }}
* forwarding lets a result produced in one cycle be used starting the next cycle

Schedule:
{{ cycle 1: movq #1, subq #1 --> cycle 2: movq #2, subq #2 --> cycle 3: movq #3 --> cycle 4: addq #1, orq #1 --> cycle 5: addq #2, orq #2 --> cycle 6: xorq #1 --> cycle 7: xorq #2 --> cycle 8: andq #1 --> cycle 9: andq #2 }}

Answer: {{ 9 cycles }}

Reasoning:
{{ movq and subq can start immediately because they are independent. After %r8 is loaded, addq and orq can run. xorq must wait for both %r11 from subq and %r9 from addq. andq must wait for the final %r9 from xorq. }}

**Question 3**

Instruction sequence:

addq %r8, %r9

addq %r9, %r10

<missing>

addq %r9, %r8

addq %r11, %r9

Assume the processor does register renaming as described in lecture.

Which of these instructions, if it took the place of the missing instruction, would result in fewer free registers being used to complete the renaming process for these instructions compared to the alternatives?

addq %r8, %r10
{{ ❌ writes one destination register, so needs one new physical register }}
addq %r10, %r12
{{ ❌ also writes one destination register, so needs one new physical register }}
addq %r9, %r10
{{ ❌ also writes one destination register }}
addq %r8, %r9
{{ ❌ also writes one destination register }}
addq %r10, %r9
{{ ❌ also writes one destination register }}
None, they are all equivalent
{{ ✅ every option is an addq, and every addq writes exactly one destination register }}

Answer: {{ None, they are all equivalent }}

Reasoning:
{{ With register renaming, each instruction that writes a register gets a new physical register. Since every possible missing instruction writes exactly one architectural register, they all require the same number of renamed/free registers. }}

**Question 4**

An out-of-order processor includes 4 single-cycle ALUs that can handle all arithmetic/bitwise instructions and 3 pipelined memory access units that require three cycles to complete a load or store. The processor has seven stages (fetch, decode, rename, issue, execute, writeback, and commit) and is 4-wide, meaning the fetch, decode, rename, issue, writeback, and commit stages can handle four instructions simultaneously. The processor's clock speed is 1 GHz (so each clock cycle is 1 ns).

The processor is executing a machine learning workload (with many multiply–accumulate operations) and runs a large number of consecutive add and multiply instructions where every instruction uses the output of the previous instruction. What will be the average performance of this processor? Assume cache misses and branch mispredictions are negligible. Specify your answer in a number of instructions committed per cycle.


Processor:

4 single-cycle ALUs
3 pipelined memory units
4-wide fetch/decode/rename/issue/writeback/commit
workload has many consecutive add/multiply instructions
every instruction uses the output of the previous instruction

Question: average performance in instructions committed per cycle?

Answer: {{ 1 instruction per cycle }}

Reasoning:
{{ Even though the processor is 4-wide and has multiple ALUs, the instructions form one long dependency chain. Each instruction must wait for the previous instruction’s result, so the processor cannot exploit instruction-level parallelism. Therefore, performance is limited by the dependency chain, not by the width of the processor. }}