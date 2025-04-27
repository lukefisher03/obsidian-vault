### Definition and Overview of an OS | Chapter 1

**Operating System Goals**: 
- Execute user code that is safe, secure, efficient, and reliable
- Provide users with convenient interface through which to interact with their running code
- Provide interface for users to modify how their running programs interact with one another
The OS allocates resources and controls the executions of other programs. **Systems programming** is leveraging the knowledge of OS systems to create programs with useful effects.

##### Interrupts
When the CPU is interrupted it immediately stops what it's doing and transfers execution to another fixed location. The interrupt architecture must also keep the previous state so it can be reverted to upon the interrupt's completion.
**Interrupts** are software or hardware signals that demand instant attention from the OS. 
**Interrupt Vector** - An array that acts as a lookup table for interrupt service routines (ISR). An interrupt request gives an index and that's used to lookup the memory address of the corresponding ISR. 
**Traps** are when a user program asks the kernel to do something. It's normally an intentional transfer of control. After the kernel routine (trap handler) is completed, execution is returned to the user processes. 
**Faults** are really a type of trap. They appear when exceptions occur and handle issues like divide by zero. 

##### Basic Storage Structure
Memory is divided into two primary types: volatile and nonvolatile memory. Semiconductor circuits can be used in the manufacturing of either. 

##### I/O Structure
**DMA** or direct memory access allows device controllers to have access to main memory without the intervention of the CPU. This allows the CPU to perform other work while the device communicates with main memory. An interrupt from the device controller is used when the transfer of data is completed.

##### Computer System Architecture
A **core** is a component that executes instructions and registers for storing data locally. 
**Multi-processor** systems have more than one single core CPU and will oftentimes share a clock, the computer bus, and other things. Multi processor systems increase *throughput*. But synchronizing them and keeping them working together often takes some extra overhead. 
**Symmetric Multiprocessing** is when each peer CPU performs all tasks, including both OS and user processes. In this configuration each CPU has its own registers and cache, but they all share system memory.
**Multicore systems** are now more popular in consumer electronics. Multicore means that just a single processor has multiple computing cores. This can be more efficient due to faster inter-core communication. It is also more power efficient. 
**Non-uniform memory access (NUMA)** is when all processors share the same physical address space, but each CPU has its own dedicated portion of it to which it's connected by a small local bus. This helps solve the issue of bus contention when all CPUs use the same memory bus. NUMA is only relevant on multiprocessor systems, not multicore systems.

##### OS Operations
The **bootstrap** program is firmware that starts the kernel process. It initializes all aspects of the system. 
**Systemd** is a *daemon* outside the kernel that is constantly running in order to provide services to user programs. systemd is an init system that starts many other processes and performs setup for a variety of different programs.
**Multiprogramming** is when a CPU will always have a program to execute. This means if one program is waiting for something, then the CPU will switch to something else to continue execution. The idea is that there's no idle time.
**Multitasking** is when a CPU rapidly switches between processes to give the illusion of parallel execution, when in reality it's just concurrent execution governed by a scheduler. 

### Operating System Structures | Chapter 2
##### System Calls
**System calls** provide an interface to the services made available by the operating system. Many operating system utilities (such as `cp` or `ls` ) leverage system calls to perform functions. 

There are many different types of system calls, the main categories are as follows:
1. Process control
2. File management
3. Device management
4. Information maintenance
5. Communications
6. Protections

##### Design Goals
Designing an operating system generally has goals in two main categories, *user goals* and *system goals*. Designing real time embedded operating systems versus large distributed systems both require different user and system goals. RTOS may prioritize system goals over user goals considering speed and efficiency are most important. But a large distributed system may have many users and user friendliness may be paramount. 
##### Mechanisms vs Policies
*Mechanisms* determine how to do something and *policies* determine what will be done. Generally a mechanism should function under multiple different policies. 
##### Operating System Structure
**Monolithic** operating systems are *tightly coupled* meaning that the kernel is generally placed in a single or only a few static binaries and operating in a singular large address space. This can be good for performance due to low overhead, but can be difficult to extend or build upon.

**Layered** approaches give more modularity to the operating system. Generally higher level structures in the OS layers propagate requests to lower level structures to get things done. Privilege and protection is implemented easier in this type of approach. But some overhead cost is incurred due to synchronization and communication. 
##### System Boot
The boot sequence is as follows:
1. Bootstrap program loads the kernel
2. Kernel is loaded into memory and started
3. Kernel initializes hardware
4. Root file system is mounted
##### Operating System Debugging
A **Core dump** captures the memory of a process and store it for later analysis. This is useful for tracing errors in programs and finding out what happened in harder to understand situations. 

