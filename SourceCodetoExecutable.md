# C++ Compilation process:
<img width="940" height="1283" alt="image" src="https://github.com/user-attachments/assets/8a9a4631-2142-4a0e-888f-75e43051e0af" />

## 1. C Preprocessor (CPP)

### Overview

The **C Preprocessor** is a program that processes and modifies your source code in the beginning of compilation. It handles **directives** that begin with `#`, performing textual substitutions and code inclusion. Its output is a pure C code file that the compiler then compiles. The result is an expanded .i file (pure C code with all macros and includes expanded).

### Key Functions of the Preprocessor

1. **File Inclusion** – Inserts contents of another file into the source file.
2. **Macro Substitution** – Replaces identifiers with specified code snippets.
3. **Conditional Compilation** – Removes or keep certain parts of code based on given conditions.

---

### 1. File Inclusion (`#include`)

Used to include header files or external code. After preprocessing, the included file’s code 

```c
#include <stdio.h>   // System header file 
#include "myheader.h"  // User-defined header file 
```

When encountered, the preprocessor literally replaces the **#include** line with the actual content of the file.

**Example:**

```c
// main.c
#include "mathlib.h"
int main() {
printf("Sum = %d", add(2, 3));
}
```
If mathlib.h contains:
```c
// add.h
int add(int a, int b) { return a + b; }
```
After preprocessing, `main.c` effectively contains the code from both files.

---

### 2. Macro Substitution (`#define`)

Macros define constants or reusable code fragments.

#### (a) Object-like Macros

```c
#define PI 3.14159
#define MAX 100
```

Whenever `PI` or `MAX` appear in code, they are replaced by thier values.

#### (b) Function-like Macros

```c
#define SQUARE(x) ((x) * (x))
```

**Usage:**

```c
int a = SQUARE(5);  // Expands to ((5) * (5))
```
Preprocessor Expands it to ((called_value) * (called_value)) wherever SQUARE() is used.

---

### 3. Conditional Compilation

Used to keep code for compilation or ignore parts of code depending on certain conditions.

```c
#define DEBUG 1

#ifdef DEBUG
    printf("Debug mode active\n");
#endif
```
Only keeps the print code when DEBUG is defined.

Other conditional directives:

```c
#if CONDITION
#elif OTHER_CONDITION
#else
#endif
```

**Example:**

```c
#define OS 1

#if OS == 1
  printf("Running on Linux\n");
#elif OS == 2
  printf("Running on Windows\n");
#else
  printf("Unknown OS\n");
#endif
```
**Preprocessor → expands macros and includes → compiler compiles expanded code**
---

# 2. Compiler

A **compiler** converts preprocessed high-level language code into assembly code that goes input to assembler. The compilation process occurs in **two main phases**:

1. **Front-end Phase** — Understands the program and ensures correctness.
2. **Back-end Phase** — Optimizes and generates machine code.

Each phase has several sub-stages, shown in the diagram: **Lexical Analysis → Syntax Analysis → Semantic Analysis → Intermediate Code Generation → Code Optimization → Code Generation.**

<img width="416" height="522" alt="image" src="https://github.com/user-attachments/assets/f4a302fd-97ec-4690-8c60-ec7f7f496eef" />

---

## ⚙️ 1. Front-End Phase

The front-end focuses on **analyzing and understanding** the source code. Its main tasks are to check for **errors** and to build an internal representation of the program.

### 🔹 (a) Lexical Analysis — *Tokenizing the Source Code*

**Purpose:**

* Converts raw source code (sequence of characters) into **tokens** — the smallest meaningful units.

**Example:**

```c
int sum = a + b;
```

➡ Lexical analyzer outputs:

```
[INT] [ID:sum] [ASSIGN] [ID:a] [PLUS] [ID:b] [SEMICOLON]
```

**Key points:**

* Removes comments and whitespace.
* Detects invalid tokens.
* Generates a **token stream** used by the parser.

