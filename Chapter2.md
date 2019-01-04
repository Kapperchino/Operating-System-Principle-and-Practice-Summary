# The Kernel Abstarction
## The Process Abstaction
### What is a Process?
Abstraction of a program with it's own memory running in the os.
### How does a program run?
OS copies the instsruction from the executable image into memeory. Then sets aside a memory region called the excecution stack, to hold variables during procedure calls. A heap is also allotcated.
### Executable image
Machine instructions stored in a file.
### Running muiltiple copies of the same program
Sotre a single copy of a program's instructions, but seperate copies of data, stack and heap.
### Difference between process and programs
A process is an instance of a program. Each program can have zero or more processes
### Process control block
Or PCB, it stores all the information of OS needs about a process.
## Dual-mode operation
Single bit in the processer status register that represents the current mode of the processor. There are two modes, user mode and kernal mode.
### User mode
The processor checks for each instruction to make sure nothing stupid happens.
### Kernal mode
The protections are off and every instrctions are trusted.
## Privlieged Instructions
Instructions are are avaliable in kernal mode but not in user mode. For example: changing privilege levels, adjeusting memory access and such.
## Memory Protection
To make memory shareing safe, the OS need to configure the hardware so that each application processes can only read and write on its own memeory.


