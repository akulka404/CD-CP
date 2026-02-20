# CD-CP — Compiler Design Course Project

CD-CP is a compiler front-end project that implements two forms of **intermediate code generation** for a subset of the C programming language. It is built using **Flex** (lexical analysis) and **YACC/Bison** (syntax analysis and code generation).

---

## Table of Contents

- [Project Overview](#project-overview)
- [Components](#components)
  - [Three Address Code](#three-address-code)
  - [Syntax Tree (AST)](#syntax-tree-ast)
- [Prerequisites](#prerequisites)
- [Build & Run Instructions](#build--run-instructions)
  - [Three Address Code](#building-and-running-three-address-code)
  - [Syntax Tree (AST)](#building-and-running-syntax-tree)
- [Supported Language Features](#supported-language-features)
- [Test Cases](#test-cases)

---

## Project Overview

This project takes a C source file as input, performs lexical and syntactic analysis, and produces:

1. **Three Address Code (TAC)** — a flat sequence of simple instructions (similar to assembly), along with a **Symbol Table** and a **Constant Table**.
2. **Abstract Syntax Tree (AST)** — an ASCII-art tree diagram that visually represents the hierarchical structure of the parsed program.

Both components share a similar tool chain: a Flex scanner for tokenization and a YACC/Bison parser for grammar-driven code generation.

---

## Components

### Three Address Code

Located in the `Three Address Code/` directory.

- **`scanner.l`** — Flex lexer that tokenizes keywords, identifiers, constants, operators, and pre-processor directives. It also maintains a **Symbol Table** and a **Constant Table** during scanning.
- **`parser.y`** — YACC/Bison parser that enforces C grammar rules and emits three-address instructions. It performs:
  - Scope checking and duplicate detection
  - Type checking for assignments and return statements
  - Code generation for arithmetic/logical expressions, assignments, function declarations and calls, and control-flow constructs
- **`y.tab.c` / `y.tab.h`** — Generated parser source and header.
- **`lex.yy.c`** — Generated scanner source.
- **`tests/`** — Ten sample C programs used to validate the generator.
- **`Screenshots/`** — Screenshots of the expected output for each test case.

**Output:** For a valid input file the tool prints:
- Three-address instructions to `stdout`
- Status message (`Parsing Complete - Valid`)
- Symbol Table
- Constant Table

### Syntax Tree (AST)

Located in the `Syntax Tree/` directory.

- **`AST.l`** — Flex lexer for a subset of C (data types, identifiers, literals, operators, control structures, struct, and selected standard-library headers).
- **`AST.y`** — YACC/Bison parser that builds an in-memory AST using three node types:
  - `typeCon` — constant nodes
  - `typeId` — identifier nodes
  - `typeOpr` — operator/statement nodes
- **`graph.c`** — Renders the AST as an ASCII-art diagram to `stdout`.
- **`header.h`** — Shared type definitions (`nodeType`, `conNodeType`, `idNodeType`, `oprNodeType`).
- **`input.c`** — A sample C input file for manual testing.
- **`AST`** — Pre-built binary (Unix/Linux).

---

## Prerequisites

| Tool | Purpose |
|------|---------|
| `flex` | Generates the C lexer from `.l` files |
| `bison` / `yacc` | Generates the C parser from `.y` files |
| `gcc` | Compiles the generated C sources |

Install on Debian/Ubuntu:
```bash
sudo apt-get install flex bison gcc
```

---

## Build & Run Instructions

### Building and Running Three Address Code

```bash
cd "Three Address Code"

# 1. Generate the lexer
flex scanner.l

# 2. Generate the parser
yacc -d parser.y

# 3. Compile
gcc y.tab.c lex.yy.c -w -o tac

# 4. Run against a C source file
./tac <filename>
```

**Example:**
```bash
./tac tests/test1.c
```

### Building and Running Syntax Tree

```bash
cd "Syntax Tree"

# 1. Generate the lexer
flex AST.l

# 2. Generate the parser
yacc -d AST.y

# 3. Compile (include graph.c for ASCII tree rendering)
gcc y.tab.c lex.yy.c graph.c -w -o ast

# 4. Run against a C source file (input is read from stdin)
./ast < input.c
```

---

## Supported Language Features

Both components support a subset of C including:

- **Data types:** `int`, `float`, `char`, `void`, `double`, `long`, `short`, `signed`, `unsigned`
- **User-defined types:** `struct`
- **Variable declarations** with optional initialization (scalars and arrays)
- **Arithmetic operators:** `+`, `-`, `*`, `/`, `%`
- **Relational operators:** `<`, `>`, `<=`, `>=`, `==`, `!=`
- **Logical operators:** `&&`, `||`, `!`
- **Assignment operators:** `=`, `+=`, `-=`, `*=`, `/=`, `%=`
- **Increment/Decrement:** `++`, `--`
- **Control flow:** `if`/`else`, `for`, `while`, `do`/`while`
- **Functions:** declarations, definitions, calls (with argument/parameter checking in TAC)
- **`return`** and **`break`** statements
- **Standard library headers:** `stdio.h`, `stdlib.h`, `math.h`, `string.h`, `time.h`
- **Pre-processor directives:** `#include`, `#define`

---

## Test Cases

The `Three Address Code/tests/` directory contains ten C test programs (`test1.c` – `test10.c`) covering:

| Test | Description |
|------|-------------|
| `test1.c` | `for` loop, `while` loop |
| `test2.c` – `test10.c` | Various combinations of declarations, expressions, control flow, and function calls |

Corresponding expected output screenshots are available in `Three Address Code/Screenshots/`.
