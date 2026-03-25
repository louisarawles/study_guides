**Sync 4: Transactions**

**Sync-xact**

_Transactions_
* = set of ops that occur atomically
  * ops happen as we expect, ie, read and write to a database and those will happen as an atomic unit with nothing interleaving in the middle

Consistency / durability
* consistency --> locks
* durability --> making sure on crash, no partial transaction seen

implementation ideas
* idea: do one transaction at a time

**sync-rwlocks**

_Reader/writer_ 
Problem
* single access that can mutate data
* read-only access from multiple threads

Locks
* abstraction: lock that distinguishes readers/writers
* ops:
  * read lock
  * read unlock
  * write lock
  * write unlock


pthread rwlocks code:
````
pthread_rwlock_t rwlock;
pthread_rwlock_init(&rwlock, NULL /* attributes */);
...
    pthread_rwlock_rdlock(&rwlock);
    ...  /* read shared data */
    pthread_rwlock_unlock(&rwlock);

    pthread_rwlock_wrlock(&rwlock);
    ...  /* read+write shared data */
    pthread_rwlock_unlock(&rwlock);

...
pthread_rwlock_destroy(&rwlock);
````

rwlock effects exercise
```
pthread_rwlock_t lock;
void ThreadA() {
  pthread_rwlock_rdlock(&lock);
  puts("a");
  ...
  puts("A");
  pthread_rwlock_unlock(&lock);
}
void ThreadB() {
  pthread_rwlock_rdlock(&lock);
  puts("b");
  ...
  puts("B");
  pthread_rwlock_unlock(&lock);
}
void ThreadC() {
  pthread_rwlock_wrlock(&lock);
  puts("c");
  ...
  puts("C");
  pthread_rwlock_unlock(&lock);
}
void ThreadD() {
  pthread_rwlock_wrlock(&lock);
  puts("d");
  ...
  puts("D");
  pthread_rwlock_unlock(&lock);
}
```
Q: which of these outputs are possible?
A. aAbBcCdD
B. abABcdDC
C. cCabBAdD
D. cdCDaAbB
E. caACdDbB
ANSWER: A, C
EXPLANATION: {{
* A: if A, B, C, D run sequentially; possible
* B: in C and D, we have atomic operations where nothing could interleave while they run, so dD couldn't be in between c C
* C: atomic operations are maintained, and while A and B are separated from their lowercase, they allow interleaving
* D: same as B, interleaving while C and D are running that aren't allowed bc atomic
* E: ditto; interleaving with C that's not allowed
}}

_END OF SYNC, using primitives, etc_