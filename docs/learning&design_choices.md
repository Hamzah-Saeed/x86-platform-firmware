# Phase 0 - Tooling & Mental Model
***
### <u>What does "freestanding mean?"</u>
Essentially, this project is to build an x86-64 bare-metal OS in QEMU. Which means we are building with C, when there is no operating system. Typically, C consists of the following:

C program --> compiler --> libc --> linux system calls --> hardware

However, instead, for my kernel it looks like:
C program --> compiler --> machine code --> CPU

We never hit libc. 

There exists an option in GCC called "-ffreestanding", which is a flag. This prevents the compiler from depending on the standard C library, and still compiling. Essentially, there is no written C library, and freestanding code, is code without that library.

### <u>ELF Sections</u>
When you write code, the compiler doesn't simply recognize that code, it categorizes the stuff that was written. These are categorized into the sections: .text, .data, .bss, .rodata.

These distinguish that different types of data have different requirements. Some stuff is executable, some stuff don't need to be writeable, some stuff need to be writeable, etc.


| Section Type | Description                                                                                                                                                                                                         |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| .text        | The actual code. The compiler turns this into actual machine instructions.                                                                                                                                          |
| .data        | Initialized writable global data. Not actual code, it's data that can be written to. Data is generally mapped to read and write.                                                                                    |
| .bss         | Not really sure. The example if with "char buffer[1000000];", instead of literally storing one million zero bytes, an ELF file can say "I need 1 MB of zero initialized memory" without actually storing the zeroes |
| .rodata      | Read only data. Things like constants where the values can be read, but do not change.                                                                                                                              |

There are more sections. There are also *segments*, which describe HOW the executables should be loaded into memory. Sections organize the file, segments organize loaded moemory.

### <u>Why a cross compiler</u>
A cross compiler is a compiler that runs on the host, but emits code for the target. Essentially, the way I understand this, is that by default, GCC is a Linux compiler, and that it generates machine code FOR a linux system. It therefore expects a linux environment. The intent behind the project, is the kernel doesn't live under Linux. So, the cross compiler should be hitting the x86-64 target from wherever I host it.

It can still function without a cross compilor with the -ffreestanding and -nostdlib flags, but the intent behind it is to make the separation between the host and target clear. We are targeting an ELF-based environment. We are going down the cross compiler route. We don't want the compiler to assume linux.

## <u>QEMU Virtual Motherboard</u>
Essentially an emulator for a system, that allows me to mimic how the kernel would perform on a real computer, without accidentally somehow screwing up my own system. It's a fake PC.
