# Introductions

## What is a Compiler

* Recognise **legal** (and **illegal**) programs
* Generate **correct**, hopefully **efficient**, code

## Typical Structure of a Compiler
* Has a Front-end and a Back-end
* COMP3131 will only focus on the Front-end
    * **Scanner** (Assignment 1) -> **Tokens**
    * **Parser** (Assignment 2 & 3) -> **AST**
    * **Semantic** Analyser (Assignment 4) -> **Decorated AST**
    * **Code Generator** (Assignment 5) -> **Jasmin code**

<br></br>

![overview](../imgs/example.png)

## Lexical Analysis

### Scanner
* Groups character into **tokens** - the basic unit of syntax
    ```position = initial + rate * 60```
    becomes
    ```
    1. The identifier position
    2. The assignment operator = 
    3. The identifier initial
    4. The plus sign
    5. The identifier rate
    6. The multiplication sign
    7. The integer constant 60.
    ```
* Character string forming a token is a **lexeme**
* Eliminates white spaces (blanks, tabs and returns) and comments as they are only useful for the programmer and not the rest of the compiler modules

## Syntax Analsis

### Parser

* Groups tokens into **grammatical phrases**
* Represents grammatical phases as an **Abstract Syntax Tree**
* Produces meaningful **error messages**
* Attempts **error detection** and **recovery**
* The syntax of a language is typically specified by a **CFG** (Context-Free-Grammar)

## Semantic Analysis

### Semantic Analyser

* Checks the program for **semantics errors**
    * variable **used before defined**
    * operands called with **incompatible types**
    * procedures called with **incorrect number and types of arguments**
* An important task for the Semantic Analyser is to perform **type checking**
    * For example a real cannot be used to index into an array
    * Allow type conversions when some operand coercions are permitted
        * 2.0 to 2 (Real to Integer)

## Intermediate Code Generation

### (Intermediate) Code Generator
* Used to generate an explicit **Intermediate Representation**
    * We used **Abstract Syntax Tree** representation in COMP3131
* Important **IR** properties
    * ease of generation
    * ease of translation into machine instructions
* Small decisions in the **IR design** may have **major effects on the speed and effectiveness** of the compiler
* Some popular IRs are ...
    * **ASTs**
    * **DAGs**
    * **Postfix Notation**
    * **Thress Address Code**