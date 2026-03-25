**Out-of-Order**

04-14 
* Overview:
  * Register renaming
  * Instruction queues
  * Dispatch
  * Branch prediction ideas
* Slides:
  * ooo
  * bpred

_Register Renaming_
* rename architectural registers to physical registers
  * architectural = part of instruction set architecture
* Steps:
1. Rename
2. Execute (out of order)
3. ROB holds result
4. Commit (in order)
5. 
![alt text](06-01.png){size=medium}

Exercise
![alt text](06-02.png){size=medium}
1. addq %x13 %x17 --> %x18
   2. free regs: cross out %x18
   3. new mapping: %r9 --> %x18
2. movq $100 --> %x20
   3. free regs: cross out %x20
   4. new mapping: %r10 --> %x20
3. subq %x20 %x13 --> %x21; 
   4. free regs: cross out %x21
   5. new mapping: %r8 --> %x21
4. xorq %x21 %x18 --> %x23
   5. free regs: cross out %x23
   6. new mapping: %r9 --> %x23
5. andq %x04 %x23 --> %x24
   6. free regs: cross out %x24
   7. new mapping: %r9 --> %x24

_Instruction Queue and Dispatch_
| Cycle # | Execution Unit | Instruction Queue BEFORE | Scoreboard BEFORE | Instruction Queue AFTER | Scoreboard AFTER |
|----------|----------|----------|----------|----------|----------|
| 1        | ALU 1    | addq %x01, %x05 --> %x06  | %x01 ready, %x05 ready | 
| 1        | ALU 2    | {{sync}}   |

