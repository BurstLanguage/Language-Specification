# Burst
## Language Specification

## Contents
 - [Introduction](#introduction)
 - [Lifecycle of a Burst program](#lifecycle-of-a-burst-program)
 - [Tokenization and Lexical Analysis](Lexer.md)
    - [Tokenization](Lexer.md#tokenization)
    - [Lexical Analysis](Lexer.md#lexical-analysis)

### Introduction
The "Burst" language is a general-purpose interpreted programming language,
designed with C programmers in mind, meaning its syntax and functionality
shouldn’t differ too much from that of the C language.

**Disclaimer**: This specification is still in its very early stages, therefore
nothing contained within this document is final, unless otherwise noted.

### Lifecycle of a Burst program
Programs written using the Burst language generally follow the lifecycle that is
outlined below:

 1. User writes some Burst code.
 2. Path to file(s) with Burst code written by user, is sent to the Burst
 interpreter.
    - The Burst interpreter attempts to open each file that it was given paths
    to.
    - The Burst interpreter calls upon its lexer, and attempts to tokenize the
    entire file.
    - The Burst interpreter calls upon its type checker, to validate the types
    of each expression found within the program.
    - The Burst interpreter calls upon its parser, to generate an Abstract
    Syntax Tree for the current file.
    - The Burst interpreter attempts to interpret the previously generated
    Abstract Syntax Tree.
    - The Burst interpreter exits.