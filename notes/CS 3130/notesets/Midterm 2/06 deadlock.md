## Deadlock

**03/17**

* We wanna implement a program that lets us move files: 
```c
struct Dir {
    mutex_t lock; HashMap entries;
};
void MoveFile(Dir *from_dir, Dir *to_dir, string filename) {
    mutex_lock(&from_dir−>lock);
    mutex_lock(&to_dir−>lock);

    Map_put(to_dir−>entries, filename,
    Map_get(from_dir−>entries, filename));
    Map_erase(from_dir−>entries, filename);

    mutex_unlock(&to_dir−>lock);
    mutex_unlock(&from_dir−>lock);
}
```
* Suppose Thread 1 ran `MoveFile(A, B, "foo")` and Thread 2 ran `MoveFile(B, A, "bar")`. 
  * In a **lucky** timeline (without a deadlock), one thread {{acquires both locks}} first, finishes, then the other proceeds. 
  * This could look like:

  ![image](lucky_deadlock.png)

  * or:

  ![image](lucky_deadlock2.png)

  * In an **unlucky** timeline, {{one thread acquires one lock, the other acquires another, and the two are stuck forever waiting for each other}}.
  * This could look like:

  ![image](unlucky_deadlock.png)
 

_Deadlock with free space_
* Means: {{ A system is deadlocked even though there is still unused capacity (free space) available }}
* Typically, you think no space --> {{ blocked }} and no data --> {{ blocked }}, but here: {{ there is space, but no one can use it }}
  * Ie: bounded buffer;
    * buffer size = 10
    * curr items = 5, so 5 free slots
    * BUT:
      * all producers aer blocked waiting for something
      * all consumers are blocked waiting for something
      * SO system is stuck
* Root cause: {{ circular wait / incorrect ordering of locks or conditions; threads are waiting on the wrong condition at the wrong time }}
* Ie:
  * producers waiting for space even though space exists
  * consumers waiting for data even though producers are blocked

**_Lucky versus unlucky execution comes down to thread scheduling order._**
* lucky: {{ The scheduling order avoids deadlock even though the program is flawed }}
* unlucky: {{ the scheduling order exposes the deadlock, so threads interleave in a bad way and deadlock occurs }}

### exercise: deadlock with free space

what's wrong with this logic and how might you fix it?

```
wait(empty);
lock(mutex);

wait(full);
```

