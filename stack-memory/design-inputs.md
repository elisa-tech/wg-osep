# Sources of design information for Stack Memory topic

## Kernel design documents

### Kernel stacks
https://www.kernel.org/doc/html/latest/x86/kernel-stacks.html

* This document is specific to the x86_64 architecture
* The kernel has a stack for every active thread
    - Size of stack is THREAD_SIZE (2*PAGE_SIZE)
    - While thread is in user space, the kernel stack is empty except for the
      `thread_info` structure at the bottom
* Kernel also has specialized stacks associated with each CPU
    - Only used while the kernel is in control on that CPU
    - When a CPU returns to user space, these stacks contain no useful data
* The only specialised stack listed is the **Interrupt Stack**:
    - Size is IRQ_STACK_SIZE
    - Used for external hardware interrupts
    - Also used when processing a softirq
    - Switching done by software based on a per CPU interrupt nest counter
* Kernel for x86_64 also has **Interrupt Stack Table (IST)**
    - Allow kernel to automatically switch to a new stack for designated events
      such as double fault or NMI
    - Supported ISTs are:
      - Double Fault Exception
        - Invoked when handling one exception causes another exception
      - Non-maskable interrupts (NMI)
        - NMI can be delivered at any time, including when the kernel is in the
          middle of switching stacks.
      - Debug interrupts
        - Used for hardware debug interrupts (interrupt 1) and for software
          debug interrupts (INT3)
      - Machine Check Exception
        - MCE can be delivered at any time, including when the kernel is in the
          middle of switching stacks.

## External summaries / descriptions

### Kernel and User Space Stack
https://www.baeldung.com/linux/kernel-stack-and-user-space-stack

* Stack grows downward to lower addresses, whereas dynamic allocations (heap)
  grow upwards to higher addresses
* Stack in this context means 'call stack'
    - Information about the active subroutines of a computer program
    - Within a call stack, we can find a series of stack frames
    - A stack frame consists of data relevant to a particular function
    - It usually includes function arguments, return address, and local data  
* The kernel stack is part of the kernel space
    - When a user process uses a syscall, the CPU mode switches to kernel mode
    - During the syscall, the kernel stack of the running process is used
* Size of the kernel stack is configured during compilation and remains fixed
* Unlike the kernel stack, we can change the size of the userspace stack
    - using [setrlimit](https://man7.org/linux/man-pages/man2/setrlimit.2.html)

## Processes and threads
https://www.baeldung.com/linux/process-vs-thread

## Stack protection mechanisms

See [LFSCS notes](https://github.com/elisa-tech/wg-lfscs/blob/main/Kernel%20configurations%20for%20stack%20memory%20protection.md)
