# ARM Reverse Engineering

If you're diving into reverse engineering, chances are you've encountered the Arm assembly language and its significance in analyzing Arm-based binaries. But why does this low-level language even exist when high-level languages like C/C++ are more commonly used? High-level languages are convenient for programmers, but processors can't directly understand them. Instead, these languages are compiled into machine code for execution.

Machine code differs from assembly language and looks cryptic in a text editor. Processors only understand machine code, not assembly. So, why is assembly crucial for reverse engineering? To grasp its role, let's explore the computing history and how assembly fits into the puzzle.

## Bit and Byte

A long time ago, when computers were first made, we needed a way to talk to them because they don't understand human words, only electricity. Computers use electricity, and it can either be on or off. To explain these on and off states, we used a simple system called binary, where each piece (we call it a bit) can be either 0 or 1. When we put bits together, we can show big numbers.

Here's the tricky part: we needed a way to store these numbers in the computer's memory or on tapes and know where one number ends and the next one starts. Back then, this was a big problem. So, people decided to group bits together into bunches with a fixed size, and they called each bunch a "byte."

But here's the interesting part: bytes didn't always have 8 bits like they do today. In the past, different computers used different byte sizes. It started with 4 bits, then went to 6 bits called Binary Coded Decimal Interchange Code (BCDIC). It was only later, with IBM's 8-bit EBCDIC (Extended Binary Coded Decimal Interchange Code), that the 8-bit byte became the usual size.

Why did they choose 8 bits? Well, there were good reasons:

1. 256 characters were enough for most things.
2. Each character could fit in one byte, which made it easy to work with data.
3. 8-bit bytes were a good use of storage space.

An 8-bit byte can show values from 0 to 255. How we understand these values depends on the computer program. They can stand for positive numbers from 0 to 255 or use a method called two's complement to show signed numbers from -128 to 127.

## Character Encoding

Computers didn't only use bytes for numbers; they also stored and worked with readable letters and numbers, known as characters.

Early character systems like ASCII used 7 bits per byte, allowing for 128 characters. This covered English letters, numbers, a few symbols, and control characters but left out many other language characters. EBCDIC with its 8-bit bytes tried a different approach, with different code pages for various languages, but it was cumbersome.

To solve this, the Unicode project started in 1987. It aimed to create a universal character set for all languages and symbols. The dominant encoding on the Web is UTF-8. It keeps ASCII characters as they are and uses multiple bytes for extended characters.

Now, characters are represented as bytes using two hexadecimal digits. For example, the letters A, R, and M are encoded like this: 01000001 01010010 01001101.

This way, we can handle text in most languages, using 8 bits for each character or multiples of 8 for complex characters. This makes it easier to understand long strings of bits. For instance, the bit pattern 01000001 01010010 01001101 encodes the word "Arm."

**Figure 1.1**: Hexadecimal Values for A, R, M Letters

## Machine Code and Assembly

Computers have a unique capability to encode their logic as data, which can be stored in memory or on disk and modified as needed, allowing for software updates to change the computer's operating system without buying a new machine. This logic encoding relies on processor architecture and instruction sets. While it's possible to create a custom processor with its own instruction encoding, most people use existing processors with predefined encodings. For instance, Arm processors have fixed-size instruction encodings (32-bit or 16-bit) determined by the program's instruction set. Each instruction, represented as binary patterns, follows specific rules set by the Arm architecture. For illustration, if designing a 16-bit instruction set, one of the first tasks is to allocate a portion of the encoding for the opcode, specifying the type of instruction to execute (e.g., addition or subtraction). See table 1.1.

_**Table 1.1:** Addition and Subtraction Opcodes equivalent_

| OPERATION   | OPCODE  |
| ----------- | ------- |
| Addition    | 0001110 |
| Subtraction | 0001111 |

Writing machine code manually is possible but impractical. Instead, it's common to write assembly code in a human-readable "assembly language," which is then converted into machine code. This involves defining shorthand instructions called instruction mnemonics, as illustrated in Table 1.2.

_**Table 1.2:**_ _**Mnemonics**_

| OPERATION   | OPCODE  | MNEMONICS |
| ----------- | ------- | --------- |
| Addition    | 0001110 | ADD       |
| Subtraction | 0001111 | SUB       |

