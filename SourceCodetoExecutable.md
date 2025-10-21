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

#2. Compiler

A **compiler** converts preprocessed high-level language code into low-level machine code that the computer can execute. The compilation process occurs in **two main phases**:

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

When the Assembler generates an object file, it still doesn’t know where in memory the program will actually be loaded. This uncertainty creates a problem: instructions that refer to addresses (like variables or jump labels) can’t yet be filled with real memory addresses. To handle this, the assembler builds a Relocation Table — a list of all memory addresses that need to be “fixed up” later by the Linker and Loader.

Imagine you write a small assembly program:

```asm
MOV EAX, [a]
ADD EAX, [b]
MOV [sum], EAX
```

When the assembler translates this to machine code, it doesn’t know where variables `a`, `b`, and `sum` will exist in memory. It leaves placeholders instead of real addresses.

#### ⚙️ What the Assembler Does

The assembler:

1. Writes machine instructions with placeholder addresses.
2. Creates **relocation entries** for every instruction that refers to a symbol whose address is not yet known.

**Example machine code:**

```
Offset   Instruction
0x0000   MOV EAX, [????]   ; address of a unknown
0x0006   ADD EAX, [????]   ; address of b unknown
0x000C   MOV [????], EAX   ; address of sum unknown
```
Here, the `????` means “to be filled later.”

#### 📦 Example Relocation Table

| Offset (Location) | Type     | Symbol | Description                                       |
| ----------------- | -------- | ------ | ------------------------------------------------- |
| 0x0001            | R_X86_32 | a      | Memory address of variable `a` needs to be filled |
| 0x0007            | R_X86_32 | b      | Address of `b` must be resolved                   |
| 0x000D            | R_X86_32 | sum    | Address of `sum` to be patched                    |


#### 🧠 Types of Relocation Entries

| Type                      | Meaning                                             | Example                       |
| ------------------------- | --------------------------------------------------- | ----------------------------- |
| **Absolute (R_X86_32)**   | Needs the full physical address replaced later      | Accessing global variable `a` |
| **Relative (R_X86_PC32)** | Needs an offset relative to the current instruction | For jumps like `JMP end`      |

**Example:**

```asm
JMP end   ; assembler doesn’t know how far to jump → linker will adjust
```
---

### 5. **Machine Code Generation**

* The assembler produces binary machine instructions from parsed assembly.
* Each instruction is encoded with the exact bit pattern recognized by the CPU.

The generated **object file (.o)** contains:

* Machine instructions (binary form)
* Symbol table
* Relocation table
* Debug information (optional)

**Example Layout of Object File:**

```
Header
Text Section (machine code)
Data Section (constants, variables)
Symbol Table
Relocation Table
Debug Info
```

---

## 📦 Example Flow

### Input (Assembly Code `Hello.s`):

```asm
section .data
a dd 10
b dd 20
sum dd 0

section .text
global _start
_start:
    mov eax, [a]
    add eax, [b]
    mov [sum], eax
```

### Output (Object File `Hello.o`):

```
Binary representation:
  B8 0A 00 00 00   ; mov eax, [a]
  03 05 14 00 00 00 ; add eax, [b]
  A3 18 00 00 00   ; mov [sum], eax
```

Includes symbol table and relocation entries for `a`, `b`, and `sum`.

---

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

