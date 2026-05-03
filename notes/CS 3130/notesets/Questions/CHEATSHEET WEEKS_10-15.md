Here’s a **clean, exam-focused mega cheat sheet (Weeks 10–15)** in your {{ reveal }} style. I’ve kept it tight, conceptual, and pattern-based so you can skim fast before an exam.

---

# **Weeks 10–15 Mega Cheat Sheet**

---

# 🧵 **WEEK 10 — Locks, Deadlock, Races**

## 🔒 Locking Rules

* {{ Always protect ALL shared variables consistently }}
* {{ Partial locking → inconsistent reads }}

---

## ⚠️ Race Conditions

* {{ Occur when data changes while being used }}
* Fix:

  * {{ hold the right locks }}
  * {{ or copy (snapshot) before using }}

---

## 💀 Deadlock (4 conditions)

{{

1. Mutual exclusion
2. Hold and wait
3. No preemption
4. Circular wait
   }}

---

## 🚫 Prevent Deadlock

* {{ global lock ordering }}
* {{ acquire all locks at once }}
* {{ avoid shared resources }}
* {{ at most one lock per thread }}

---

## 🧠 Key Insight

{{ Deadlock = cycle in wait-for graph }}

---

## 🍀 Lucky vs 😬 Unlucky

* {{ Lucky: bug exists but schedule avoids it }}
* {{ Unlucky: schedule exposes bug }}

---

## 🔓 Revocable Locks

* {{ can be forcibly taken away }}
* help:

  * {{ break deadlock }}
* require:

  * {{ safe rollback or interruption }}

---

---

# 🧵 **WEEK 11 — (Implicit from patterns: concurrency + ordering)**

## 🧠 Key Pattern

{{ Order matters more than resources }}

---

---

# 🌐 **WEEK 12 — Networking + Monitors**

## 📦 Condition Variables

Pattern:

```c
lock();
while (!condition) wait();
use data;
unlock();
```

---

## ❌ Common Mistakes

* {{ treating cv as boolean }}
* {{ using if instead of while }}
* {{ signaling wrong cv }}
* {{ using signal when broadcast is needed }}

---

## 📢 Signal vs Broadcast

* {{ signal → wakes one thread }}
* {{ broadcast → wakes all threads }}

Use broadcast when:
{{ multiple conditions / different waiters }}

---

## 🌐 NAT

Mapping:

{{ (remote IP:port, public port, private IP:port) }}

Rules:

* {{ router assigns public port }}
* {{ private port stays same }}

---

## 📡 Reliability

| Problem     | Needed                 |
| ----------- | ---------------------- |
| Loss        | {{ ACK + timeout }}    |
| Reordering  | {{ sequence numbers }} |
| Corruption  | {{ checksums }}        |
| Duplication | {{ sequence numbers }} |

---

---

# ⚙️ **WEEK 13 — Pipelines + Crypto**

## 🔐 Crypto (MITM)

* {{ encryption ≠ integrity }}
* {{ must sign ALL related fields }}

---

## ⚙️ Pipeline Basics

* Throughput = {{ 1 / slowest stage }}
* Latency = {{ sum of stages }}

---

## 🔄 Forwarding

* {{ used when value needed before writeback }}
* {{ only works if timing allows }}

---

## 🧱 Stalls

Occurs when:

{{ forwarding cannot satisfy timing }}

Classic case:

{{ load-use hazard }}

---

## 🧠 Dependency Types

* RAW → {{ true dependency }}
* WAR / WAW → {{ handled by renaming }}

---

---

# ⚙️ **WEEK 14 — OoO + Renaming**

## 🔁 Out-of-Order Execution

* {{ independent instructions run in parallel }}
* {{ dependencies limit execution }}

---

## 🧠 Key Rule

{{ Only dependencies matter, not instruction order }}

---

## 🔗 Dependency Types

* {{ RAW → must wait }}
* {{ WAR/WAW → eliminated by renaming }}

---

## 🧾 Register Renaming

* {{ each write gets a new physical register }}
* avoids:

  * {{ false dependencies }}

---

## 🚀 Performance Limits

* {{ dependency chain → 1 instruction per cycle }}
* width doesn’t help if:
  {{ instructions depend on previous results }}

---

---

# 🔐 **WEEK 15 — Spectre + Cache Attacks**

## 🧠 Spectre Core Idea

{{ Trick CPU into speculatively accessing secret data }}

---

## 🎯 Requirements

* {{ branch misprediction }}
* {{ secret used as memory index }}
* {{ observable side effect (cache) }}

---

## 🧵 Attack Flow

1. {{ train predictor (predict true) }}
2. {{ trigger out-of-bounds access }}
3. {{ speculative execution reads secret }}
4. {{ secret affects cache }}
5. {{ attacker measures cache }}

---

## 📦 Cache Side Channel

* {{ attacker observes which set was accessed }}
* uses:

  * {{ prime + probe }}

---

## 🧠 Key Insight

{{ speculation leaks through microarchitectural state }}

---

---

# 🧠 Cache Math (Important!)

## Address Breakdown

| Part   | Bits                   |
| ------ | ---------------------- |
| Offset | {{ log2(block size) }} |
| Index  | {{ log2(# sets) }}     |
| Tag    | {{ rest }}             |

---

## Set Index Formula

```python
set_index = (address >> offset_bits) & (num_sets - 1)
```

---

## Mask Rule

{{ mask = num_sets - 1 (if power of 2) }}

---

## Array Access

```c
array[i]
```

Address:

{{ base + i × element_size }}

---

## Block Movement

{{ floor(offset / block_size) }}

---

## Eviction

{{ occurs only if set is full }}

---

---

# 🔥 **GLOBAL PATTERNS (MOST IMPORTANT)**

---

## 🧠 Concurrency

* {{ correctness depends on ordering }}
* {{ deadlock = circular wait }}
* {{ partial locking = bugs }}

---

## ⚙️ Pipelines

* {{ slowest stage limits throughput }}
* {{ dependencies limit parallelism }}

---

## 🔐 Security

* {{ missing integrity → MITM }}
* {{ speculation leaks secrets }}

---

## 🧱 Memory + Cache

* {{ indexing is bit manipulation }}
* {{ side effects leak information }}

---

---

# 🧠 **ONE-LINE MEMORY BANK**

* {{ “Deadlock = cycle.” }}
* {{ “Forwarding if possible, stall if not.” }}
* {{ “Encryption ≠ integrity.” }}
* {{ “Speculation leaks.” }}
* {{ “Mask = num_sets − 1.” }}
* {{ “Dependency chain = 1 IPC.” }}
* {{ “Snapshot or lock — never neither.” }}

---

---

If you want, I can:

* compress this into a **1-page PDF**
* or turn it into **Anki flashcards**

Those would be perfect for last-minute review.
