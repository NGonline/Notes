[TOC]

# Python

# Philosophy
- Two kinds of programs process high-level languages into low-level languages. Interpreters read a high-level program and execute it a little at a time. Compilers read the program and translate it completely into the object code (or the executable) before the program starts running.
- Python programs are executed by an interpreter. There are two ways to use the interpreter: interactive mode and script mode.

# Basics

## Python Interpreter
- Typing an end-of-file character (Control-D on Unix, Control-Z on Windows) at the primary prompt causes the interpreter to exit with a zero exit status.
- A second way of starting the interpreter is `python -c command [arg] ...`, which executes the statement(s) in command.
- Some Python modules are also useful as scripts. These can be invoked using `python -m module [arg] ...`, which executes the source file for module as if you had spelled out its full name on the command line.
- When a script file is used, it is sometimes useful to be able to run the script and enter interactive mode afterwards. This can be done by passing `-i` before the script.
- when no script and no arguments are given, sys.argv[0] is an empty string. When the script name is given as `-` (meaning standard input), sys.argv[0] is set to '-'. When `-c` command is used, `sys.argv[0]` is set to '-c'. When `-m` module is used, `sys.argv[0]` is set to the full name of the located module.
- It is also possible to specify a different encoding for source files. In order to do this, put one more special comment line right after the #! line to define the source file encoding:
```
# -*- coding: encoding -*-
```

## Values and Types
- If you are not sure what type a value has, the interpreter can tell you: `type(1.7)`.
- Integer with a leading zero is octonary.
- `exec` is no longer a keyword in Python 3, but `nonlocal` became one.
- In Python 3, the result of `59/60` is a float. The new operator `//` performs floor division.

## Functions
- `int()` can convert floating-point values to integers, but it doesn't round off; it chops of the fraction part.
- `import math` creates a module object named math.
- Defining a function creates a variable with the same name. The value of it is a function object, which has type `function`.
