			+--------------------+
			| PINTOS             |
			| PROJECT 1: THREADS |
			| DESIGN DOCUMENT    |
			+--------------------+
				   
---- GROUP ----

Serin Yu <se.yu@ucdenver.edu>

Kelli Webber <kelli.webber@ucdenver.edu>

Scott Nedvesky <scott.nedvesky@ucdenver.edu>

---- PRELIMINARIES ----

**Please cite any offline or online sources you consulted while
preparing your submission, other than the Pintos documentation, course
text, lecture notes, and course staff.**

Online sources:

http://stuartharrell.com/blog/2016/12/16/efficient-alarm-clock/

			     ALARM CLOCK
			     ===========

---- DATA STRUCTURES ----

**A1: Copy here the declaration of each new or changed `struct' or
`struct' member, global or static variable, `typedef', or
enumeration.  Identify the purpose of each in 25 words or less.**

In timer.c:

static struct list sleep_list - this list keeps track of sleeping threads 

In thread.h:

int64_t ticks - this value determines how long a thread is sleeping for and 
when it should be unblocked


---- ALGORITHMS ----

**A2: Briefly describe what happens in a call to timer_sleep(),
including the effects of the timer interrupt handler.**

First, the function checks that the 'ticks' argument is a positive number. 
Next, this number is added to the global ticks value and passed into the 
thread_sleep_until() function. Here, the current thread is blocked and set 
to wake up after ticks. The sleeping thread's information is inserted into 
the sleeping thread list and the next thread is scheduled. 
The timer interrupt handler get's the list_front and checks if the thread's
ticks value is less than the global ticks. If it is, the thread is removed
from the sleep list and unblocked. This is repeated until the sleep list
is empty.

**A3: What steps are taken to minimize the amount of time spent in
the timer interrupt handler?**

The sleep list is sorted. By doing this, the interrupt handler doesn't 
have to travrse the entire list at every interrupt. 

---- SYNCHRONIZATION ----

**A4: How are race conditions avoided when multiple threads call
timer_sleep() simultaneously?**

Interrupts are temporarily diabled in timer_sleep().

**A5: How are race conditions avoided when a timer interrupt occurs
during a call to timer_sleep()?**

Since timer_sleep() checks that the ticks value is valid and if not,
the thread does not sleep, thus, avoiding race conditions. 

---- RATIONALE ----

**A6: Why did you choose this design?  In what ways is it superior to
another design you considered?**

The previous implementation of timer_sleep() called thread_yield()in a loop.
By doing this, there was nothing preventing a thread from being scheduled 
again even though it could still be sleeping. By implementing our design,
we were able to avoid busy waiting by blocking the sleeping thread so that
CPU cycles could be used to schedule non-sleeping threads. 



			 PRIORITY SCHEDULING
			 ===================

---- DATA STRUCTURES ----

**B1: Copy here the declaration of each new or changed `struct' or
`struct' member, global or static variable, `typedef', or
enumeration.  Identify the purpose of each in 25 words or less.**

The folowing members were added to *struct* thread:

int init_priority - the priority of the thread

struct lock *waiting_lock - lock that a thread is waiting on

struct list donations_list - list of threads waiting on locks 

struct list_elem donation_elem - element that can be added to another 

thread's donations list 

**B2: Explain the data structure used to track priority donation.
Use ASCII art to diagram a nested donation.  (Alternately, submit a
.png file.)**




---- ALGORITHMS ----

**B3: How do you ensure that the highest priority thread waiting for
a lock, semaphore, or condition variable wakes up first?**

**B4: Describe the sequence of events when a call to lock_acquire()
causes a priority donation.  How is nested donation handled?**

**B5: Describe the sequence of events when lock_release() is called
on a lock that a higher-priority thread is waiting for.**

---- SYNCHRONIZATION ----

**B6: Describe a potential race in thread_set_priority() and explain
how your implementation avoids it.  Can you use a lock to avoid
this race?**

---- RATIONALE ----

**B7: Why did you choose this design?  In what ways is it superior to
another design you considered?**
