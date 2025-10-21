#C++ Compilation process:
<img width="940" height="1283" alt="image" src="https://github.com/user-attachments/assets/8a9a4631-2142-4a0e-888f-75e43051e0af" />

## C Preprocessor (CPP)

### Overview

The **C Preprocessor** is a program that processes your source code in the beginning of compilation. It handles **directives** that begin with `#`, performing textual substitutions and code inclusion. Its output is a pure C code file that the compiler then compiles. The result is an expanded .i file (pure C code with all macros and includes expanded).

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

---
**Preprocessor → expands macros and includes → compiler compiles expanded code**
