# Sub Chapters
- [The Process Abstraction](#the-process-abstraction)
- [Duel-mode Operation](#dual-mode-operation)
- [Types of Mode Transfer](#types-of-mode-transfer)
- [Implementing Safe Mode Transfer](#implementing-safe-mode-transfer)
- [x86 Mode Transfer](#x86-mode-transfer)
- [Implementing Secure System Calls](#implementing-secure-system-calls)
- [Starting a new process](#starting-a-new-process)
- [Implementing Upcalls](#implementing-upcalls)
- [Case Study: Booting an Operating System](#case-study-booting-an-operating-system)
- [Case Study: Virtual Machines](#case-study-virtual-machines)
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
To make memory shareing safe, the OS need to configure the hardware so that each application processes can only read and write on its own memory.
### Base and Bound memory protection
In this approach, a processor has two extra registers, called base and bound. The base specifies the start of the process's memory region and bound specifies the end of the memory region. When the process tries to access memory outside of the base and bounds, the hardware raises an exception. The base and bound can only be changed by privledged instructions.
### Base and Bound memory protection shortcomings
Doesn't have expandable heap and stack, cannot share memory with other process, uses physical memory instead of virtual memory and has a lot of fragmentation.
### Virtual Address
The hardware translate virtual addresses to physical memory locations, which allows each process's memory to start at the same place.
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
A hardware event caused by user program behavior that caused a transfer of control to the kernel. Examples inlcudes accessing memory outside of itself, dividing by zero and such. Saves the current execution state like interrupts.
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
The context switch needs to implement the following.
### Limited entry into the kernel
Must ensure entry point into the kernel is one set up by the kernel.
### Atomic changes to processor state
The mode, program counter, stack, and memory protection should all be changed at the same time.
### Transparent, restartable execution
The operating must be able to restore the state of the user program exactly as it was before the interruption.
## Interrupt Vector Table
The OS must take differnt actions depening on the interrupt, how does the processor know what code to run?
![alt text](https://www.andrew.cmu.edu/course/15-412/ln/inttable.jpg "Interrupt Vector Table")
### Interrupt Vector Table
An array of pointers, with each entry pointing to the frist instruction of a differnt handler procedure in the kernel.
### Interrupt Handler
Procedure called by the kernel on an interrupt.
## Interrupt Stack
Where should the interrupted process's state be store in?
### Interrupt stack
A priviledged hardware register pointer to a regoin of kernel memeory. When an interrupt,processor exception or system call trap causes a contexct switch into the kernel, the hardware changes the stack pointer to point to the base of the kernel interrupt stack. Also saves the interrupt process's registers by pushign them onto the interrupt stack before calling the handler.</br>
When the handler runs, it pushes any remianing registers onto the stack before performing its work. After it is done, the handler pops the saved registers, then the hardware restores the registers it saved, returning to the point where the process was interrupted.
### Reasons for the interrupt handlers to run on a kernel stack
#### Reliability
The process's user-level stack can be an invalid memeory address, but the handler has to work properly.
#### Security
User program's stack could be modified by other threads.
## Two Stack per process
In most operating systemns, a process has two stacks, one for user code and one for kernel code.
### Process running in user mode
its kernel stack is empty and ready to be used for interrupts, processor exceptions and system calls.
### Process running in kernel mode
its kernel stack is in use, containing the saves registers from the interrupted user-level computation as well as the current state of the kernel handler.
### Process waiting for its turn
its kernel stack contains the registers and state to be restored when the process is resumed.
### Process waiting for io events to complete
its kernel stack ocntains the suspended computations to be resumed when the io finishes.
## Interrupt Masking
Can an interrupt happen while the kernel is already handling an interrupt?
### Interrupt disable
Temporarily defer(mask) delivery of an interrupt until it's safe to do so. 
### Interrupt enable
Pending interrupts are delivered to the processor.
## Hardware Support for Saving and Restoring Registers
### x86 interrupt process
- x86 pushes interrupted process stack pointer onto the kernel's interrupt stack and switches to the kernel stack.
- x86 pushes the interrupted process's intstruction pointer.
- x86 pushes the x86 processor status word.</br>
The hardware saves the values for the processor state word before jumping through the interrupt vector table to the interrupt handler.
### x86 features for restoring state
- popad: instruction to pop an array of integer register values off the stack into the registers and an iret(return from interrupt) instruction that loads a processor state.
- pushad: instruction to save the remianing register on to the stack.
# x86 Mode Transfer
## When user-level process is running
![alt text](/Figures/Chapter2/Figure_2-5-1.png "user-level process diagram")
## When a processor exception or system call trap occures
![alt text](/Figures/Chapter2/Figure_2-5-2.png "interrupt diagram")

The hardware saves a small amount of the interrupted thread state
1. **Mask Interrupts**: Starts by preventing any interrupts from occurring while the processor is switching from user mode to kernel mode.
2. **Save three key values**: Saves the value of the stack pointer(rsp and ss registers), the execution flags and the instruction pointer(rip and CS registers).
3. **Switch onto the kernel interrupt stack**: Then switches the stack pointer to the base of the kernel interrupt stack.
4. **Push the three key values onto the new stack**: Then the hardware stores the internally saved values onto the stack.
5. **Optionally save an error code**: Certian typess of exceptions, such as page faults, generates an error code to provide more info about the event. For these exceptions, the hardware pushes this code, making it the top item of the stack. For other types of events, the software pushes a dummy value onto the stack instead of the error code.
6. **Invoke the interrupt handler**: Finally, the hardware changes the code segment/program counter to the address of the interrupt handler procedure.A special register in the processor contains the location of the interrupt vector table in kernel memory. This register can only be modified by ther kernel.
## When The handler software starts
![alt text](/Figures/Chapter2/Figure_2-5-3.png "handler diagram")
1. **Saves the other registers**: The handler pushes the rest of the registers, including the current stack pointer using pushad instruction.
2. **Handler completes**: Once the handler completes, it can resume the interrupted process. The handler pops the registers it saved on the stack. This restores all the register except the execution flags, program counter, and stack pointer. Uses the popad instruction. Then the handler executes the x86 iret instruction to restore the code segment, program counter, execution flags, stack segment, and stack pointer from the kernel's interrupt stack. This restroes the process to what is was before the interrupt.
### How the hardware prevents infinite intrrupts
When the hardware takes an exception to emulate an instruction in the kernel. If the handler returns back the instruction that caused the exception. Another exception would instantly recur. To prevent the infinite loop, the exception handler modifies the program counter stored at the base on the stack to point to the instruction after the one causing the mode switch. The iret instruction can then return to the user process at the correct location. 
# Implementing Secure System Calls
The OS kernel constructs a restricted enviorment for process execution to limit the impact of malicous programs. Everytime a process needs to an action outside of its protection domain, it must ask the operating system to do it for them.

System Calls provide an illusion that the kernel is simply a set of library avaliable to user programs. System calls, like interrupts and processor exceptions, share the same mechanism for switching between user and kernel mode. The x86 instruction to trap into the kernel on a syscall is called int.

Inside the kernel, a procedure impelemnts each syscall. This procedure behaves as if the call was made from the kernel, but with differences. The kerenel must implemnt it in a way that protects itself from errors and attacks that can be launched by the misuse of the interfafce. The kernel must assume that the parameters passed to a syscall are intentionally designed to be as malicious as possible.
### Pair of Stubs
A pair of procedures that mediate between two enviorments. In this case, between the user enviorment and the kernel. Also mediate procedure calls between computers in a distributed system. 

TODO: Add a diagram
## Steps involved in a system call
1. The user program calls the user stub in the normal way
2. The user stub fills in the code for the syscall and executes the trap instuction
3. The hardware transfer contorl to the kernel, vectoring to the syscall handler. The handler acts as a stub on the kernel side, copying and checking arguments and then calling the kernel implementation of syscall.
4. After the syscall completes, it returns to the handler.
5. The handler returns to user level at the nest instruction in the stub.
6. The stub returns to the caller.

User level stub example:

```assemblymarkdow
open:
    movq #SysCallOpen, %rax
    #Traps into the kernel
    int #TrapCode
    ret
```

The int instruction saved the program counter, stack pointer, and eflags on the kerenel stack before jumping to the syscall handler thought the interrupt vector table. Also saves other registers. Then examins the syscall integer code in %rax, verifies it is a legal opcode, and calls the correct stub for the syscall.

### Four tasks of the kernel stub
- **Locate system call arguments**: The arguments to a syscall are stored in the memory, typically on the user stack. IF the syscall has a pointer argument, the stub mnust check the address to verify it is a legal adress within the user domain. If so, the stub converts it to a physical address.
- **Validate parameters**: The kernel checks for malicious arguments and return an error if it is invalid.
- **Copy before check**: The kernel copies syscall parameter into kernel memory before performing the checks. It is done to prevent the applicationfrom modifying the parameter after the stub checks the value, but before the parameter is used in the actual implementation of the routine. This is called a time of check vs. time of use attack(TOCTOU).
- **Copy back any results**: The stub must copy the result from the kernel into user memeory.
# Starting a new process
```c
int KernelStub_Open()
{
    char* localCopy[MaxFileNameSize+1];
    //checks that the stack pointer is valid, and the arguments are stored at valid addrerss
    if(!validUserAddressRange(userStackPointer,userStackPointer + size of arguments))
        return error_code;
    //Fetch pointer to file name from user stack and convert it to a kernel pointer
    filename = VirtualToKernel(userStackPointer);
    //Make a local copy of the filename, checks each address in the string before use to make sure it is valid
    if(!VirtualToKernelStringCopy(filename,localCopy,MaxFileNameSize))
        return error_code;
    //Make sure the local copy of the file name is null terminated
    localCopy[MaxFileNameSize] = 0;
    //check if the user is permitted to access this file
    if(!UserFileAccessPermitted(localCopy,current_process))
        return error_code;
    //Finally, call the actual routine to open the file.
    return Kernel_Open(localCopy);
}
```
### How to start running a process
- Allocate and initialize the process control block.
- Allocate memory for the process.
- Copy the program from disk into the newly allocated memory.
- Allocate a user-level stack for user-level execution.
- Allocate a kernel-level stack for handling system calls, interrupts and processor exceptions.
- **Copy arguments into user memory**: kernel copies the file name to a special region in the user process, then the aruguments for the process is stored in the base of user stack.
- **Transfer control to user mode**: Most Os reuses the same code to exit the kernel for starting a new process and for returning from a system call.When a newe process is created, we allocate a kernel stack to it, and reserve room at the bottom of the kernel stack for the initial values of the user states.
# Implementing Upcalls
To allow applications to implement operating system-like functionality, we needto virtualize some of the kernel so that applications can behave more like operating systems.
### Upcall
Virtualized interrupts and exceptions. Called signals in UNIX, and asynchronous events in windows.
## Uses for upcalls
- **Premptive user-level threads**: An application may run multple tasks, or threads, in a process. A user-level thread package can use a periodic timer upcall as a trigger to switch tasks, to share the processor more evenly.
- **Async I/O notificaiton**: Most system calls wait til the requestion operation completes then return. 
### Asynchronous I/O
A system call starts the request and returns immediatly, later the application can poll the kernel for I/O completion,or a seperate notification can be sent via an upcall to the application when the I/O completes.
### Interprocesss communication
A kernel upcall is needed if a process generates an event that needs the instant attention of another process. For example, UNIX sends an upcall to notify a process when the drbugger wants to suspend or resume the process.
### User-level exception handling
The OS needs to inform the application when it recieves a processor exception, so the application runtime, rather than the kernel, handles the event.
### User-level resource allocation
The operating system must inform the process when its allocation changes. Example: Java garbage collector.

TODO: Diagrams
## UNIX signals
- **Types of Signals**: In place of hardware-defined interrupts and processor exceptions, the kernel defines a limited number of signal types that a processor can recieve.
- **Handlers**: Each process defines its own handlers for each signal type.
- **Signal stack**: Applications have the option to run UNIX signal handlers on the process's normal execution stack or on a special signal stack allocated by the user process in user memeory.
- **Signal masking**: UNIX defers signals for events that occur while the signal handler for those types of events is in progress. Instead, the signal is delivered once the handler returns to the kernel.
- **Processor state**: The signal handler can also modify the saved state, e.g, so that the kernel resume a differnt user-level task when the handler returns.
# Case Study: Booting an Operating System
When a computer boots, it sets the machine's program counter to start executing at a pre-determined position in memory. Since the computer is not running, the initial machine instructions must be fetched and executed immediatly after the power is turned on before the system has had a chance to initialize its DRAM. Instead the system uses a special read-only hardware memory called boot ROM. On most x86 computers, the boot program is called the BIOS.
### Bootloader
The bios reads a fixed-size block of bytes from a fixed position on disk into memeory. THis block of bytes is called the bootloader.
### Cryptographic signature
A specially designed function of the bytes in a file and a private cryptographic key that allows someone with the corresponding public key to verify that an authorized entity produced the file.
# Case Study: Virtual Machines
How do interrupts, processor exceptions and system calls work in a VM? 
### Host operating system
Operating system providing the VM.
### Guest operating system
The VM OS running inside an OS

TODO: Diagram
## How Does the guest kernel start a process?
- The host loads the guest bootloader from the virtual disk and starts it running.
- THe guest bootloader loads the guest kernel from the virtual disk into the memeory and starts it running.
- The guest kernel then initializes its interrupt vector table to point to the guest interrupt handlers.
- The guest kernel loads a process form the virtual dissk into guest memory.
- To start a process, the guest kernel issues instructions to resume excecution at user level.(Using reti on the x86) Since changing priliege level is a privileged operation, this instruction traps into the host kernel.
- The host kernel simulates the requested mode transfer as if the processor had directly executed. It restores the program counter, stack pointer, and processor status word exactly ass the guest operating system had intended.
## How does the guest OS do system calls?
- The host kernel saves the instruction counter, processor status register, and user stack pointer on the inerrupt stack of the guest operating system.
- The host kernel transfer control to the guest kernel at the beginning of the interrupt handler, but with guest kernel running with user-mode privilege.
- The guest kernel performs the system call
- When the guest kernel attempts to return from the system call back to the user level, this causes a processor exception, dropping back into the host kernel.
- The host kernel can then restore the state of the user process, running at user level, as if the guest OS had been able to return there directly.