Processors require specific instructions to perform operations like addition, and these instructions also need to specify which values to add and where to store the result. For instance, in the operation "a = b + c," the values of b and c must be stored somewhere, and the instruction must identify where to store the result a.

In many processors, such as Arm, temporary values are typically stored in registers, which are small storage locations for "working" values. Programs can load data from memory into registers for processing and store results back in memory as needed. The number and naming conventions of registers depend on the processor's architecture. Using registers is faster than accessing memory directly, reducing the need for memory access and speeding up program execution.

In the previous example of designing a 16-bit instruction, 7 bits are used for the operation (e.g., ADD/SUB), and the remaining 9 bits encode source and destination registers, as well as a constant value for addition or subtraction. These bits are allocated and assigned shortcuts and corresponding machine codes, as outlined in Table 1.3.

**Table 1.3:** Manually Assigning the Machine Codes

| OPERATION            | MNEMONICS | MACHINE CODE |
| -------------------- | --------- | ------------ |
| Addition             | ADD       | 0001110      |
| Subtraction          | SUB       | 0001111      |
| Integer Value 2      | #2        | 010          |
| Operand register     | R0        | 000          |
| Destination register | R1        | 001          |

Instead of manually creating machine codes, a more efficient approach is to write a program that converts human-readable assembly language syntax like "ADD R1, R0, #2" (meaning R1 = R0 + 2) into the corresponding machine-code pattern. This machine-code pattern can then be used by the processor. Refer to Table 1.4 for examples.

**Table 1.4:** Programming the Machine Codes

| INSTRUCTION    | BINARY MACHINE CODE | HEXADECIMAL ENCODING |
| -------------- | ------------------- | -------------------- |
| ADD R1, R0, #2 | 0001110 010 000 001 | 0x1C81               |
| SUB R1, R0, #2 | 0001111 010 000 001 | 0x1E81               |

The constructed bit pattern represents one of the instruction encodings for 16-bit ADD and SUB instructions within the T32 instruction set. Figure 1.3 illustrates the components of this encoding and their arrangement within the instruction.

**Figure 1.2**: 16-bit Thumb encoding of ADD and SUB instructions

Modern processors indeed offer a wide range of instructions, often with intricate sub-encodings. For instance, Arm architecture includes the load register instruction (LDR) that retrieves a 32-bit value from memory and stores it in a register. In this instruction:

* The memory address to load from is specified in register 2 (R2).
* The read value is written to register 3 (R3).

This illustrates how processors can handle more complex operations beyond simple ADD or SUB instructions, accommodating a variety of tasks.

**Figure 1.3:** LDR instruction loading a value from the address in R2 to register R3

The use of brackets around R2 in the syntax signifies that the value stored in R2 should be treated as a memory address, not as a regular data value. In other words, the intent is not to copy the value from R2 to R3 but to access the contents of the memory location pointed to by the value in R2 and load that content into R3. Programs may need to reference memory locations for various reasons, such as function calls or data loading from memory into a register.

This distinction highlights the fundamental difference between assembly code and machine code. Assembly language is a human-readable syntax that provides a clear interpretation of how each encoded instruction should be executed. On the other hand, machine code is the actual binary data that the processor consumes and processes, with its encoding precisely defined by the processor's designer.

## Assembling

To convert between assembly language and machine code, as processors understand only the latter, we use programs called assemblers. Assemblers not only translate assembly instructions into machine code but also interpret directives that guide them to perform tasks like switching between data and code or handling various instruction sets. Assembly language and assembler language refer to the same concept, with syntax and meaning depending on the specific assembler used.

Directives and expressions in assembly programming are helpful shortcuts for the assembly program but are not strictly part of the assembly language itself; they guide the assembler's operation. Different assemblers are available on various platforms, such as the GNU assembler (as), the ARM Toolchain assembler (armasm), or the Microsoft assembler (armasm) found in Visual Studio. These tools enable the conversion process from human-readable assembly to machine code.

Suppose, for example, we want to assemble the following two 16-bit instructions written in a file named **myasm.s:**

```nasm
.section .text
.global _start
_start:
.thumb
      movs r1, #5
      ldr  r3, [r2]
```

In this program, the first three lines consist of assembler directives. These directives provide instructions to the assembler: the first one specifies where the data should be placed (in the .text section), the second defines the label for the entry point of the code as a global symbol (named "\_start"), and the third specifies the use of the Thumb instruction encoding, which allows for 16-bit wide instructions and is part of the Arm architecture.

