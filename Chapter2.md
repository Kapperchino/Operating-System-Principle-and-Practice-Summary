# The Process Abstraction
### What is a Process?
Abstraction of a program with its own memory running in the os.
### How does a program run?
OS copies the instruction from the executable image into memory. Then sets aside a memory region called the execution stack, to hold variables during procedure calls. A heap is also allocated.
### Executable image
Machine instructions stored in a file.
### Running multiple copies of the same program
Store a single copy of a program's instructions, but separate copies of data, stack and heap.
### Difference between process and programs
A process is an instance of a program. Each program can have zero or more processes
### Process control block
Or PCB, it stores all the information of OS needs about a process.
# Dual-mode operation
Single bit in the processor status register that represents the current mode of the processor. There are two modes, user mode and kernel mode.
### User mode
The processor checks for each instruction to make sure nothing stupid happens.
### Kernel mode
The protections are off and every instructions are trusted.
## Privileged Instructions
Instructions are are available in kernel mode but not in user mode. For example: changing privilege levels, adjusting memory access and such.
## Memory Protection
To make memory shareing safe, the OS need to configure the hardware so that each application processes can only read and write on its own memeory.
### Base and Bound memory protection
In this approach, a processor has two extra registers, called base and bound. The base specifies the start of the process's memeory region and bound specifies the end of the memeory region. When the process tries to access memeory outside of the base and bounds, the hardware raises an exception. The base and bound can only be changed by privledged instructions.
### Base and Bound memeory protection shortcomings
Doesn't have expandable heap and stack, cannot share memory with other process, uses physical memeory instead of virtual memeory and has a lot of fragmentation.
### Virtual Address
The hardware translate virtual addresses to physical memory locations, which allows each process's memeory to start at the same place.
## Timer Interrupts
The Operating system needs a way to regain control from other processes, as the processes will have almost full control over the hardware. So in situations like an infinite loop, the OS can get control back from the process.
### Hardware timer
Interrupts the processor after a specified delay, each processor has one hardware timer. When the timer intrrupts occurs, the hardware transfer control to the kernel from the user process. 
# Types of Mode Transfer
How to safly transition from user more to kernel mode, or vise versa?
## User to Kernal Mode
There are three reasons for the kernel to take control from a user process: Interrupts, processor exceptions and system calls. Interrupts are asynchronously.
### Trap
Synchronous transfer of control from user mode to the kernel.
### Interrupts
Asynchronous signal to the processsor that some external event has occurred. The processor checks for interrupts when it excecutes instructions. If there are intrrupts, the processor completes or stalls the current instruction and saves the current execution state. The processor also starts executing at a specially designed interrupt handler in the kernal. On a muiiltiprocessor, the intrrupts only taken on one of the processors and the rest of the processor continues wihout the influence of the interrupt.
### Polling
Alternative to interrupts. The kernel loops and checks for any event that occurred from input/output devices that needs handling.
### Processor Exceptions
A hardware event caused by user program behavior that caused a transfer of control to the kernel. Examples inlcudes accessing memeory outside of itself, dividing by zero and such. Saves the current execution state like interrupts.
### System calls
Any procedure provided by the kernel that can be called from user level. Basically the user asking the kernel to do something. Ususally implemented with a sepcial trap or syscall instruction.  Examples of system calls include connecting to a web server, creating or deleting files, reading or writing data into files and creating a new process.
## Kernal to User Mode
To make memory sharing safe, the OS need to configure the hardware so that each application processes can only read and write on its own memory.
### Base and Bound memory protection
In this approach, a processor has two extra registers, called base and bound. The base specifies the start of the process's memory region and bound specifies the end of the memory region. When the process tries to access memory outside of the base and bounds, the hardware raises an exception. The base and bound can only be changed by privileged instructions.
### Base and Bound memory protection shortcomings
Doesn't have expandable heap and stack, cannot share memory with other process, uses physical memory instead of virtual memory and has a lot of fragmentation.
### Virtual Address
The hardware translate virtual addresses to physical memory locations, which allows each process's memory to start at the same place.
## Timer Interrupts
The Operating system needs a way to regain control from other processes, as the processes will have almost full control over the hardware. So in situations like an infinite loop, the OS can get control back from the process.
### Hardware timer
Interrupts the processor after a specified delay, each processor has one hardware timer. When the timer interrupts occurs, the hardware transfer control to the kernel from the user process. 
# Types of Mode Transfer
How to safely transition from user mode to kernel mode, or vise versa?
## User to Kernel Mode
There are three reasons for the kernel to take control from a user process: Interrupts, processor exceptions and system calls. Interrupts are asynchronous.
### Trap
Synchronous transfer of control from user mode to the kernel.
### Interrupts
Asynchronous signal to the processor that some external event has occurred. The processor checks for interrupts when it executes instructions. If there are interrupts, the processor completes or stalls the current instruction and saves the current execution state. The processor also starts executing at a specially designed interrupt handler in the kernel. On a multiprocessor, the interrupts only taken on one of the processors and the rest of the processor continues without the influence of the interrupt.
### Polling
Alternative to interrupts. The kernel loops and checks for any event that occurred from input/output devices that needs handling.
### Processor Exceptions
A hardware event caused by user program behavior that caused a transfer of control to the kernel. Examples includes accessing memory outside of itself, dividing by zero and such. Saves the current execution state like interrupts.
### System calls
Any procedure provided by the kernel that can be called from user level. Basically the user asking the kernel to do something. Usually implemented with a special trap or syscall instruction.  Examples of system calls include connecting to a web server, creating or deleting files, reading or writing data into files and creating a new process.
## Kernel to User Mode
### New process
The kernel sopies the program into memory, sets the program counter to the first instruction of the process, sets the stack pointer to the base of the user stack, and witches to user mode.
### Resume after interrupt, processor exception or syscall
Resumes execution of the interrupted process by restoring its program counter, register and changes itss mode back to user level.
### Switch to a differnet process
The kernel switches to a differnt process, and saves the old process's state in the process control block. Then resumes a differnt process by loading its state from the process's control block into the processor then switches to user mode.
### User-level upcall
User programs with the ability to receive async notification of events.
# Implementing Safe Mode Transfer
### Switch to a different process
The kernel switches to a different process, and saves the old process's state in the process control block. Then resumes a different process by loading its state from the process control block into the processor then switches to user mode.
### User-level upcall
User programs with the ability to receive async notification of events.
# Implementing Safe Mode Transfer
