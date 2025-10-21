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

