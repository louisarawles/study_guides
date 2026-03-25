**Synchronization 3 --- monitors, producer/consumer**

_Sync-monitors_
* Facilitate coordination between threads

_Monitors/condition variables_
* locks
  * for mutual exclusion
  * MUST be acquired before accessing any part of monitor's stuff
    * otherwise you get race conditions
  * reps queue of threads waiting for lock
* condition variables
  * for events and communicating the events b/w threads and any shared state threads need to coordinate
  * also reps list of waiting threads for condition to be true about shared data
  * API/actions: 
    * wait
      * 2 args bc requires cv and lock
      * removes lock after waits bc no longer needs it then doesn't return till it reacquires the lock, allowing you to access the shared data again
      * adds you to list of threads waiting for lock based on cv
        * can have multiple cv, and all threa
    * signal
      * get one thread waiting on condition; signal v broadcast j depends on what the application is doing
    * broadcast
      * something changed --> event true --> notify all waiting threads --> threads in some order get access to the lock
* monitor
  * combines locks and condition variables

pthread cv usage
**Code**
// MISSING: init calls, etc.
pthread_mutex_t lock;
bool finished;   // data, only accessed with after acquiring lock
pthread_cond_t finished_cv;  // to wait for 'finished' to be true

void WaitForFinished() {
  pthread_mutex_lock(&lock);
  while (!finished) {
    pthread_cond_wait(&finished_cv, &lock);
  }
  pthread_mutex_unlock(&lock);
}

void Finish() {
  pthread_mutex_lock(&lock);
  finished = true;
  pthread_cond_broadcast(&finished_cv);
  pthread_mutex_unlock(&lock);
}

Order:
1. Lock: acquire before reading or write finished
   2. pthread_mutex_lock(&lock);
   3. pthread_mutex_lock(&lock);
4. Check waiting: check whether we need to wait at all; yes, must be a loop
   5. while (!finished)
6. Do waiting: know we need to wait, so wait releasing lock (lock must be released so finished can change)
   7. pthread_cond_wait(&finished_cv, &lock);
8. Broadcast: all waiters to proceed once we release lock
   9. pthread_cond_broadcast(&finished_cv)

WaitForFinish timeline 1
![00 waitforfinish](00 waitforfinish.png){size=small}

WaitForFinish timeline 2
![image 1](00 waitforfinish2.png){size=small}

Why the loop?
* _Spurious wakeups_ (= _wait_ returns even though nothing happened) may occur

_Producer Consumer Pattern_
* 1 thread produce, some other thread execute on that output
* ie: networking: request put in, another thread figure out what that thread is, put that in the queue, but don't exactly know when the request will come through

Unbounded buffer
pthread_mutex_t lock;
pthread_cond_t data_ready;
Queue buffer;
Produce(item) {
    pthread_mutex_lock(&lock);
    buffer.enqueue(item);
    pthread_cond_signal(&data_ready);
    pthread_mutex_unlock(&lock);
}
Consume() {
    pthread_mutex_lock(&lock);
    while (buffer.empty()) {
        pthread_cond_wait(&data_ready, &lock);
    }
    item = buffer.dequeue();
    pthread_mutex_unlock(&lock);
    return item;
}
* btw use signal bc only need to wake up one thread in this specific ie
1. Consumer side: Dequeue if empty and wake : check if empty, once not empty, then dequeue THEN release lock
   2. Check: while (buffer.empty())
      3. wait if it's empty
   3. Dequeue: item = buffer.dequeue()
      4. since have lock, don't need to worry about someone else dequeuing
   3. Release lock: pthread_mutex_unlock(&lock);
4. Produce side: Wake
   6. Enqueue:
      7. buffer.enqueue(item); 
   8. Wake one consume thread (signal one thread to wake up to handle new item)
      9. pthread_cond_signal(&data_ready);
![image 1](00 unbounded p-c waiting.png){size=small}

Hoare vs Mesa monitors
* Hoare:
  * signal 'hands off' lock to awoken thread
* Mesa-style:
  * any eligible thread gets lock next
  * maybe some other idea of priority?
  * more common than hoare; generally assume for classes but assume for both cases

