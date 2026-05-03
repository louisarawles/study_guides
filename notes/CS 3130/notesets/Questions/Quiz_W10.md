## Week 10 — Locks, Deadlock, Race Conditions

---

## Question 1 (Game with count + goal)

```
pthread_mutex_t count_lock = PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_t goal_lock = PTHREAD_MUTEX_INITIALIZER;

int goal = 5;
int count = 0;

void change_goal(int new_goal) {
  pthread_mutex_lock(&goal_lock);
  goal = new_goal;
  pthread_mutex_unlock(&goal_lock);
}

void add_count(int add) {
  pthread_mutex_lock(&count_lock);
  count += add;
  pthread_mutex_unlock(&count_lock);
}

void subtract_count(int subtract) {
  pthread_mutex_lock(&goal_lock);
  pthread_mutex_lock(&count_lock);
  count -= subtract;
  pthread_mutex_unlock(&goal_lock);
  pthread_mutex_unlock(&count_lock);
}

bool check() {
  pthread_mutex_lock(&count_lock);
  if (goal == count) printf("Winner! Goal %d reached by count %d.\n", goal, count);
  pthread_mutex_unlock(&count_lock);
}
```


#### Assume there are exactly three threads playing this game. The threads can only access count_lock, goal_lock, goal, and count using the functions listed. No other threads are accessing this library.
#### What is possible while this game is being played? Select all that apply.

---

1. add_count() and subtract_count() are called simultaneously and an addition operation is lost.
   {{ ❌ both use count_lock → no lost update }}

---

2. add_count() and subtract_count() are called simultaneously and a subtraction operation is lost.
   {{ ❌ same reason — count is protected }}

---

3. subtract_count() and check() are called simultaneously and the game deadlocks.
   {{ ❌ no circular wait (check only takes count_lock) }}

---

4. subtract_count() and change_goal() are called simultaneously and the game deadlocks.
   {{ ❌ change_goal only uses goal_lock → no cycle }}

---

5. Winner prints mismatched goal and count
   {{ ✅ YES — check() locks count_lock but NOT goal_lock, so goal can change during check }}

---

6. Two threads deadlock, third continues
   {{ ❌ no deadlock cycle exists }}

---

7. Goal changes after check() is called
   {{ ✅ YES — goal is not locked during check() }}

---

## **Answer:**

{{ mismatched goal/count print; goal can change during check }}

---

## 🧠 Key insight

{{ reading shared variables without locking all relevant locks → inconsistent view }}

---

---

---------------------------------------------------------------------------------

Consider the following code snippet.

```
struct hardware_resource {
  pthread_mutex_t lock;
  int id;
}

struct interrupt {
  struct hardware *hardware_resource[5];
  int operation;
}

pthread_mutex_t interrupts_lock;
struct interrupt *pending_interrupts[100];

void service_next_interrupt(int interrupt_index) {
  pthread_mutex_lock(&interrupts_lock);
  struct interrupt *Interrupt = pending_interrupts[interrupt_index];
  pthread_mutex_unlock(&interrupts_lock);

  for (int i=0; i<5; i++) {
    if (Interrupt->hardware_resource[i] != NULL) {
      pthread_mutex_lock(&Interrupt->hardware_resource[i]->lock);
    }
  }

  do_operation(Interrupt->hardware_resource, Interrupt->operation);

  for (int i=0; i<5; i++) {
    if (Interrupt->hardware_resource[i] != NULL) {
      pthread_mutex_unlock(&Interrupt.hardware_resource[i]->lock);
    }
  }
}
```

`service_next_interrupt()` is called from multiple threads.

## Question 2 (Interrupt + hardware resources)

#### Assume no other relevant code references the above structures or their locks other than to initialize them. Which of the following conditoins are sufficient to ensure that deadlock will not occur for the above code as a result of how it uses mutexes? Select all that apply.

---

1. All hardware resources in every struct interrupt are listed in the same order
   {{ ✅ YES — enforces global lock ordering → prevents circular wait }}

---

2. Any two struct interrupts have at most 1 hardware resource in common.
   {{ ❌ still possible to form cycles. Say you have T1 using {A,B}, T2 {B,C}, T3 {C,A}, then each pair shares only one resource, but each individual thread shares a resource with the two other threads, meaning ultimately, a cycle exists as 1. T1 locks A, waits for B. 2. T2 locks B, waits for C. 3. T3 locks C, waits for A. }}

