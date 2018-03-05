
<h1 align="center"> PINTOS</h1>
<h2 align="center"> PROJECT 1: THREADS</h2> 
<h3 align="center"> DESIGN DOCUMENT</h3>


### GROUP

Serin Yu | <se.yu@ucdenver.edu>

Kelli Webber | <kelli.webber@ucdenver.edu>

Scott Nedvesky | <scott.nedvesky@ucdenver.edu>

### PRELIMINARIES 

Online sources:

http://stuartharrell.com/blog/2016/12/16/efficient-alarm-clock/

<h2 align="center"> ALARM CLOCK </h2>

### DATA STRUCTURES 

**A1: Copy here the declaration of each new or changed `struct` or
`struct` member, global or static variable, `typedef`, or
enumeration.  Identify the purpose of each in 25 words or less.**

In timer.c:

`static struct list sleep_list` - this list keeps track of sleeping threads 

In thread.h:

`int64_t sleep_ticks` - this value determines how long a thread is sleeping for and 
when it should be unblocked


### ALGORITHMS 

**A2: Briefly describe what happens in a call to timer_sleep(),
including the effects of the timer interrupt handler.**

First, the function checks that the 'ticks' argument is a positive number. 
Next, the thread's number of ticks to sleep is set by adding the global ticks value
(since OS booted) with the requested number of ticks.  
The sleeping thread is inserted into the sleeping thread list in sorted order
by ascending tick values and is blocked so that the next thread can run.
The timer interrupt handler checks if the first thread in the sleep list has a
tick value less than the global ticks. If it is, the thread is removed
from the sleep list and unblocked. This is repeated until all the threads
in the list that can wake up is unblocked and added to the ready list.

**A3: What steps are taken to minimize the amount of time spent in
the timer interrupt handler?**

The sleep list is sorted. By doing this, the interrupt handler doesn't 
have to travrse the entire list at every interrupt (efficient). 

### SYNCHRONIZATION 

**A4: How are race conditions avoided when multiple threads call
timer_sleep() simultaneously?**

Interrupts are temporarily diabled in timer_sleep().

**A5: How are race conditions avoided when a timer interrupt occurs
during a call to timer_sleep()?**

Since timer_sleep() checks that the ticks value is valid and if not,
the thread does not sleep, thus, avoiding race conditions. 

### RATIONALE 

**A6: Why did you choose this design?  In what ways is it superior to
another design you considered?**

The previous implementation of timer_sleep() called thread_yield() in a loop.
By doing this, there was nothing preventing a thread from being scheduled 
again even though it could still be sleeping. By implementing our design,
we were able to avoid busy waiting by blocking the sleeping thread so that
CPU cycles could be used to schedule non-sleeping threads. 



<h2 align="center"> PRIORITY SCHEDULING </h2>


### DATA STRUCTURES 

**B1: Copy here the declaration of each new or changed `struct` or
`struct` member, global or static variable, `typedef`, or
enumeration.  Identify the purpose of each in 25 words or less.**

The folowing members were added to `struct thread`:

`int init_priority` - the priority of the thread

`struct lock *waiting_lock` - lock that a thread is waiting on

`struct list donations_list` - list of threads waiting on locks 

`struct list_elem donation_elem` - element that can be added to another thread's donations list 

**B2: Explain the data structure used to track priority donation.
Use ASCII art to diagram a nested donation.  (Alternately, submit a
.png file.)**

![alt text](https://github.com/ucd-os-fuschia-s18/pintos/blob/master/diagram.png "Logo Title Text 1")


### ALGORITHMS 

**B3: How do you ensure that the highest priority thread waiting for
a lock, semaphore, or condition variable wakes up first?**

The way the threads are entered into the waiting list is based upon priority to begin with. So when activity occurs with either a lock or semaphore, we check for the one with the highest priority and bump them from the waiting list. The same exact thing occurs with a conditional variable as well. 

**B4: Describe the sequence of events when a call to lock_acquire()
causes a priority donation.  How is nested donation handled?**

1) The current thread's waiting lock value is set to *lock*
2) The thread is added to the lock holder's donations list 

**B5: Describe the sequence of events when lock_release() is called
on a lock that a higher-priority thread is waiting for.**

### SYNCHRONIZATION 

**B6: Describe a potential race in thread_set_priority() and explain
how your implementation avoids it.  Can you use a lock to avoid
this race?**

### RATIONALE 

**B7: Why did you choose this design?  In what ways is it superior to
another design you considered?**
