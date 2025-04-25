
**1.1** What is _wrong_ with this "optimized" version of the code IF you were actually running it in a multi-threaded environment even if it does all the same things as the original code in terms of modifying memory as needed when it ALL runs?  Be specific in your answer.
- In the optimized version of the assembly code for setting the global variable `c` and unlocking the memory, the unlocking happens before `c` is written to. This means that the memory becomes available to other threads before the current thread is actually finished with it.

**1.2:** Consider again this lock code:
Is there a possible race condition in here?  
``` C
inline void lock(lock_variable_type *lock_variable)  
{ asm volatile (""::: "memory");  
while (*lock_variable);  
 *lock_variable = 1;  
}
```
Explain where it is.  You should use the assembled version of the call to lock provided earlier in this writeup as part of your explanation.
- Yes, there is a race condition. Setting the lock variable to 1 is not an atomic operation meaning that changes to the critical section could occur when assignment is happening. The assembly code for variable assignment is split into multiple commands that could be interleaved improperly.

**1.3**: Assume you are multi-threading your code and multiple threads are making calls to the given lock() code and accessing the same lock.  Would you consider this implementation wasteful of CPU?  Why or why not?
- Yes, cpu cycles are being executed that aren't doing anything inside the busy while loop. Normally, the thread should be put to sleep instead of executing busy cycles. Additionally, the lock has no mechanisms for ensuring that every thread will eventually get run meaning that if multiple threads are waiting, then some may never actually execute.
  
**1.4**: Assume you are multi-tasking your code and multiple threads were being multitasked on a SINGLE CPU.  Is the given code for lock() dangerous in a way different than introducing a race condition?  If so, how so.  Explain your answer.
- Another big issue with spin locks is due to preemptive scheduling. Say there's multiple threads waiting for access to the critical section, but there's one thread that's currently got the lock. If the thread with the lock gets preempted by another higher priority process, then the access to the critical section is completely blocked and the spinning threads are wasting CPU cycles as well as potentially never actually completing.

**What happens when there’s MANY reader threads? (20 points):** Use the Unix\Linux time command to determine how long (in wall clock, real time) it takes for the sample program to finish when setting the reader (checksum) thread count to 1, 10, and 20.  You may or may not see a trend in how long it takes the process to complete for different numbers of reader threads.  In your own words, explain WHY you are seeing what you are seeing.  Note that what you see may very well depend on HOW you implement the reader-writer lock primitives.  Provide your observations in written form, with any graphs or diagrams you consider appropriate, as a separate PDF file turned in with your source code.
- From what I experienced, past about 10 threads, there was a more significant slow down. This is due to the fact that there are more `checksum` threads to interrupt the `put_item_in_array` thread. When there's only 1 checksum thread, the CPU only has to share time between that and the filler thread. But when you increase the number of threads running the checksum, then you increase the probability that the write lock is going to get activated as the thread sums up the values thus causing the other thread to wait. Since everything waits on the filler thread, more interrupts from the checksum thread mean that the overall time to complete the task will increase. Below is a screenshot of running the program at 1, 10, and 20 seconds respectively. But really, the time is somewhat non-deterministic due to the fact that it relies on the scheduler.
  ![[Pasted image 20250331170036.png]]