To compile this program on a Linux operating system machine running on an Arm processor, you can utilize the GNU assembler (as).

`$ as myasm.s -o myasm.o`

The assembler processes the assembly language program "myasm.s" and generates an object file named "myasm.o." Inside this object file, there are 4 bytes of machine code that correspond to the two 2-byte instructions, represented in hexadecimal format.

`05 10 a0 e3 00 30 92 e5`

Assemblers offer a valuable feature known as labels, which serve as references to specific memory addresses. These labels can point to various elements in code, such as branch targets, functions, or global variables, enhancing the organization and readability of assembly language programs.

Let’s take the assembly program as an example.

```nasm
.section .text
.global _start

_start:
      mov r1, #5
			mov r2, #6
			b mylabel 
result:
			mov r0, r4
			b _exit
mylabel: 
			add r4, r1, r2
			b result

_exit: 
			mov r7, #0
			svc #0 
```

In this program, two registers are initialized with values, and it performs branches (jumps) to different labels to execute instructions. It starts with an ADD instruction at the "mylabel" label, followed by branching to the "result" label, executing a move instruction, and concluding with a branch to the "\_exit" label. Labels play a crucial role as references for these program branches, assisting the linker in assigning relative memory locations. Figure 1.4 illustrates the program's flow.

**Figure 1.4:** Previous \*\*\*\*example program flow

Labels in assembly language are versatile and serve not only as references for branching instructions but also for accessing the contents of specific memory locations. They enable tasks like fetching data from memory or directing the program to various points in the code.

```nasm
.section .text
.global _start

_start:

			mov r1, #5         // 1. fill r1 with value 5
			adr r2, myvalue    // 2. fill r2 with address of myvalue
			ldr r3, [r2]       // 3. fill r3 with value at address in r2
			b mylabel          // 4. jump to address of mylabel
result:
		  mov r0, r4         // 7. fill r0 with value in r4
		  b _exit            // 8. Branch to address of _exit
mylabel:
    add r4, r1, r3       // 5. fill r4 with result of r1 + r3
    b result             // 6. jump to result

myvalue:
.word 2                  // word-sized value containing value 2
```

The assembly program uses the ADR instruction to load the address of the variable "myvalue" into register R2 and employs an LDR instruction to load the data stored at that address into register R3. Subsequently, the program branches to the instruction specified by the label "mylabel" performs an ADD operation, and then branches to the instruction denoted by the label "result," following the flow depicted in Figure 1.5.

**Figure 1.5:** Illustration of ADR and LDR instruction logic

In the following example, the assembly code accomplishes the task of printing "Hello World!" to the console and subsequently exiting. It employs a label to reference the string "hello" by loading the relative address of its label "mystring" into register R1, which is achieved through the ADR instruction. This code is used for handling strings and displaying output to the console.

```nasm
.section .text
.global _start

_start:
    mov r0, #1.         // STDOUT
    adr r1, mystring.   // R1 = address of string
    mov r2, #6          // R2 = size of string
    mov r7, #4          // R7 = syscall number for 'write()'
    svc #0              // invoke syscall
_exit:
    mov r7, #0
		svc #0

mystring:
.string "Hello\n"
```

Once you assemble and link this program on a processor that supports the Arm architecture and the specific instruction set used, it will execute and print out "Hello" when run. The program leverages the instructions and labels to achieve this output.

`$ as myasm2.s -o myasm2.o $ ld myasm2.o -o myasm2 $ ./myasm2 Hello`

Modern assemblers are typically integrated into compiler toolchains and are configured to produce object files rather than directly converting assembly instructions into machine code. These object files contain not only the assembly instructions but also symbol information and hints for the compiler's linker program. The linker's role is to combine these object files, resolve dependencies, and create complete executable programs suitable for running on modern operating systems. This modular approach allows for more efficient and flexible program development and compilation processes.

## Cross-Assemblers

Running an Arm program on a different processor architecture, such as Intel x86-64, would typically result in an error. The error message would indicate that the binary file cannot be executed due to an error in the executable format. This occurs because the binary code generated by an assembler is specific to the target architecture and is not compatible with other architectures. Incompatible processor architectures cannot directly execute each other's binary code, as they have different instruction sets and internal structures. To run the program on an Intel x86-64 processor, it would need to be reassembled and linked specifically for that architecture.