_Bounded buffer_
**Code**
pthread_mutex_t lock;
pthread_cond_t data_ready;
pthread_cond_t space_ready;
BoundedQueue buffer;
Produce(item) {
    pthread_mutex_lock(&lock);
    while (buffer.full()) {
        pthread_cond_wait(&space_ready, &lock);
    }
    buffer.enqueue(item);
    pthread_cond_signal(&data_ready);
    pthread_mutex_unlock(&lock);
}
Consume() {
    pthread_mutex_lock(&lock);
    while (buffer.empty()) {
        pthread_cond_wait(&data_ready, &lock);
    }
    item = buffer.dequeue();
    pthread_cond_signal(&space_ready);
    pthread_mutex_unlock(&lock);
    return item;
}
1. Added
   2. pthread_cond_t space_ready
   3. while (buffer.full()) {
        pthread_cond_wait(&space_ready, &lock);
    }
   4. pthread_cond_signal(&space_ready)
5. Signal/broadcast
   6. pthread_cond_signal(&space_ready)

_General Monitor Pattern_
**Code**
pthread_mutex_lock(&lock);
while (!condition A) {
    pthread_cond_wait(&condvar_for_A, &lock);
}
... /* manipulate shared data, changing other conditions */
if (set condition A) {
    pthread_cond_broadcast(&condvar_for_A);
    /* or signal, if only one thread cares */
}
if (set condition B) {
    pthread_cond_broadcast(&condvar_for_B);
    /* or signal, if only one thread cares */
}
...
pthread_mutex_unlock(&lock)

Monitors Rules of Thumb
* never touch shared data w/o holding the lock
* keep lock held for entire operation
  * verifying condition up to and including
  * manipulating data
* create cv for every kind of scenario waited for
* always write loop calling cond_wait to wait for condition X
* broadcast/signal condition variable everytime you change X
* correct but slow to...
  * broadcast when signal would work
  * broadcast or signal when nothing changed
  * use one condvar for multiple conditions

_Mutex/cond var init/destroy_
pthread_mutex_t mutex;
pthread_cond_t cv;
pthread_mutex_init(&mutex, NULL);
pthread_cond_init(&cv, NULL);
// --OR--
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t cv = PTHREAD_COND_INITIALIZER;

// and when done:
...
pthread_cond_destroy(&cv);
pthread_mutex_destroy(&mutex);

_wait for both finished_
// MISSING: init calls, etc.
pthread_mutex_t lock;
bool finished[2];
pthread_cond_t both_finished_cv;

void WaitForBothFinished() {
  pthread_mutex_lock(&lock);
  while (_____________________________) {
    pthread_cond_wait(&both_finished_cv, &lock);
  }
  pthread_mutex_unlock(&lock);
}

void Finish(int index) {
  pthread_mutex_lock(&lock);
  finished[index] = true;
  _____________________________________
  pthread_mutex_unlock(&lock);
}
Q: What do you put in the first while statement?
A. finished[0] && finished[1] 
B. finished[0] || finished[1]
C. !finished[0] || !finished[1] 
D. finsihed[0] != finished[1]
E. something else
ANSWER: {{c}}
EXPLANATION: {{ A is what we want, but we have to check if we have to wait}}
Q: What do you put in the second while statement?
A. pthread_cond_signal(&both_finished_cv)
B. pthread_cond_broadcast(&both_finished_cv)
C. if (finished[1-index])
          pthread_cond_signal(&both_finished_cv);
D. if (finished[1-index])
          pthread_cond_broadcast(&both_finished_cv);
E. something else
ANSWER: {{D}}
EXPLANATION: {{ Already know that one is finished at index, we just set one of them to be true, suggesting we need the if statement so we're not waking up a thread unless both are finished (B would give same end result, but D is probs best answer) }}

_Monitor Exercise: One-use Barrier_

struct BarrierInfo {
    pthread_mutex_t lock;
    int total_threads;  // initially total # of threads
    int number_reached; // initially 0
    {{ pthread_cond_t cv; }} {{ # add condition variable to wait on condition, distinct from condition }}
};

void BarrierWait(BarrierInfo *b) {
    pthread_mutex_lock(&b->lock);
    ++b->number_reached;
    if (b->number_reached == b->total_threads) {
        {{ pthread_cond_broadcast(&b->cv); }} {{ # if the if statement is true, then broadcast condition variable }}
    } else {
        while (b->number_reached < b->total_threads)
            {{ pthread_cond_wait(&b->cv, &b->lock); }} {{ # have to wait while we are NOT the last thread, waiting on cv (ie, until all threads hit the barrier) and holding lock while we wait }}
    }
    pthread_mutex_unlock(&b->lock);
}