Each process provides the resources needed to execute a program.   
A process has

- a virtual address space,
- executable code,
- open handles to system objects,
- a security context,
- a unique process identifier (pid),
- environment variables,
- a priority class,
- minimum and maximum working set sizes,
- and at least one thread of execution.

Each process is started with a single thread, often called the primary thread, but can create additional threads from
any of its threads.

A thread (lightweight process) is the entity within a process that can be scheduled for execution.
All threads of a process share its virtual address space and system resources.  
Each thread:

- maintains exception handlers,
- a scheduling priority,
- thread local storage,
- a unique thread identifier,
- and a set of structures the system will use to save the thread context until it is scheduled
- The thread context includes the thread's set of machine registers, the kernel stack, a thread environment block, and a
  user stack in the address space of the thread's process.

On a multiprocessor computer, the system can simultaneously execute as many threads as there are processors on the
computer.

### Context Switches

The scheduler maintains a queue of executable threads for each priority level. These are known as ready threads. When a
processor becomes available, the system performs a context switch. The steps in a context switch are:

- Save the context of the thread that just finished executing.
- Place the thread that just finished executing at the end of the queue for its priority.
- Find the highest priority queue that contains ready threads.
- Remove the thread at the head of the queue, load its context, and execute it.


### Terminating a Thread
Terminating a thread has the following results:

    Any resources owned by the thread, such as windows and hooks, are freed.
    The thread exit code is set.
    The thread object is signaled.
    If the thread is the only active thread in the process, the process is terminated. For more information, see Terminating a Process.


The MySQL Server (mysqld) executes as a single OS process, with multiple threads executing concurrent activities.