### Processes | Chapter 3
##### Process Concept
A **process** in its simplest form is a program in execution. It is the unit of work in a modern computing system. 

The **program counter** (the register in the CPU that holds the address of the current instruction being executed) and CPU registers represents the status of the current activity of a process. 

The memory map of a process is shown below
![[Pasted image 20250425122621.png]]
**Stack** - Temporary storage when invoking functions. Function parameters, return addresses, and local variables are stored here.
**Heap** - Memory that is dynamically allocated during runtime.
**Data section** - Global and static variables are stored here.
**Text section** - The executable code.

An **activation record** is pushed to the stack when a function is called and contains the parameters, local variables, and return addresses. Once the function is finished, the activation record is popped. 

The stack and heap grow towards each other, but the OS must ensure they never overlap. 

A process is an example of an *active entity* whereas a program is a *passive entity*.

Processes have multiple different **states** that they can be in. 
- New - Process is being created.
- Running - Instructions are being executed.
- Waiting - The process is waiting for some event to occur.
- Ready - The process is waiting to be assigned to a processor.
- Terminated - Process finished.

A **process control block** stores all of the data required for a process to start or restart a process. The data inside a process control block is as follows:
![[Pasted image 20250425125753.png]]
**Process state** - See above for different states
**Process number** - Indicates the next address of the instruction to be executed for this process
**Registers** - CPU register data so that the process can be restarted exactly where it was before after an interrupt or some other event.
**Memory management information** - Information required by the task scheduler
Accounting information. This information includes the amount of CPU and real time used, time limits, account numbers, job or process numbers, and so on.
**I/O status information**. This information includes the list of I/O devices allocated to the process, a list of open files, and so on.

##### Process Scheduling
The **process scheduler** algorithmically selects an available process to execute on the CPU.

The number of processes currently in memory is known as the **degree of multiprogramming**. 

Processes are stored in a **ready queue**, which is a linked list that stores pointers to different process' process control blocks. Processes that are waiting for a certain event to occur are placed in a **wait queue**. Generally there's 3 types of wait queues. 
1. I/O wait queue - I/O event occurred. 
2. Child wait queue - Parent process created a child, has to wait for child to terminate
3. Interrupt wait queue - Process got interrupted 
After being in a wait queue it is then put back into the ready queue. 

**Swapping** is used when memory space is tight. A process' memory is moved from RAM onto disk temporarily. The process control block still remains in RAM so it can be swapped back in, but it essentially frees up main memory. This is slow especially for systems with mechanical drives as secondary memory and should only be utilized when the RAM is full. 

Switching a CPU core from executing one process to another requires a **context switch**. This involves saving and loading states from process control blocks. Context switches are pure overhead and should be minimized.
##### Process Creation
A **PID** is a process identifier that is normally an integer number. 

When creating a child process using `fork()` the child process receives a copy of the parent's address space. Calling `exec()` in a child process will replace the process' memory space with a new program. 

A **zombie** process occurs when a child process has been terminated but its parent hasn't called `wait()` on it yet. This leads to the child process still existing in the process table. Processes are **orphans** when their parent process gets terminated and doesn't call `wait()`. This leaves the child processes behind, normally they are adopted by the init process. In certain scenarios child processes may not be allowed to exist without the parent and when a parent is terminated all of the child processes also get terminated. This is called **cascading termination**. 

##### Interprocess Communication
Interprocess communication is broken into two categories: **shared memory** and **message passing**.

**Shared memory** requires a process to create a shared memory segment that another process can attach itself to. Then since both processes have access to the shared memory segment, they can read or write back and forth though it. Mechanisms such as mutexes and semaphores may help with synchronization.

**Message passing** uses mechanisms within the kernel to automatically synchronize messages between two processes. The `send()` and `receive()` system calls are generally used. The downside is that it's much slower than shared memory and incurs additional kernel overhead. 

**Pipes** are a common (and old) method of message passing IPC. 

***Ordinary pipes*** are unidirectional lines of communication between processes. They have a read and a write end. You can implement bi-directional communication with ordinary pipes using 2 of them facing opposite directions. In UNIX pipes are treated as a type of file, so they can be accessed via the `read` and `write` system calls. Since child processes inherit the file descriptors of their parents, pipes are easily setup between parent and forked processes. When communicating over pipes it is essential to close whichever end of the pipe you're not using. Closing both ends of the pipe is essential for the reading process to know when the writing process is done.

**Named pipes** are different from ordinary pipes in that they allow bidirectional communication at half duplex. Additionally, a parent-child relationship between processes isn't required either. They also don't terminate after the end of the communicating processes. In fact, more than 2 processes can communicate through named pipes. They are also called `FIFOs`.

