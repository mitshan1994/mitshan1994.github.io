---
layout: post
title:  "C/C++ Compiling, Linking and Loading"
categories: cpp
---

Basics of executable/library compiling, linking, loading and executing.

References:
* CSAPP, 3rd edition.
* Others.

### Definitions
**ELF**: Executable and Linkable Format.

**Relocatable object file**. Contains binary code and data in a form that can be combined with other relocatable object files at compile time to create an executable object file.

### ELF File
The following is a typical ELF relocatable object file:

| ELF header |
| --- |
| .text |
| .rodata |
| ... |
| .strtab |
| Section header table |

The *ELF header* contains information that allows linker to parse and interpret the object file, including the size of the header, file type, machine type, **offset of the section header table**, size and number of entries in the section header table.

The *section header table* describes the locations and size of the various sections. It contains a fixed-size entry for each section in the object file.


### Sections in ELF Relocatable Object
* **.text**: Machine code of the program.
* **.rodata**: Read-only data.
* **.data**: *Initialized* global and static C variables.
* **.bss**: *Uninitialized* global and static C variables, along with any global or static variables that are initialized to zero.
* **.symtab**: A symbol table with information about functions and global variables that are defined and referenced in the program.
* **.rel.text**: A list of locations in the `.text` section that will need to be modified when the linker combines this object file with others.
* **.rel.data**: Relocation information for any global variables that are referenced or defined by the module.
* **.debug**: A debugging symbol table, including the original C source file. It is only present if compiled with `-g` option.
* **.line**: A mapping between line numbers in the original C source program and machine code in the `.text` section. Only available under `-g`.
* **.strtab** A string table for the symbol tables in the `.symtab` and `.debug` sections and for the section names in the section headers. It is a sequence of null-terminated character strings.

### Symbol Tables
Symbol table are built by assemblers, using symbols exported by the compiler into the assembly-language `.s` file. It is an array of symbol entries, whose structure is:
```
typedef struct {
    uint32_t      st_name;   // String table offset.
    unsigned char st_info;   // Type; Local or global.
    unsigned char st_other;  // Visibility.
    uint16_t      st_shndx;  // Relevant section header table index.
    Elf64_Addr    st_value;  // Symbol's address, offset or absolute.
    uint64_t      st_size;   // Size of the object.
} Elf64_Sym;
```

Three pseudosections that don't have entries in the section header table:
* **ABS**: symbols that should not be relocated.
* **UNDEF**: undefined symbols, which are referenced in this module but defined elsewhere.
* **COMMON**: uninitialized data objects that are not yet allocated.

### Symbol Resolution
The linker resolves symbol references by associating each reference with exactly one symbol definition from the symbol tables of its input relocatable object files.

#### *Strong* and *Weak* Symbol
When compiling under Linux, functions and initialized global variables get strong symbols. Uninitialized global variables get weak symbols.

Linux linkers use the following rules for dealing with duplicate symbol names:
* Rule 1: Multiple strong symbols with the same name are not allowd.
* Rule 2: Given a strong symbol and multiple weak symbols with the same name, choose the strong symbol.
* Rule 3: Given multiple weak symbols with the same name, choose any of the weak symbols.

#### Resolving References when Linking Statically
The rules of linking is list on page 688 (7.6.3) in CSAPP 3rd edition.

During symbol resolution phase, the linker scans the relocatable object files and archives left to right in the same sequential order that they appear on the compiler driver's command line. (The order is significant)

If the library that defines a symbol appears on the command line before the object file that references that symbol, then the reference will not be resolved and linking will fail.

### Relocation
After the linker collects all object files to be merged, relocation begins, to merge the input modules and assigns run-time address to each symbol. Relocation consists of two steps:
1. Relocalting sections and symbol definitions.
2. Relocating symbol references within sections.

#### Relocation Entries
When the assembler encounters a reference to an object whose ultimate location is unknown, it generates a *relocation entry* that tells linker how to modify the reference when it merges the object file into an executable. Relocation entries for code are places in `.rel.text`. Relocation entries for data are placed in `.rel.data`.

x86-64 *small code model* assumes that the total size of the code and data in the executable object file is smaller than 2GB, then thus can be accessed at run-time using 32-bit PC-relative address (Program Counter). It's default for `gcc`.

### Executable Object Files
Since the executable is *fully linked*, it needs no `.rel` sections.

ELF executables are designed to be easy to load into memory, with contiguous chunks of the executable file mapped to contiguous memory segment. This mapping is described by *program header table*(*segment header table*).

The process of copying the program into memory and then running it is known as *loading*.

On Linux x86-64 systems, the code segment starts at address `0x40000`, followed by the data segment. The run-time `heap` follows the data segment and grows upward via calls to the `malloc` library. This is followed by a region that is reserved for shared modules.
The user stack starts below the largest legal user address (2^48 - 1) and grows down. The region above the stack, is reserved for the code and data in the *kernel*.

### Dynamic Linking with Shared Libraries
A shared library is an object module that can be loaded at an arbitrary meomory address and linked with a program in memory. This process is known as *dynamic linking* and is performed by a program called a *dynamic linker*.

During linking of executable, the linker copies some relocation and symbol table information that will allow references to code and data in shared objects to be resolved at load time. The executable is *partially linked*.

After the loader loads the partially linked executable, it notices that it contains a `.interp` section, which contains the path name of the dynamic linker, which is itself a shared object (e.g. `ld-linux.so` on Linux). Instead of passing control to the application, the loader loads and runs the dynamic linker. The dynamic linker then finishes the linking task by relocating dependent shared objects into memory, relocating undefined symbols in executable.