---

3. Each struct interrupt points to a distinct set of hardware resources, and each thread calling service_next_interrupt uses a distinct interrupt_index
   {{ ✅ YES — no shared locks → no contention → no deadlock }}

---

4. Each time service_next_interrupt is called, any other threads calling service_next_interrupt have at least reached the first for loop.
   {{ ❌ timing does not prevent circular wait }}

---

5. Each interrupt, aka the list of hardware resources in each struct interrupt, has 4 NULL items
   {{ ✅ YES — at most one lock per thread → no cycle possible; each interrupt locks at most 1 resource }}

---

6. Each thread uses different interrupt_index when multiple threads call service_next_interrupt()
   {{ ❌ they may still share hardware resources }}

---

## **Answer:**

{{ same lock ordering, disjoint resource sets, at most one lock per thread }}

---

## 🧠 Key patterns

* deadlock = {{ circular wait }}
* prevent by {{ ordering }} OR {{ removing shared resources }}
* limiting overlap per pair does {{ not remove shared resources and prevent global cycles }}

---

## Important condition comparison

| Condition                     | Deadlock possible? | why                                                               |
|-------------------------------|--------------------|-------------------------------------------------------------------|
| <= 1 shared resource per pair | {{ yes }}          | {{ cycles across 3+ threads }}                                    |
| <= 1 lock per thread          | {{ no }}           | {{ no circular wait possible when each thread only uses 1 lock }} |


---

## Question 3 (Unlocking something not locked)


#### Suppose some code modifies the list of hardware resources for *pending_interrupts[10] while service_next_interrupt(10) is running. As a result, we find that service_next_interrupt attempts to unlock a struct hardware lock is that is not actually locked. This occurs even though the code that modifies the list of hardware resources holds the interrupts_lock while doing that modification.

#### What’s happening?

{{ Race condition: the resource list is modified while being used }}

---

#### What would be ways of preventing this issue? Select all that apply.

---

1. Add a lock per interrupt (wrap BOTH loops) -- add a lock to each struct interrupt, have service_next_interrupt acquire that lock just before its first for loop and release it just after, and have the code that modifies the hardware resources also acquire that lock before it modifies the list and release it afterwards
   {{ ⚠️ only works if held across BOTH loops; otherwise still racy }}

---

2. Unlock interrupts_lock later -- have service_next_interrupt unlock the interrupts_lock immediately after the first for loop instead of earlier
   {{ ❌ does not protect resource list during iteration }}

---

3. Lock each hardware resource before modifying list -- have the code that modifies the list of hardware resources acquire the lock of each of those hardware resources before changing the list
   {{ ❌ race still possible before lock is acquired }}

---

4. Copy the interrupt locally -- replace struct interrupt *Interrupt = pending_interrupts[interrupt_index] with struct interrupt Interrupt = *pending_interrupts[interrupt_index] and cahgning instances of Interrupt-> to Interrupt.

```c
struct interrupt Interrupt = *pending_interrupts[index];
```

{{ ✅ YES — snapshot prevents concurrent modification issues }}

---

## **Answer:**

{{ copying the struct locally }}

---

## 🧠 Key insight

{{ Use a snapshot to avoid concurrent mutation }}

---

# 🔥 Final Cheat Sheet Summary

---

### 🔒 Locking

* {{ protect ALL shared variables consistently }}
* {{ partial locking → inconsistent state }}

---

### 💀 Deadlock

* {{ requires circular wait }}
* prevent via:

  * {{ global ordering }}
  * {{ no shared resources }}
  * {{ at most one lock }}

---

### ⚠️ Race conditions

* {{ occur when data changes mid-use }}
* fix via:

  * {{ locking }}
  * {{ copying (snapshot) }}

---

### 🧠 One-liners

* {{ “If you read without locking everything, you see lies.” }}
* {{ “Deadlock = cycle in lock acquisition.” }}
* {{ “Snapshots prevent mid-iteration mutation bugs.” }}

---

If you want, I can combine **Weeks 10–15 into one mega cheat sheet** — that would basically be your entire exam.