`null@ubuntu:~$ ./myasm bash: ./myasm: cannot execute binary file: Exec format error`

Running an Arm binary on an x64 machine is not possible due to differing instruction encoding on these platforms. Even when trying to perform the same operation across different architectures, there are substantial differences in assembly language and machine code formats. For example, the task of moving the decimal number 1 into the first register is achieved differently in Armv8-A (64-bit), Armv8-A (32-bit), and Intel x86-64 instruction sets.

**Armv8-A: 64-Bit Instruction Set (AArch64)**

`d2 80 00 20 mov x0, #1` // move value 1 into register r0

**Armv8-A: 32-Bit Instruction Set (AArch32)**

`e3 a0 00 01 mov r0, #1` //move value 1 into register r0

**Intel x86-64 Instruction Set**

`b8 01 00 00 00 mov rax, 1` // move value 1 into register rax

Not only does the syntax vary, but the machine code bytes also exhibit substantial differences among different instruction sets. Consequently, machine code bytes generated for the Arm 32-bit instruction set would convey an entirely different meaning on a platform with a distinct instruction set, such as x64 or A64.

Similarly, the reverse scenario holds true, where the same sequence of bytes can have significantly varied interpretations on different processors.

**Armv8-A: 64-Bit Instruction Set (AArch64)**

\*\*`d2 80 00 20** mov x0, #1` // move value 1 into register x0

**Armv8-A: 32-Bit Instruction Set (AArch32)**

\*\*`d2 80 00 20** addle r0, r0, #32` // add value 32 to r0 if LE = true

In simpler terms, to run an assembly program on a specific architecture, you must write the program in the assembly language of that architecture and assemble it using an assembler designed for that instruction set.

Surprisingly, it's possible to generate Arm binaries without having an Arm-based machine. To achieve this, you need an assembler that understands Arm syntax. If this assembler is compiled for an x64 platform, you can run it on an x64 machine to create Arm binaries. This practice is known as using a cross-assembler, enabling you to assemble code for a target architecture different from your current working machine's architecture.

As an example, you can download an assembler for AArch32 on an x86-64 Ubuntu machine and assemble your code using that assembler.

`null@ubuntu:~$ arm-linux-gnueabihf-as myasm.s -o myasm.o null@ubuntu:~$ arm-linux-gnueabihf-ld myasm.o -o myasm`

By running the Linux command "file," we can determine that we've successfully generated a 32-bit ARM executable file.

`null@ubuntu:~$ file myasm myasm: ELF 32-bit LSB executable, ARM, EABI5 version 1 (SYSV), statically linked, not stripped`

## High-Level Language

Assembly language has not become the dominant programming language due to its lack of portability. The need to rewrite an entire codebase for each processor architecture is impractical. Instead, higher-level languages have emerged, abstracting processor-specific details and enabling easy compilation for multiple architectures. These higher-level languages are contrasted with assembly, which is low-level and closely tied to specific hardware and architecture.

The classification of "high-level" or "low-level" languages is relative and has evolved over time. C and C++ were once considered high-level, but with the emergence of more abstract languages like Visual Basic or Python, they are sometimes labeled as low-level. The distinction varies depending on perspective and context.

Similar to assembly language, processors cannot directly comprehend high-level source code. Programmers must use a compiler to transform high-level programs into machine code. The choice of target architecture must still be specified, and cross-compilers enable the creation of Arm binaries on non-Arm systems, maintaining this flexibility.

Compilers generate executable files that can run on specific operating systems. These binary executables, rather than the original source code, are commonly distributed to users. Consequently, when analyzing a program, often the only available resource is the compiled executable file.

Reverse engineering often faces the challenge of not being able to revert the compilation process to retrieve the original source code. Compilers are intricate and involve numerous layers of abstraction and iteration between source code and binary output. Additionally, several of these steps eliminate the human-readable information that facilitates programmers' comprehension of the program.

When analyzing software without access to the source code, there are two primary options, depending on the desired level of detail: decompiling or disassembling the executable file.

## Disassembling

Disassembling a binary involves converting the machine-code instructions of the binary into a human-readable assembly language format. Common applications of disassembly include malware analysis, validating compiler performance and output accuracy, as well as vulnerability analysis and developing exploits or proofs-of-concept for defects in closed-source software.