**Sockets** allow for inter-machine communication. Essentially it uses networks to communicate between machines. The most common protocols for sockets are TCP/UDP.

### Threads and Concurrency | Chapter 4
A **Thread** is a basic unit of CPU utilization. Each thread has their own:
- Thread ID
- Program counter
- Register Set
- Stack
All threads in a process share:
- Code section
- Data section
- Heap
- Other OS resources such as open files and signals.
A traditional process has a *single* thread of control.

Multithreaded applications have 4 primary benefits
1. Responsiveness - With the creation of new threads, the user experience on an application can be without delays as work is either given to other threads while the main thread continues to listen for user input or situations like that. 
2. Resource Sharing - Threads by default share memory within a process, so IPC and other mechanisms can be bypassed for more direct communication.
3. Economy - Creating new processes is costly, creating new threads is less so due to no context switching. 
4. Scalability - Technically, different threads could run on different cores resulting in true parallel execution.

If there's only a single computational core then threads get switched via concurrency. But if there's multiple cores, then there's a possibility for both *concurrency* and *parallelism*. 

**Data parallelism** is splitting data among different programming cores and all of them running the same task. This is different from **task parallelism** which focuses on different cores doing different tasks that all work together to run the program.

**User threads** are threads created by a user process and are managed without the intervention of the kernel through a library. **Kernel threads** are the actual threads that the operating system cares about. As such, when we create user threads, they have to map to different kernel threads in order to be executed by the operating system. So there's different mappings between user and kernel threads.

**Thread Mappings**
**One to One** - A single user thread is mapped to a single kernel thread. This allows for the greatest possibility of true parallelism if the kernel threads get scheduled on different cores. ***The linux kernel uses this method of thread mapping*.**
**Many to One** - Multiple user threads are mapped to a single kernel thread. If the kernel thread is executing, then the user threads are running concurrently. However, if the kernel thread is blocked, all user threads belonging to the kernel thread are also blocked. 
**Many to Many** - This allows multiple kernel threads to own multiple user threads. The user threads are multiplexed to a smaller number of kernel threads. This makes threads fast to create and still provides ample opportunity for true parallelism.

**Implicit Threading** is when thread creation and management is done by compilers or runtime libraries. 
A **Thread pool** is a strategy of implicit threading where a *pool* of threads is held. As the process needs more threads, they can grab them from the pool and then put them back when a task is finished. This prevents unlimited threads from being created, but still provides a noticeable performance boost.

**Thread cancellation** is when a thread is terminated before it's complete. This can be done via **asynchronous cancellation** where the thread is terminated immediately or by **deferred cancellation** where the target thread periodically checks whether it should terminate, allowing it to terminate itself in a more orderly fashion. Asynchronous cancellation can be tricky when the target thread is in the middle of updating something or modifying data and it gets interrupted in the middle of that process. This can cause corrupted data which is why deferred cancellation can sometimes be better. If a single thread in a process calls `exit()` ALL THREADS ARE CANCELLED AND THE PROCESS IS TERMINATED. This differs from multiprocessing using something like `fork()`. If a single thread calls `exec()` the ENTIRE PROCESS IS REPLACED AND ALL THREADS ARE CANCELLED.

**Thread local storage** or TLS is data that is specific to a thread that can be utilized over multiple function calls. Generally the threading library you're using to create and manage the threads provides this mechanism. 

### CPU Scheduling | Chapter 5
CPU scheduling decisions may take place under the 4 following circumstances:
1. When a process switches from the running to the waiting state. An example would be the process waiting for I/O. 
2. When a process switches from the running state to the ready state. For example when an  interrupt occurs.
3. When a process switches from the waiting state to the ready state.
4. When a process terminates.

When scheduling only happens if a processes switches from running to waiting (circumstance 1) or when a process terminates (circumstance 4) then we call that scheduling scheme **cooperative** or **non-preemptive**. Otherwise, the scheduler is **preemptive**. Most operating systems use preemptive scheduling.

The **Dispatcher** is the module that gives control to the process selected by the scheduler. The dispatcher first performs a context switch, then it enables user mode, and finally it jumps to the proper place in memory for the given user program to resume execution. The time it takes to perform these steps is referred to as **dispatch latency**.

##### Scheduling Criteria
There are a variety of different variables that come into play when deciding which processes should be scheduled and when.

