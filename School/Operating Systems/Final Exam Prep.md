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

In a **variable partition scheme** the operating system keeps a table indicating which parts of memory are available and which are occupied. 