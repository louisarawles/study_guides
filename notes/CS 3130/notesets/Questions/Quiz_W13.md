## Week 13 — Pipelines, Crypto, Reliability
Quiz

**Question 1 (MITM attack)**

Consider the following insecure system that accepts requests to reserve rooms for events on campus.

Since there are requirements about the confidentiality of events and about ensuring that events are only booked or modified by authorized people, the system uses asymmetric encryption and signatures, where each user and a booking server has public keys securely distributed to everyone and private keys not distributed.

To reserve a room, a user sends a message to the server containing

the user's ID (not encrypted)
a submessage, encrypted to the server's public key, containing
the user's ID, and
the name of the room they are requested
a submessage, encrypted to the server's public key, containing
the times they are requesting that room for
a digital signature generated using the user's private key over a submessage containing
the user's ID
the times they are requesting a room for
Then, the booking server sends back a response containing

a submessage, encrypted to the user's public key, containing
the user's ID,
the times successfully reserved, and
the times not successfully reserved
Assume each submessage is encrypted or signed separately.

How could a machine-in-the-middle interfere with this protocol? Select all that apply.

1. If MITM guesses the room and time, they can make the server appear to reserve that room when it does not
{{ ✅ possible because server response is encrypted but NOT signed → attacker can forge response }}
2. If MITM changes the room but keeps the same time
{{ ✅ possible because time is signed BUT room is NOT → attacker can swap room }}
3. If MITM changes the time
{{ ❌ time is signed → modification breaks signature }}
4. If MITM guesses request and learns which times succeeded
{{ ❌ response is encrypted to user → attacker cannot read it }}

Answer:
{{ change room attack, fake response attack }}

Reasoning:
{{ fields are not cryptographically bound together; encryption ≠ integrity }}

**Question 2 (Pipeline throughput)**

Suppose a pipelined processor divide the work of an running instruction into four stages. The stages require time for their computation or storage operations as follows:

Stages:

1. 300 ps
2. 400 ps
3. 600 ps
4. 200 ps

Also, we need to add pipeline registers between the stages, and these pipeline registers require 10 ps to store a value.

In instructions per second rounded to the whole number, what is the expected throughput of the processor? (There are 10 to the 12th power picoseconds per second.) Assume no relevant hazards require stalling or similar.

Register overhead = 10 ps

Key idea: Throughput = {{ 1 / (slowest stage + register overhead) }}

Compute:

* Slowest stage = {{ 600 ps }}

* Cycle time = {{ 600 + 10 = 610 ps }}

* Throughput = {{ 1 / (610 × 10⁻¹²) ≈ 1.639 × 10⁹ instructions/sec }}

Answer: {{ 1.639 × 10⁹ }}

**Question 3 (Execution time)**

Suppose we have a pipelined processor an 1000 ps (1 ns) cycle time.

We run a program on that processor which needs to run 1 billion instructions. To resolve data hazards, 5% of those instructions need to stall for 1 cycle. Also, 1% of instructions are mispredicted branches, for which the processor will fetch the wrong instruction for 2 cycles. Assume no other action is needed to resolve hazards.

How long, in milliseconds to the nearest millisecond, will the program take to run?

_Given_:

* 1 billion instructions
* 1 ns cycle
* 5% stall 1 cycle
* 1% mispredict, 2-cycle penalty

Base cycles:

{{ 1 × 10⁹ }}

Stall penalty:

{{ 0.05 × 10⁹ }}

Branch penalty:

{{ 0.02 × 10⁹ }}

Total cycles:

{{ 1.07 × 10⁹ }}

Time:

{{ 1070 ms }}

_Answer_: {{ 1070 }}

------------------------------------------------------------

For the following questions, consider a six stage pipelined processor, with the following pipeline stages (following the naming convention we used in lecture):

fetch

decode

execute, part 1

execute, part 2

memory

writeback

For the two part execute stage, the processor performs the same operation the 5-stage processor we discussed in lecture did, but across two cycles

Assume the processor resolves data hazards with forwarding and stalling, and that the processor's register file supports reading a register's value during the same cycle in which is written.

**Question 4 (Forwarding)**

Consider the following assembly snippet:

addq %r8, %r9

subq %r10, %r11

xorq %r8, %r9

movq %r13, (%r11)

nop

andq %r11, %r12

(Recall that x86-64 assembly, addq %r8, %r9 adds the register %r8 to the regsiter %r9 and places the result in register %r9. movq %r11, (%r9) takes the value of register %r11 and stores it in memory at the address specified by %r9.)

When the above assembly snippet runs, some values will be forwarded to resolve data hazards. Which of the following are examples of forwarding that would be expected? (It's possible not all forwarding is listed.) Select all that apply.

Evaluate each:
1. %r8 from addq → xorq
{{ ❌ %r8 is not modified by addq }}
2. %r9 from addq → xorq
{{ ✅ xorq needs updated %r9 immediately }}
3. %r11 from subq → movq
{{ ✅ movq uses %r11 as address right after }}
4. %r11 from movq → andq
{{ ❌ movq does not modify %r11 }}
5. %r11 from subq → andq
{{ ❌ enough delay → no forwarding needed }}

Answer:
{{ %r9 from addq → xorq, %r11 from subq → movq }}


**Question 5 (Stalling)**

Which of the following situations would require stalling on this processor? Select all that apply.

Evaluate each:
1. add → nop → use
{{ ❌ nop gives enough delay → no stall }}
2. addq %r8, %r9
3. addq %r8, %r9
{{ ✅ dependency too close → stall needed }}
4. movq (%r9), %rax
5. addq %rax, %r8
{{ ✅ load-use hazard → value arrives too late }}
6. movq (%r9), %rax
7. movq (%r10), %rbx
{{ ❌ independent → no stall }}
8. addq %r8, %r9
9. xorq %r8, %r10
10. subq %r11, %r9
{{ ❌ enough spacing → forwarding works }}

Answer:
{{repeated addq case, load-use case }}

**Question 6 (No timers, packet loss occurs)**
Evaluate each:
1. Communication fails completely
{{ ❌ not necessarily — protocol may still recover }}
2. Checksums detect loss
{{ ❌ only detect corruption }}
3. Sequence numbers let B detect missing packet
{{ ✅ B sees gap }}
4. ACKs without sequence numbers solve it
{{ ❌ sender cannot identify WHICH packet is missing }}
5. Protocol can ensure delivery with one resend
{{ ❌ without timers, sender doesn’t know when to resend }}
6. B must send ACKs twice
{{ ❌ no reason }}

Answer:
{{ sequence numbers allow B to detect missing packet }}