**Initial Symbol Table (After Lexical Analysis):**

| Symbol | Type | Scope  | Value |
| ------ | ---- | ------ | ----- |
| sum    | int  | global | —     |
| a      | int  | global | —     |
| b      | int  | global | —     |
---

### 🔹 (b) Syntax Analysis — *Checking Grammatical Structure*

**Purpose:**

* Ensures the tokens follow the grammar (rules) of the language.
* Builds a **Parse Tree** or **Abstract Syntax Tree (AST)**.

**Example:**
For the expression:

```c
sum = a + b * c;
```

AST:

```
   =
  / \
 sum  +
     / \
    a   *
       / \
      b   c
```

**Errors Detected:**

* Missing semicolons, brackets.
* Misplaced operators.
* Invalid syntax.

**Output:** AST representing the structure of the program.

---

### 🔹 (c) Semantic Analysis — *Checking Meaning and Types*

**Purpose:**

* Ensures **logical and type correctness**.
* Checks whether variables, functions, and operations make sense.

**Examples:**

```c
int x = "hello";  // ❌ Type mismatch
float sum = a + b; // ✅ if a,b are float
```

**Checks performed:**

* Type compatibility.
* Variable declaration before use.
* Function arguments and return types.

**Updated Symbol Table (After Semantic Checks):**

| Symbol | Type  | Scope  | Value   | Status                   |
| ------ | ----- | ------ | ------- | ------------------------ |
| sum    | int   | global | —       | ✅ Valid                  |
| a      | int   | global | —       | ✅ Valid                  |
| b      | float | global | —       | ⚠️ Type mismatch warning |
| x      | int   | global | "hello" | ❌ Error: Type mismatch   |

**Key Differences:**

* Added **Value** column (shows assigned values if any).
* Added **Status** column to show validation results.
* Type mismatches and undeclared variables are now marked.

**Output:** Annotated AST (with type and scope info) + updated Symbol Table.

---

### 🔹 (d) Intermediate Code Generation — *Platform-Independent Representation*

**Purpose:**

* Converts the verified AST into an **Intermediate Representation (IR)** that is easier to optimize and portable across architectures.

**Example:**
For `a = b + c * d;`
Intermediate Code (Three-Address Code, TAC):

```
t1 = c * d
a = b + t1
```

**Advantages of IR:**

* Easier to optimize.
* Independent of hardware.
* Acts as a bridge between front-end and back-end.

---

## ⚙️ 2. Back-End Phase

The back-end takes the intermediate code and produces efficient machine code for a specific processor.

### 🔹 (e) Code Optimization — *Making Code Efficient*

**Purpose:**

* Improve the performance of code without changing its output.
* Optimize **speed**, **memory usage**, and **resource efficiency**.

**Types of Optimizations:**

1. **Constant Folding:**

   ```c
   int x = 3 * 4;  // → int x = 12;
   ```
2. **Dead Code Elimination:**

   ```c
   if (0) { printf("Hello"); }  // Removed
   ```
3. **Common Subexpression Elimination:**

   ```c
   x = a * b;
   y = a * b + c;  // → reuse result of a*b
   ```
4. **Loop Optimization:**
   Move calculations outside loops if they don’t change within.

**Goal:** Minimize computation and memory access.

**Output:** Optimized Intermediate Code.

---

### 🔹 (f) Code Generation — *Producing Assembly Code*

**Purpose:**

* Converts optimized intermediate code into **target machine instructions**.
* Handles registers, addressing modes, and instruction selection.

**Example:**
For IR:

```
t1 = c * d
a = b + t1
```

Possible x86 Assembly:

```asm
MOV EAX, [c]
IMUL EAX, [d]
ADD EAX, [b]
MOV [a], EAX
```

**Sub-stages:**

1. **Register Allocation:** Assigns variables to CPU registers efficiently.
2. **Instruction Selection:** Chooses appropriate machine instructions.
3. **Instruction Scheduling:** Orders instructions to avoid stalls and improve CPU pipeline performance.