Among these applications, exploit development is notably reliant on analyzing the actual assembly code. While vulnerability discovery can often be accomplished through techniques like fuzzing, the construction of exploits from detected crashes or the investigation of why specific code sections are not triggered by fuzzers often demands a deep understanding of assembly language.

A deep understanding of a vulnerability's precise conditions through assembly code analysis is essential. Compiler-driven decisions regarding variable and data structure allocation play a crucial role in exploit creation, demanding extensive assembly knowledge. Surprisingly, vulnerabilities that appear "unexploitable" can be exploited with creativity and diligent effort invested in comprehending the inner workings of the vulnerable function.

Disassembling an executable file can be achieved using various methods, which will be explored further in the second part of this book. However, for now, a straightforward tool to quickly examine the disassembly output of an executable file on Linux is objdump.

Let’s compile and disassemble the following write() program:

```c
#include <unistd.h>

  int main(void) {
      
			write(1, "Hello!\n", 7);
  }
```

To obtain disassembly output for a specific code file compiled with GCC, you can use the "-c" option, instructing GCC to create the object file without performing the linking process. This allows you to run objdump solely on your compiled code without including disassembly from other object files like the C runtime. The output will provide disassembly details for the main function.

`null@arm32:~$ gcc -c hello.c null@arm32:~$ objdump -d hello.o`

```nasm
Disassembly of section .text:

00000000 <main>:
     0:b580      push{r7, lr}
     2:af00      addr7, sp, #0
     4:2207      movsr2, #7
     6:4b04      ldrr3, [pc, #16]; (18 <main+0x18>)
     8:447b      addr3, pc
     a:4619      movr1, r3
     c:2001      movsr0, #1
     e:f7ff fffe bl0 <write>
    12:2300      movsr3, #0
    14:4618      movr0, r3
    16:bd80      pop{r7, pc}
    18:0000000c .word0x0000000c
```

Linux utilities like objdump are practical for disassembling small programs, but larger programs necessitate more convenient solutions for reverse engineering. A variety of disassemblers are available, including free open-source tools like Ghidra and commercial solutions like IDA Pro.

## Decompilation

A more recent development in reverse engineering is the utilization of decompilers. Decompilers extend beyond disassemblers by attempting to reconstruct equivalent C/C++ code from a compiled binary, providing a higher-level representation of the program's functionality.

Decompilers offer value by simplifying and condensing disassembled output through the generation of pseudocode. This simplification aids in quickly comprehending the program's high-level functionality when reviewing a function.

Decompilers simplify code comprehension by generating pseudocode but come with trade-offs. They can lose important details during the decompilation process. The original source code, including symbol names, local variables, comments, and program structure, is inherently altered by compilation and not fully recoverable. Additionally, automatic naming or relabeling of local variables and parameters can be misleading when storage locations are reused by aggressive optimizing compilers.

We will illustrate the decompilation process by examining a C function, compiling it with GCC, and then decompiling it using both IDA Pro's and Ghidra's decompilers. This practical example will provide insights into how decompilers work and their output.

**Figure 1.6** illustrates a function called file\_record in the ihex2fw.c file from the Linux source code repository.

**Figure 1.6:** Source code of file\_record function in the ihex2fw.c from linux source code

After compiling the C file on an Armv8-A architecture and loading the executable into IDA Pro 7.6, Figure 1.7 displays the pseudocode generated by the decompiler for the earlier function. This provides a visual representation of how decompilers interpret and present the code.

**Figure 1.7:** IDA 7.6 decompilation output of the compiled file\_record function

Figure 1.8 shows the same function decompiled by Ghidra 10.0.4. In both cases, we can discern a semblance of the original code if we strain our eyes, but the decompiled code is significantly less readable and less intuitive compared to the original. In essence, while decompilers can provide a quick high-level overview of a program in many cases, they are not a universal solution and cannot replace the need for diving into the assembly code of a program when necessary.

**Figure 1.8:** Ghidra 10.0.4. decompilation output of the compiled file\_record function

and operating systems. For this reason, many of the programs we encounter in binary analysis start life as C/C++ code.

Computers do not execute source code files directly. Before the program can be run, it must first be translated into the machine instructions that the pro- cessor knows how to execute. The programs that perform this translation are called _compilers_. On Linux, GCC is a commonly used collection of compilers, including a C compiler for converting C code into ELF binaries that Linux can load and run directly. G++ is its counterpart for compiling C++ code. Figure 2.1 shows a compilation overview.