1. CPU utilization is a big factor. The concept of multiprogramming is that there's always a process that's being run, the CPU should never be idle.
2. Throughput, which is the number of processes being completed per unit of time.
3. Turnaround time, which is the time it takes to complete a process.
4. Waiting time, how long has a process been in the ready queue?
5. Response time is similar to waiting time, except it only counts the time in the queue to *first* execution. All subsequent waits are not apart of the calculation. On systems with a lot of I/O this generally measures efficiency more accurately.
##### Scheduling Algorithms
**First come, first serve** is the simplest algorithm. It works exactly as the name describes it, the first processes to enter the ready queue are the first ones to be executed. This is essentially just a FIFO queue. The issue with this algorithm is the waiting time. Depending on the order of processes entering the ready queue, the average waiting time fluctuates quite drastically. This formalized as the **convoy effect**. Essentially, a lot of smaller processes have to wait for a bigger process to get off the CPU. It may be more efficient to run the smaller processes multiple times if their I/O wait time is much shorter before running the bigger processes again. This is a nonpreemptive scheduling algorithm.

**Shortest job first** (SJF) scheduling will run the process with the shortest CPU burst first. This is optimal, however unachievable due to the fact that we don't know how long a given process' CPU burst will be. So, this method involves estimating the length of the burst using previous bursts. We take an exponential average and use that to predict how long the next burst will be. The SJF algorithm can either be preemptive or cooperative. The preemptive version can be thought of more as *shortest remaining time first*. This will essentially preempt a currently running process if there's another process that's predicted burst time is shorter than the remaining time left on execution.

**Round robin scheduling** is similar to FCFS scheduling, but it adds preemption. Each process is given a *time slice* (also called *time quantum*) of allowed execution time. Then the scheduler just goes around in the queue and gives each process equal time on the CPU. The performance of the algorithm depends on how large the time slice is. Too large and it just becomes FCFS, but too short and there's too many context switches for the algorithm to really be efficient. For general purpose programming, this is pretty efficient if the time slice is a bit larger than the time it takes to context switch. A rule of thumb is that 80% of bursts should be less than the time slice.

The SJF algorithm is a type of **priority scheduling**. Priority scheduling involves assigning each process a priority and then executing them according to some policy. A major issue with priority based scheduling is **starvation**. Starvation occurs when a lower priority process never gets executed due to constant preemption from higher priority processes. A solution to this issue is **aging**. This means that as processes age, their priority increases. Additionally, a round robin approach can be added to aging. You could execute highest priority tasks first, but then execute tasks of the same priority in a round robin fashion. This increases the *fairness* of the scheduler. 

**Multilevel Queue Scheduling** is often a better approach to priority scheduling. A single queue can be used to implement a priority based scheduler, but searching for the correct task can take up to *O(n)* time which is unacceptable. A better idea is to have a dictionary with each priority level. A bitmap or priority queue can be used to locate the highest priority efficiently. A **multilevel feedback queue** allows for the processes to switch between different queues depending on the characteristics of their CPU bursts.

**Foreground** processes are interactive and **background** processes are batch.

**Symmetric multiprocessing** is when each processor will self schedule. Each processor (or core) will examine the ready queue and find an available process to run. Sometimes the processors will share a ready queue, sometimes they'll have their own. It is more common to have each processor own individual ready queues as it optimizes the uses of caches. 

**Multicore** processors have individual cores that look like different processors to the computer. However, each core normally has more than one hardware thread. The hardware threads can execute in parallel due to the fact that memory accesses are slow and the core can fill in the gaps by executing its other thread. It's a bit like parallelism, but not exactly like it. Each hardware thread looks like a CPU to the computer. So you could have 4 cores and 2 hardware threads per core. That would mean the OS would see 8 CPUs.

**Load balancing** is distributing the tasks to all of the cores (and hardware threads) evenly. There are 2 methods for load balancing: push migration and pull migration. Push migration is when a specific task checks the load of each processor and will push new tasks to underutilized cores. Pull migration is when an idle task pulls a task from another processor's ready queue. Often times both push and pull migration are implemented together. 

**Processor affinity** is an important factor when it comes to load balancing. When a thread has been running on a specific processor, all of that processor's cache have the data from that thread. But if the thread migrates to another processor, then the memory accesses will take much longer as the new processor's cache doesn't have any data relating to the thread. This is an issue of *cache locality*. This is issue is most pertinent on systems with a shared ready queue. In many operating systems you can set processor affinity for a given task. A soft affinity means that the processor will try to keep the same thread but it isn't guaranteed, hard affinity guarantees that a process stays on the same core.

**Heterogenous multiprocessing** is when a system has different types of CPU cores in it. Generally there are different power saving cores and power efficiency is now a factor into load balancing and scheduling between cores. It's not **asymmetric multiprocessing** because all cores will still run the same instruction set. 
### Synchronization Tools | Chapter 6
A **cooperating process** is one that can affect or be affected by other processes. 