Problem: {{ wrong order; never unlocks the mutex and doesn't signal the resource is available again }}

Correct version:
```
wait(empty);
lock(mutex);
/* critical section */
unlock(mutex);
signal(full);
```
so, a deadlock with free space is {{ a logical deadlock }} it is not {{ physical resource exhaustion }}

* It's possible to have deadlocks that span more than two threads. For example, with three directories (A, B, C) and three threads, a deadlock could arise if:
    * Thread 1 holds A → wants B
    * Thread 2 holds B → wants C
    * Thread 3 holds {{C}} → wants {{A}}
* Deadlock isn't just about locks; it could be about any resource that can be held and waited on. For example, if two threads each want 2 MB of memory, the system has exactly 2 MB total, and each thread {{allocates 1 MB}} successfully. Then both threads are stuck waiting for {{the memory held by the other thread}}.

----------------------------------------------------------------------

### exercise: Given the list A, B, C, D, E, which of these (all run in parallel) can deadlock?

```c
RemoveNode(LinkedListNode *node) {
    pthread_mutex_lock(&node−>lock);
    pthread_mutex_lock(&node−>prev−>lock);
    pthread_mutex_lock(&node−>next−>lock);

    node−>next−>prev = node−>prev; node−>prev−>next = node−>next;
    
    pthread_mutex_unlock(&node−>next−>lock); 
    pthread_mutex_unlock(&node−>prev−>lock); 
    pthread_mutex_unlock(&node−>lock); 
}
```

* RemoveNode(B) and RemoveNode(C) {{ Y --> B and C both access two of the same resources, so they could be waiting on a shared resource while holding something else they both share that the other thread wants }}
* RemoveNode(B) and RemoveNode(D) {{ N --> B and D only share the resource C, so no circular wait; so once it's available for one of them, they won't be fighting over another resource or interleaving; they just go on their merry little way  }}
* RemoveNode(B) and RemoveNode(C) and RemoveNode(D) {{ Y --> like the first option except this time C and D also share multiple resources, so now you just have an absolute mess }}

----------------------------------------------------------------------

* The four conditions required for a deadlock are: 
    1. Mutual exclusion: only one thread can {{use a resource at a time}}.
    2. Hold and wait: a thread {{holds one resource}} while waiting for another.
    3. No preemption: resources can’t be {{forcibly taken away}}.
    4. Circular wait: A cycle exists (T1 waits for T2, T2 waits for T3, …, Tn waits for {{T1}}).
* Fill out this table.

| deadlock condition | mitigating strategy(s)                                                                                                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|
| Mutual exclusion   | (1) {{infinite resources}}; (2) no {{shared resources}}                                                                   |
| Hold and wait      | (1) instead of waiting for a resource, a thread {{immediately aborts and retries}}; (2) {{request all resources at once}} |
| No preemption      | allow for {{resources to be forcibly taken from a thread}}                                                                |
| Circular wait      | {{acquire resources in a consistent order. This is the BEST strategy!!}}                                                  |

### abort and retry
* The issue with immediately aborting and retrying instead of waiting for resources to free up is that {{you may have to retry many times in order to achieve the task}}. A situation where you keep aborting and retrying without end is called {{livelock}}.
    * One possible mitigation strategy for livelock is making schedule random (ie random waiting after abort). 
    * Another strategy is to make threads run one-at-a-time.
* In order to allow for preemption (for threads to forcibly take resources from one another), you also need make sure you have a way to {{undo partial operations}}.
* moving 2 files - abort and retry:

```
struct Dir { mutex_t lock; HashMap entries; };
void MoveFile(Dir *from_dir, Dir *to_dir, string filename) {
  while (true) {
    mutex_lock(&from_dir->lock);
    if (mutex_trylock(&to_dir->lock) == LOCKED) break;
    mutex_unlock(&from_dir->lock);
  }
    
  Map_put(to_dir->entries, filename, Map_get(from_dir->entries, filename));
  from_dir->entries.erase(filename);

  mutex_unlock(&to_dir->lock);
  mutex_unlock(&from_dir->lock);
}
```
* if abort and retry fails, you can do one of two things:
  * both try again, but if this keeps happening, you get a {{ livelock }}
  * try one-at-a-time, which is _guaranteed_ to work, but it's tricky to implement

### livelock
* = {{ keep aborting and retrying without end }}
* like deadlock because: {{ no one's making progress, potentially forever }}
* unlike deadlock because: {{ threads aren't waiting }}

### livelock prevention 
  * options:
    * {{ make schedule random -- ie random waiting after abort }}
    * {{ make threads run one-at-a-time if lots of aborting }}

### deadlock prevention
* stealing locks
  * unclean: {{ kill the thread }}
    * problem: {{ inconsistent state }}
  * clean: {{ have code to undo the partial operation }}
    * some dbs do this --> called {{ revocable locks }}
    * idt this'll be on the exam tbh
* revocable locks:
```  
try {
    AcquireLock();
    use shared data
} catch (LockRevokedException le) {
    undo operation hopefully?
} finally {
    ReleaseLock();
}
```
  * definition: {{ a lock that can be forcibly taken away from a thread }}
  * originally, thread acquires lock --> keeps it until it releases it
  * with revocable locks, another thread or system can revoke it
  * danger: {{ the thread may be in the middle of a critical section (so shared data may be inconsistent and invariants may be broken) }}
  * to use revocable locks safely, you need {{ a way to roll back or safely interrupt the critical section, ie checkpointing, transactional memory, idempotent operations }}
  * allows system to recover even in unlucky cases, so they help with {{ deadlock avoidance / recovery }} but they require {{ safe interruption or rollback }} as otherwise they can {{ break consistency and correctness }}
    * tradeoff = {{ progress vs correctness }}
* acquiring locks in consistent order
```
MoveFile(Dir* from_dir, Dir* to_dir, string filename) {
  if (from_dir->path < to_dir->path) {
    lock(&from_dir->lock);
    lock(&to_dir->lock);
  } else {
    lock(&to_dir->lock);
    lock(&from_dir->lock);
  }
  ...
}
```
  * from_dir->path < to_dir->path --> any ordering will do, ie here compare pointers
  * avoids circular dependency, and it means everyone else _eventually_ gets a turn
* allocating all at once
  * useful for {{ resources like disk space, memory }}
  * figure out max allocation when {{ starting thread to give "only" a conservative estimate }}
  * only start thread if those resources are available
  * this is called _Banker's algorithm_

### deadlock detection for prevention -- Banker's Algorithm
* if you know max resources a process could request, make decision {{ when starting process ("admission control") }}
* ask, "what if {{ every process was waiting for maximum resources }}, including the one we're starting"
  * if that condition would cause a deadlock --> don't let it start
----------------------------------------------------------------------

### exercise: pipe() deadlock
```
int child_to_parent_pipe[2], parent_to_child_pipe[2];
pipe(child_to_parent_pipe); pipe(parent_to_child_pipe);
if (fork() == 0) {
    /* child */
    write(child_to_parent_pipe[1], buffer, HUGE_SIZE);
    read(parent_to_child_pipe[0], buffer, HUGE_SIZE);
    exit(0);
} else {
    /* parent */
    write(parent_to_child_pipe[1], buffer, HUGE_SIZE);
    read(child_to_parent_pipe[0], buffer, HUGE_SIZE);
}
```
what's the problem here? {{ this will hang forever if HUGE_SIZE is big enough }}

* waiting:
  * child writing to pip waiting for free buffer space ... {{ which won't be available until parent reads }}
  * parent writing to pipe waiting for free buffer space ... {{ which will not be available until child reads }}

----------------------------------------------------------------------


```
Lock chopsticks[NUM_CHOPSTICKS];

int GetChopstick() {
    for (int i = 0; i < NUM_CHOPSTICKS; i++) {
        if (trylock(chopsticks[i])) return i;
    }
    return -1; 
}

void PutDown(x) { Unlock(chopsticks[x]); }

void Eat() {
    left = right = -1;

    while (left == -1)
        left = GetChopstick();

    while (right == -1)
        right = GetChopstick();

    // use chopsticks and eat

    PutDown(left)
    PutDown(right)
}
```
* Exercise: Consider the code above; suppose we modify the dining philosophers to put all chopsticks in the center of the table where each of the philosophers takes the first of those chopsticks that is available. For each of the following scenarios, is deadlock possible?
    * 5 philosophers, 5 chopsticks {{`Y`}}
    * 4 philosophers, 5 chopsticks {{`N`}}
    * 5 philosophers, 6 chopsticks {{`N`}}
    * 5 philosophers, 4 chopsticks {{`Y`}}


THIS ISNT RELEVANT ITS JUST I ACCIDENTALLY TOOK NOTES TWICE
_Deadlock Requirements_
* Mutual exclusion
* Hold and wait
* No preemption of resources
* Circular wait

How is deadlock possible?
![image 1](00-01%20how_deadlock.png){size=small}
Given list: A, B, C, D, E

```
  RemoveNode(LinkedListNode *node) { 
    pthread_mutex_lock(&node−>lock); 
    pthread_mutex_lock(&node−>prev−>lock); 
    pthread_mutex_lock(&node−>next−>lock); 
    node−>next−>prev = node−>prev; 
    node−>prev−>next = node−>next; 
    pthread_mutex_unlock(&node−>next−>lock); 
    pthread_mutex_unlock(&node−>prev−>lock); 
    pthread_mutex_unlock(&node−>lock); 
  } 
```


Which of these (all run in parallel) can deadlock? 
A. RemoveNode(B) and RemoveNode(C) 
B. RemoveNode(B) and RemoveNode(D) 
C. RemoveNode(B) and RemoveNode(C) and RemoveNode(D) 
D. A and C 
E. B and C 
F. all of the above 
G. none of the above

Deadlock prevention techniques
* infinite resources
  * no mutual exclusion
  * not practical
* no shared resources
  * no mutual exclusion
  * defeats purpose of having multiple threads
* no waiting
  * avoids waiting for forever
  * no hold and wait / preemption
  * Options:
    * abort and retry
    * preempt resources aka steal locks
  * acquire resources in consistent order
    * no circular wait
  * request all resources at once
    * no hold and wait

Abort and Retry
* how many times will you retry?

```
struct Dir { 
mutex_t lock; 
HashMap entries; 
}; 
void MoveFile(Dir *from_dir, Dir *to_dir, string filename) { 
while (true) { 
mutex_lock(&from_dir−>lock); 
if (mutex_trylock(&to_dir−>lock) == LOCKED) break; 
mutex_unlock(&from_dir−>lock); 
} 
Map_put(to_dir−>entries, filename, Map_get(from_dir−>entries, filename)); 
from_dir−>entries.erase(filename); 
mutex_unlock(&to_dir−>lock); 
mutex_unlock(&from_dir−>lock); 
}
```

* possible problem: livelock
  * essentially, really bad luck during abort and retry
  ![image 1](00 livelock.png){size=small}
  * prevention:
    * make schedule random
    * make threads run one at a time

Stealing locks
* unclean: just kill the thread
* clean: have code to undo partial operation
  * revocable locks

Acquiring locks in consistent order
* achieve either by comparing pointers OR by convention (ie document the required order and enforce it you HAVE to use them in this order)