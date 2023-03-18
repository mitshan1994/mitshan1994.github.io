---
layout: post
title:  "C/C++ Compiling, Linking and Loading"
categories: cpp
---

Basics of executable/library compiling, linking, loading and executing.

References:
* CSAPP
* Others

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