**Race conditions** occur when two unsynchronized processes rely on or attempt to modify shared data which then becomes stale or unusable due to the order in which the processes accessed or modified the data.

The **critical section** is an area of code in which a process may be accessing and updating code that is shared with at least one other process. The idea is that when one process is executing in its critical section, no other process is allowed to execute their critical section. A protocol is needed in order to manage entry to critical sections. Each process has to make a request (**entry section**) to enter and then signal an exit (**exit section**) the critical section. The code after the request to the critical section is the **remainder section**. A protocol implementing this must have these two properties:
1. Mutual exclusion - No two processes running their critical sections simultaneously.
2. Progress - The selection of processes to execute in their critical sections must be done by processes that aren't yet in their remainder sections. 
3. Bounded waiting - A process is guaranteed that at some point it's request to enter its critical section will be approved.

### Main Memory | Chapter 9
##### Basic Hardware
The CPU is fast enough that memory accesses to the main memory will become a bottleneck and leave the CPU doing nothing. In order to fix this, we have caches which store relevant process data to prevent having to fetch from main memory. 

Each process needs to have separate memory from each other to avoid corruption. One method of doing this is having a base register and a limit register. The **base register** or **relocation register** holds the lowest legal memory address available and the limit supplies the width of the new address space. Within hardware we can then trigger an interrupt if memory access outside of that range is attempted for a given process. 

**Binding** is the process of connecting symbols (variables such as `count`) to absolute addresses. There is normally an intermediate step where the symbols are given offsets, such as 10 bytes from the start of program memory and then eventually it's assigned an absolute address. Binding can technically occur in any stage in building and running a program.
- Compile time - If at compile time it is known where the program will reside in memory, then you can generate **absolute code**. If the location changed, you would need to recompile the code.
- Load time - If at compile time the memory address of your program is unknown, then the compiler generates **relocatable code**. 
- Execution time - If the process can be moved during its execution from one place in memory to another, then binding has to be delayed until runtime. Most computers use this scheme.

A **logical address** (or virtual address) is an address that is generated by the CPU and then must be translated by the memory management unit (**MMU**) to the **physical address** which is a real place in memory. The program is only aware of the logical addresses as each program believes that they're the only program running. Virtual addresses live in **virtual address space** and physical addresses live in **physical address space**

**Dynamic loading** is when only the currently utilized routines are in main memory. If another routine is needed, then it can be loaded. This allows larger programs to run as the size of main memory is no longer a bottleneck.

**Static linking** is when a library is literally apart of the program's binary. This differs from **dynamic linking** of **dynamically linked libraries** where the library isn't baked into the binary. Instead the program is linked to the shared/dynamic library. 

##### Contiguous Memory Allocation
Memory is divided into two areas, user and system memory. Normally system resides in higher memory addresses.

In a **variable partition scheme** the operating system keeps a table indicating which parts of memory are available and which are occupied. As programs fill up contiguous slots of the memory, there are holes where new programs can fit. 

When using the variable partition scheme there's a few different methods for matching processes with holes in the memory space.
- **First fit** - Just find the first open memory slot that will fit the process.
- **Best fit** - Find the smallest hole that's big enough to fit the process.
- **Worst fit** - Allocate the process to the biggest hole. Then the remainder of the hole is left for other processes.

Best fit and worst fit suffer from **external fragmentation**. This is when there's enough total memory space for a new process to be loaded into memory, but the space isn't contiguous. **Internal fragmentation** is when there's enough space for a process to fit into a partition, but the process is smaller than the partition leaving unused bytes of memory within the partition. It's internal due to the fact that it's inside the partition.

**Compaction** is a solution to external fragmentation. The downside of compaction is that it's expensive. The idea is to move all used memory to one side and all holes to another creating one big hole. However, only processes that have been relocated (translating virtual addresses to physical) dynamically at runtime. Once again, the cost on this is generally very high and not worth it.

##### Paging
Fragmentation (both internal and external) are major problems when it comes memory allocation and efficiency. What if we could fix this problem by storing programs in a noncontiguous fashion. This method is called **paging** .

**Paging** works by breaking down physical memory into blocks called **frames** and breaking virtual memory into **pages**. When a process has to be executed its pages are loaded into any available frames. The pages are stored in fixed size blocks that are the same size as frames or multiples of the frame size.

Every logical address generated by the CPU is divided into two parts. A page number and a page offset. The page number is used to index the page table and the page offset is to define the physical memory address. 