**Output:** Assembly Code (.s in c)

---

## 🧩 Putting It All Together 

| Phase         | Stage                        | Purpose                             | Output                               |
| ------------- | ---------------------------- | ----------------------------------- | ------------------------------------ |
| **Front-end** | Lexical Analysis             | Convert characters to tokens        | Token stream + initial symbol table  |
|               | Syntax Analysis              | Build structure of program          | Parse tree / AST                     |
|               | Semantic Analysis            | Check meaning and type correctness  | Annotated AST + updated symbol table |
|               | Intermediate Code Generation | Platform-independent representation | Intermediate code (IR)               |
| **Back-end**  | Code Optimization            | Improve speed and efficiency        | Optimized IR                         |
|               | Code Generation              | Produce final assembly/machine code | Target code                          |

---
# 3. Assembler

After the **compiler** generates the **assembly code**, the next stage in the C++ compilation process is handled by the **Assembler**. The **Assembler** converts the **assembly code (.s)** — a human-readable form of low-level instructions — into **object code (.o)**, which is the actual **binary machine code** that the CPU can execute.

---

## 🔹 Overview

* The assembler is a **translator** that converts **assembly language** into **machine language** (binary instructions).
* Assembly code is architecture-specific (x86, ARM, etc.). Each instruction like `MOV`, `ADD`, or `JMP` is translated into corresponding binary opcodes.
* The assembler also creates important data structures like the **Symbol Table(different from compiler's table)** and **Relocation Table**, which are used later by the **Linker**.

---

## ⚙️ Input and Output

| Step      | Input         | Output      | File Extension              |
| --------- | ------------- | ----------- | --------------------------- |
| Assembler | Assembly Code | Object Code | `.o` (or `.obj` on Windows) |

**Example:**

```bash
g++ -c Hello.s
```

This produces:

```
Hello.o   // Object file
```

---

## 🧠 What Happens Inside the Assembler

The assembler performs several key tasks:

### 1. **Parsing Assembly Instructions**

* Reads each instruction line (like `MOV EAX, EBX`) and breaks it into **opcode** and **operands**.
* Verifies the syntax according to the target processor's instruction set.

**Example:**

```asm
MOV EAX, [a]
ADD EAX, [b]
MOV [sum], EAX
```

Assembler parses this into tokens and ensures valid instruction format.

---

### 2. **Opcode Translation (Mnemonic → Binary)**

* Converts symbolic mnemonics into actual **binary opcodes** that the CPU understands.
* Each mnemonic (like `MOV`, `ADD`) has a unique binary encoding depending on registers and addressing modes.

**Example:**

| Assembly     | Binary Equivalent   |
| ------------ | ------------------- |
| MOV EAX, EBX | `10001011 11011000` |
| ADD EAX, 1   | `00000101 00000001` |

---

### 3. **Symbol Table Generation**

* The assembler maintains a **symbol table** for all labels, variables, and function references.

**Example:**

| Symbol | Address    | Type     |
| ------ | ---------- | -------- |
| `main` | 0x00400000 | Function |
| `sum`  | 0x00400014 | Variable |
| `a`    | 0x00400018 | Variable |

If a symbol is **declared but not yet defined**, it is marked as **external** and resolved later by the linker.

---

### 4. **Relocation Table Creation**

When a program is assembled, it is not tied to a fixed location in memory. Modern systems often load programs into different memory regions each time they execute. To make this possible, assemblers and loaders use **relocation** — the process of adjusting address-dependent instructions and data when a program is loaded. The **Relocation Table** is the assembler’s way of helping the loader do this correctly. It records **which parts of the object code depend on memory addresses**, allowing the loader to modify them later.

#### ⚙️ Purpose of Relocation Table

> To allow the same program to run correctly **no matter where it is loaded in memory**. Without relocation, all addresses in the code would be **absolute** (fixed). If the program were loaded at a different address, all jumps, memory references, and data accesses would point to the wrong locations. The relocation table identifies **which addresses inside the object code** need to be modified when the loader decides where to place the program in main memory.

#### Address-Dependent vs Non-Address-Dependent Fields

Only the parts of code that references memory addresses need relocation. Instructions that use registers, immediate constants, or opcodes alone are not affected by where the program is loaded — hence, not address-dependent so they wont be listed in Relocation table.

| Type of Field           | Example      | Depends on Memory Address? | Needs Relocation? |
| ----------------------- | ------------ | -------------------------- | ----------------- |
| **Memory reference**    | `MOV A, NUM` | ✅ Yes                      | ✅ Yes             |
| **Jump instruction**    | `JMP LOOP`   | ✅ Yes                      | ✅ Yes             |
| **Register operations** | `MOV A, B`   | ❌ No                       | ❌ No              |
| **Immediate constant**  | `MVI A, 5`   | ❌ No                       | ❌ No              |
| **HLT or NOP**          | `HLT`        | ❌ No                       | ❌ No              |

#### Example Assembly Code

```asm
START:  MOV A, NUM
        ADD A, VALUE
        JMP END
NUM:    DB 5
VALUE:  DB 10
END:    HLT
```

Assume the assembler starts from address `0000`.

| Address | Machine Code | Description           |
| ------- | ------------ | --------------------- |
| 0000    | MOV A, 0006  | Uses address of NUM   |
| 0003    | ADD A, 0007  | Uses address of VALUE |
| 0006    | 05           | Data for NUM          |
| 0007    | 0A           | Data for VALUE        |

#### Relocation Table Created by Assembler

| Field Address | Description      | Requires Relocation | Reason                 |
| ------------- | ---------------- | ------------------- | ---------------------- |
| 0001–0002     | Address of NUM   | ✅ Yes               | Depends on label NUM   |
| 0004–0005     | Address of VALUE | ✅ Yes               | Depends on label VALUE |
| Others        | Opcodes and Data | ❌ No                | No memory dependency   |

This table tells the loader exactly which bytes must be modified when the program’s base address changes.

#### Loader’s Role in Relocation

When the loader places the program in memory, it chooses a **load address (base address)** — e.g., 0x4000 instead of 0x0000. Then, for each entry in the relocation table, the loader **adds the base address** to the corresponding address field.

Example:

| Field | Original Address | New Address (Base=4000h) |
| ----- | ---------------- | ------------------------ |
| NUM   | 0006             | 4006                     |
| VALUE | 0007             | 4007                     |

So, instruction `MOV A, 0006` → becomes `MOV A, 4006`. This ensures that the program accesses the correct memory locations at runtime.

#### Absolute vs. Relative Addressing in Relocation

**Absolute Addressing:** Uses fixed memory addresses (e.g., `JMP 2050H`). The instruction directly specifies the target memory address. When the program is moved, these absolute addresses must be modified using the relocation table because they depend on the program’s physical location.

**Relative Addressing:** Uses offsets (e.g., `JMP +05` or `JMP LOOP`, where the assembler stores the distance from the current instruction). These addresses are position-independent — if the program moves, the relative offset remains valid. Hence, **relative addresses often don’t need relocation.**

---

### 5. **Machine Code Generation**

* The assembler produces binary machine instructions from parsed assembly.
* Each instruction is encoded with the exact bit pattern recognized by the CPU.

The generated **object file (.o)** contains:

* Machine instructions (binary form)
* Symbol table
* Relocation table
* Debug information (optional)

**Layout of Object File:**

<img width="769" height="576" alt="image" src="https://github.com/user-attachments/assets/3ce2a5c1-b084-4e9c-9ef5-71968e26404e" />


## 🧩 Assembler Types

| Type                   | Description                                                                  | Example                     |
| ---------------------- | ---------------------------------------------------------------------------- | --------------------------- |
| **One-pass assembler** | Translates and assigns addresses in a single scan; faster but less flexible. | Used in embedded systems    |
| **Two-pass assembler** | First pass builds symbol table; second pass resolves addresses.              | Used in most modern systems |

---

### Responsibilities

| Function                              | Description                               |
| ------------------------------------- | ----------------------------------------- |
| **Translate Assembly → Machine Code** | Converts mnemonics into binary opcodes    |
| **Symbol Table Management**           | Maps labels and variables to addresses    |
| **Relocation Table Creation**         | Marks addresses to be fixed by the linker |
| **Error Checking**                    | Detects syntax or undefined symbol errors |
| **Output Object Code**                | Produces `.o` file ready for linking      |

---

# 4. Linker

After the **assembler** generates one or more object files (`.o`), the **Linker** combines them into a single **executable file** (`a.out` on Linux or `.exe` on Windows). It resolves all symbol references across files and connects all program pieces together.

---

In large programs, the code is divided into multiple **source files** (`.c` or `.cpp`) to improve modularity and manageability. Each source file is compiled independently by the compiler → assembler → into its own **object file (.o)**.

### 🧩 Example:

Suppose we have three files:

```c
// main.cpp
#include "math.h"
int main() {
    int result = add(2, 3);
    return result;
}

// math.cpp
int add(int a, int b) {
    return a + b;
}

// utils.cpp
void printMsg() {
    printf("Hello World\n");
}
```

Each `.cpp` file is compiled separately:

```bash
g++ -c main.cpp   → main.o
g++ -c math.cpp   → math.o
g++ -c utils.cpp  → utils.o
```

Each object file contains:

* **main.o** → `main()` definition, external reference to `add()`.
* **math.o** → definition of `add()`.
* **utils.o** → definition of `printMsg()`.

All `.o` files → combined by the linker → single **executable**. If any object file is missing, the linker will throw an **undefined reference** error because it cannot find the symbol definition. 

---

## 🧩 1. Symbol Resolution — *Connecting All References*

Each object file has its own **symbol table**. Some symbols are **defined** in the same file, some are externally referenced. The linker’s first job is to connect these references.

**Example:**

`main.o`:

```c
extern int add(int, int);
int main() { return add(2, 3); }
```

`math.o`:

```c
int add(int a, int b) { return a + b; }
```

During linking:

* `main.o` references symbol `add` (undefined locally).
* `math.o` defines symbol `add`.
* The linker connects `main.o` → `math.o`.
---


## ⚙️ 2. Relocation — *Fixing Memory Addresses*

Each object file assumes its **code and data start at address `0`**. When linking multiple files, the **linker does not know the final physical memory addresses** (that’s determined later by the loader). Instead, it assigns **relative or virtual addresses** — offsets within the program’s memory layout — and updates all references using the **Relocation Table** from the assembler.

These offsets ensure that all functions and variables have unique locations *within the program*, even before the program is actually loaded into RAM. When the loader later loads the executable into memory, it decides the **base address** (for example, `0x400000`), and adds this base address to all offsets defined by the linker. This is how final virtual addresses are computed at runtime.

---

### 🔧 Steps in Relocation:

1. **Merge** text (code) and data sections from all object files.
2. **Assign relative start offsets** to each section within the executable.
3. **Adjust (relocate)** every address-dependent instruction or data reference using the relocation entries.
4. The **loader** later adds the base address to these offsets when loading the program into memory.

---

**Example:**

| Symbol | Linker Offset | Loader Base Address | Final Virtual Address |
| ------ | ------------- | ------------------- | --------------------- |
| `main` | 0x0000        | 0x400000            | 0x400000              |
| `add`  | 0x1000        | 0x400000            | 0x401000              |
| `msg`  | 0x1020        | 0x400000            | 0x401020              |

If the instruction in `main.o` says:

```
CALL 0x0000   ; placeholder for add()
```

The linker updates it to:

```
CALL 0x1000   ; relative offset to add()
```

When the loader loads the program at base address `0x400000`, it becomes:

```
CALL 0x401000 ; actual virtual address in memory
```

---

## 🧱 3. Merging Sections — *Building the Executable*

After relocation, the linker merges sections from all object files into a single memory layout with consistent virtual offsets.

| Section     | Merged From                | Purpose                                         |
| ----------- | -------------------------- | ----------------------------------------------- |
| **.text**   | Code sections of all files | Contains all CPU instructions                   |
| **.data**   | Initialized variables      | Program’s data segment                          |
| **.bss**    | Uninitialized variables    | Allocated later by loader                       |
| **.rodata** | Constants                  | Read-only section (e.g., strings, const values) |

The linker combines them into one continuous address space, ready for the loader to place into actual memory.

---

## 🔗 4. Static vs Dynamic Linking

### 🧩 **Static Linking**

* In **static linking**, the linker copies all required functions and variables from libraries directly into the executable.
* It happens **at compile time**, producing a single self-contained binary file.
* The resulting executable has no dependency on external `.so` or `.dll` files — all code is included.

**Example:**

```bash
g++ main.o math.o -static -o prog
```

✅ This embeds all the library routines (like `printf`, `sqrt`, etc.) into the final binary.

**How it works:**

* The linker searches the static libraries (like `libc.a`) for symbols used in the program.
* It copies the **used object modules** into the final executable.
* The result is a **larger but independent** executable that can run on any system without external dependencies.

**Advantages:**

1. **No runtime dependencies** – program can run even if libraries are missing on the system.
2. **Faster startup and execution** – no runtime symbol resolution.
3. **Portability** – single self-contained binary.

**Disadvantages:**

1. **Larger file size** – all library code is embedded.
2. **Recompilation needed** when any library code changes.
3. **Redundant memory usage** – if multiple programs use the same library, each keeps its own copy in memory.

---

### 🧩 **Dynamic Linking**

* In **dynamic linking**, the executable only stores **references** to external shared libraries instead of copying their code.
* The actual library code (`.so` on Linux or `.dll` on Windows) is loaded **at runtime** by the **Loader**.
* This makes the executable much smaller and allows multiple programs to share the same library code in memory.

**Example:**

```bash
g++ main.o -lm -o prog  # links dynamically with libm.so
```

**How it works:**

* The linker notes which shared libraries the program depends on and adds this information to the executable header.
* When the program runs, the **loader** finds and loads these shared libraries into memory.
* The **Dynamic Linker/Loader (ld-linux.so)** resolves all symbol references so the program can use library functions.

**Advantages:**

1. **Smaller executables** – libraries aren’t included in the file.
2. **Memory efficiency** – shared libraries are loaded once and shared among processes.
3. **Easier updates** – updating a library automatically benefits all dependent programs.

**Disadvantages:**

1. **Runtime dependency** – program won’t run if required libraries are missing or incompatible.
2. **Slightly slower startup** – time needed for the loader to resolve symbols.
3. **Version conflicts** – if library APIs change, older programs may break (the classic *DLL Hell* problem on Windows).

---

### 🧠 In short:

* **Static Linking** → Everything packaged into one big box before shipping.
* **Dynamic Linking** → Only the address of the library is stored; the actual book is fetched when needed at runtime.


## Output of Linker

After all linking steps, the linker produces an **Executable File** that includes:

1. **Merged sections** (code, data, bss, rodata).
2. **Resolved symbol table** (no unresolved externals).
3. **Relocation information cleared** (all addresses finalized).
4. **Entry point (main)** marked in the file header.

**Example command:**

```bash
g++ -o Hello Hello.o math.o utils.o
```

Output:

```
Hello → executable binary
```
---

# 5. Loader

Once the **linker** has produced an **executable file**, the final stage in the C++ compilation process is handled by the **Loader** — a part of the **Operating System** responsible for loading the program into memory and starting its execution.

---
The **Loader** performs several crucial tasks before your program begins running:

1. **Loads the executable** from disk into memory (RAM).
2. **Allocates space** for program sections (.text, .data, .bss, stack, heap).
3. **Performs runtime relocation** – adjusts addresses according to where the program is loaded.
4. **Links dynamically shared libraries** (for dynamic linking).
5. **Transfers control** to the program’s entry point (`main()` function).

---

## ⚙️ 1. Loading the Executable into Memory

When you run a program (e.g., typing `./a.out` in Linux or double-clicking an .exe in Windows):

* The **OS loader** reads the **executable file header** (like ELF in Linux or PE in Windows).
* The header specifies where each section (.text, .data, .bss) should be loaded in virtual memory.

**Example Layout:**

| Section   | Description                  | Typical Virtual Address       |
| --------- | ---------------------------- | ----------------------------- |
| `.text`   | Code (instructions)          | 0x400000 – 0x40FFFF           |
| `.data`   | Initialized data             | 0x600000 – 0x600FFF           |
| `.bss`    | Uninitialized data           | 0x601000 – 0x601FFF           |
| **Stack** | Function call stack          | High memory (e.g., 0x7FFF...) |
| **Heap**  | Dynamically allocated memory | Between `.bss` and stack      |

The loader copies these sections from the executable file into the correct regions of memory.

---

## ⚙️ 2. Relocation (Runtime Address Adjustment)

When loading a program, the loader chooses a **base address** for it in memory. If the program wasn’t compiled as **position-independent**, the loader uses the **relocation table** (from the linker) to fix every instruction or variable that depends on an absolute address.

**Example:**

If the linker assumed `.text` starts at `0x0000`, but the loader loads it at `0x400000`, then every memory reference is updated by adding the offset `0x400000`.

| Symbol   | Old Address | Base Address | New (Actual) Address |
| -------- | ----------- | ------------ | -------------------- |
| `main`   | 0x0000      | 0x400000     | 0x400000             |
| `printf` | 0x1000      | 0x400000     | 0x401000             |

---

## ⚙️ 3. Dynamic Linking at Runtime

If the program was **dynamically linked**, the loader invokes a special component — the **Dynamic Linker (ld-linux.so)** on Linux or the **Windows Loader**.

This step:

* Loads shared libraries (like `libc.so`, `libm.so`) into memory.
* Resolves symbol addresses by mapping library functions (like `printf`) to the program’s references.
* Updates the **Global Offset Table (GOT)** and **Procedure Linkage Table (PLT)** so the program can call these functions.

**Example Flow:**

1. Executable references `printf` → found in `libc.so`.
2. Loader maps `libc.so` into memory.
3. Dynamic linker patches the address of `printf` into the GOT/PLT entry.

✅ From now on, whenever `printf()` is called, control directly jumps to its address inside `libc.so`.

---

## ⚙️ 4. Memory Allocation and Process Setup

Before the program starts executing:

1. The loader sets up the **stack** (for function calls and local variables).
2. Initializes the **heap** (for dynamic memory like `malloc` or `new`).
3. Loads **environment variables** and command-line arguments (`argc`, `argv`).
4. Initializes **global/static variables** from the `.data` section.
5. Zero-initializes the `.bss` section (uninitialized data).

### 🧠 Memory Layout in a Running C++ Program

When the **Loader** loads the executable into memory, it organizes it into a structured **process memory layout**. This layout defines how the program's memory is divided into different regions, each serving a specific purpose.

The memory layout typically looks like this:

<img width="1000" height="800" alt="image" src="https://github.com/user-attachments/assets/2f241c2e-2876-4ec0-bdc2-f89d2872a205" />

---

#### 🧩 4.1. Text Segment (Code Segment)

* Contains **compiled machine instructions** of the program.
* It is **read-only** to prevent accidental modification of instructions.
* Typically shared among processes running the same program to save memory.

**Example:**

```c
int add(int a, int b) { return a + b; }
```

All the CPU instructions for this function live in the **text segment**.

---

#### 🧩 4.2. Initialized Data Segment (.data)

* Stores **global** and **static** variables that are explicitly initialized.
* Values are loaded from the executable file.
* This memory is both **readable and writable**.

**Example:**

```c
int count = 10;   // Stored in .data
static float pi = 3.14;
```

---

#### 🧩 4.3. Uninitialized Data Segment (BSS)

* Contains **global** and **static** variables** that are declared but not initialized**.
* Automatically initialized to **zero** at program startup.

**Example:**

```c
int total;      // Stored in BSS, initialized to 0
static int flag;  // Also stored in BSS
```

---

#### 🧩 4.4. Heap

* The **heap** is used for **dynamic memory allocation** during runtime.
* It grows **upward** toward higher memory addresses.
* Managed using functions like `malloc()`, `calloc()`, `free()` or `new` / `delete` in C++.

**Example:**

```cpp
int* arr = new int[100]; // Allocated on the heap
```

*If you don’t free heap memory, it remains allocated until the process terminates (memory leak).*

---

#### 🧩 4.5. Stack

* The **stack** is used for **function calls**, **local variables**, and **return addresses**.
* It grows **downward** (toward lower memory addresses).
* Automatically managed by the OS — freed when functions return.

**Example:**

```cpp
void func() {
    int x = 5;   // Stored on the stack
}
```

Each function call creates a **stack frame** containing local variables and bookkeeping info.

---

#### 🧩 4.6. Command-Line Arguments & Environment Variables

* Stored at the **top of the process address space**.
* Provide runtime configuration to the program.

**Example:**

```bash
./program input.txt
```

Here, `argc` and `argv` store these command-line arguments.

**Environment variables** (like `PATH`, `HOME`) are also stored here and accessible via `getenv()`.

---

## ⚙️ Static vs Dynamic Memory Layout

| Type                      | Sections        | Description                                            |
| ------------------------- | --------------- | ------------------------------------------------------ |
| **Static Memory Layout**  | Text, Data, BSS | Determined at compile & link time, fixed by the loader |
| **Dynamic Memory Layout** | Heap, Stack     | Determined during program execution, managed by OS     |

---

## ⚙️ Example Summary Table

| Segment   | Direction | Lifetime       | Typical Usage         | Example                  |
| --------- | --------- | -------------- | --------------------- | ------------------------ |
| **Text**  | Fixed     | Entire program | Code                  | Function definitions     |
| **Data**  | Fixed     | Entire program | Initialized globals   | `int x = 10;`            |
| **BSS**   | Fixed     | Entire program | Uninitialized globals | `int x;`                 |
| **Heap**  | Upward    | Dynamic        | Dynamic allocations   | `new`, `malloc()`        |
| **Stack** | Downward  | Function scope | Local variables       | `int x;` inside function |

---

## ⚙️ 5. Transfer of Control to the Program

Once everything is set up:

* The loader sets the **Program Counter (PC)** to the **entry point**.
* Execution begins from this address.

After `main()` returns, control goes back through `exit()` to the OS, releasing all allocated resources.

---

## Summary 

| Step | Stage            | Loader Task                               | Output/Effect             |
| ---- | ---------------- | ----------------------------------------- | ------------------------- |
| 1    | Load Executable  | Copy sections (.text, .data, etc.) to RAM | Program in memory         |
| 2    | Relocation       | Adjust addresses to base address          | Correct memory references |
| 3    | Dynamic Linking  | Load shared libraries                     | Functions resolved        |
| 4    | Memory Setup     | Prepare stack, heap, env                  | Program ready to run      |
| 5    | Transfer Control | Jump to `main()`                          | Program starts execution  |

---

## 🧱 Final Outcome

After the loader finishes:

* The program is fully placed in memory.
* All symbols and addresses are resolved.
* Stack and heap are ready.
* Execution officially begins.

✅ Your C++ program is now running.
