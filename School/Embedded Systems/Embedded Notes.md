## 1.1
CPU is equivalent to a processor

A microprocessor is a CPU running on a single chip.

Microcontrollers integrate the following:
- CPU
- Memory (RAM/ROM)
- Parallel Ports
- Serial Ports
- A/D
- TCP/IP Stack
- Comparators
- etc..
### Software Packages Used in this Course
1. MPLAB
2. C18 Compiler
3. [Proteus](https://www.labcenter.com/simulation/)

Microcontroller used for this course: [PIC18F45K22](https://www.microchip.com/en-us/product/pic18f45k22)
### Von Neuman Architecture 
- One memory containing both instructions and data.
- One address bus - a set of wires to specify memory location is to be accessed.
- One data bus 
- One read / write line 
## 2.2
### CPU Architecture
2 Types of CPU architectures.
- Harvard
- Von Neuman
#### Von Neumann Architecture
Communication between memory and the CPU happens over busses. Sample is Freescale 68HC11 so the numbers in parenthesis relate to our processor.

- **Address bus** is a set of wires needed to access a memory location. Bus width depends on processor architecture. (16 bit)
- **Data bus** is a bi-directional bus to communicate between CPU and memory. (8 bit)
- **Read / Write Flag** Specify whether data bus is reading from or writing to memory. (1 bit)
- **Instruction Register** holds the memory location where the instruction to be performed by the processor is held. It is then decoded in the control unit to actually execute the instruction. And **Instruction** consists of an opcode and an operand (which is the thing that the instruction modifies or works with).

### Harvard Architecture
The program memory and data memory is split. This overcomes the Von Neumann bottleneck since now instructions can be fetched independent of the data bus.

- Since the program memory and data memory are in different memory regions, they can be read and written independently. This means there needs to be busses for each memory space. 
