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
- In interactive mode, the last printed expression is assigned to the variable `_`. Don’t explicitly assign a value to it — you would create an independent local variable with the same name masking the built-in variable with its magic behavior.

## Values and Types
- If you are not sure what type a value has, the interpreter can tell you: `type(1.7)`.
- Integer with a leading zero is octonary.
- `exec` is no longer a keyword in Python 3, but `nonlocal` became one.
- In Python 3, the result of `59/60` is a float. The new operator `//` performs floor division.
- If you don’t want characters prefaced by `\` to be interpreted as special characters, you can use raw strings by adding an `r` before the first quote.

### String
- String literals can span multiple lines. One way is using triple-quotes: `"""..."""` or `'''...'''`. End of lines are automatically included in the string, but it’s possible to prevent this by adding a `\` at the end of the line:
```
print("""\
Usage: thingy [OPTIONS]
     -h                        Display this usage message
     -H hostname               Hostname to connect to
""")
# result is:
# Usage: thingy [OPTIONS]
#      -h                        Display this usage message
#      -H hostname               Hostname to connect to
```
- Two or more string literals (i.e. the ones enclosed between quotes) next to each other are automatically concatenated. This only works with two literals though, not with variables or expressions.
```
>>> 'Py' 'thon'
'Python'
```
- Attempting to use a index that is too large will result in an error. However, out of range slice indexes are handled gracefully when used for slicing.

### List
- All slice operations return a new list containing the requested elements. This means that the following slice returns a new (shallow) copy of the list.

## Functions
- `int()` can convert floating-point values to integers, but it doesn't round off; it chops of the fraction part.
- `import math` creates a module object named math.
- Defining a function creates a variable with the same name. The value of it is a function object, which has type `function`.

### Control Flow
- In Python, like in C, any non-zero integer value is true; zero is false. The condition may also be a string or list value, in fact any sequence; anything with a non-zero length is true, empty sequences are false.
- 