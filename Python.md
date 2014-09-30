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
- The operators `is` and `is not` compare whether two objects are really the same object; this only matters for mutable objects like lists.

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
- The first statement of the function body can optionally be a string literal; this string literal is the function’s documentation string, or docstring, usually surrounded by `""" """`
- Whereas variable references first look in the local symbol table, then in the local symbol tables of enclosing functions, then in the global symbol table, and finally in the table of built-in names. Thus, global variables cannot be directly assigned a value within a function (unless named in a global statement), although they may be referenced.
- Arguments are passed using call by value (where the value is always an object reference, not the value of the object).
- A function definition introduces the function name in the current symbol table. The value of the function name has a type that is recognized by the interpreter as a user-defined function. This value can be assigned to another name which can then also be used as a function.
- `return` without an expression argument returns `None`. Even functions without a `return` statement returns `None`.

### Dafault Argument Value
- The default values are evaluated at the point of function definition in the defining scope, so that:
```
i = 5
def f(arg=i):
    print(arg)
i = 6
f()
# print 5
```
- The default value is evaluated only once. This makes a difference when the default is a mutable object:
```
def f(a, L=[]):
    L.append(a)
    return L
print(f(1))
# [1]
print(f(2))
# [1, 2]
print(f(3))
# [1, 2, 3]
```
- If you don’t want the default to be shared between subsequent calls, you can write the function like this instead:
```
def f(a, L=None):
    if L is None:
        L = []
    L.append(a)
    return L
```

### Keyword Arguments
- Functions can also be called using keyword arguments of the form `kwarg=value`.
- When a final formal parameter of the form `**name` is present, it receives a dictionary containing all keyword arguments except for those corresponding to a formal parameter.

### Arbitary Argument List
- These arguments will be wrapped up in a tuple. Before the variable number of arguments, zero or more normal arguments may occur.
- Normally, these variadic arguments will be last in the list of formal parameters, because they scoop up all remaining input arguments that are passed to the function. Any formal parameters which occur after the *args parameter are ‘keyword-only’ arguments, meaning that they can only be used as keywords rather than positional arguments.
- The reverse situation occurs when the arguments are already in a list or tuple but need to be unpacked for a function call requiring separate positional arguments. If they are not available separately, write the function call with the *-operator to unpack the arguments out of a list or tuple:
```
args = [3, 6]
range(*args)
```
- In the same fashion, dictionaries can deliver keyword arguments with the **-operator.

### Documentation String
```
>>> def my_function():
...     """Do nothing, but document it.
...
...     No, really, it doesn't do anything.
...     """
...     pass
...
>>> print(my_function.__doc__)
Do nothing, but document it.

    No, really, it doesn't do anything.
```

### Function Annotations
- Neither Python itself nor the standard library use function annotations in any way; this section just shows the syntax. Third-party projects are free to use function annotations for documentation, type checking, and other uses.
- Annotations are stored in the `__annotations__` attribute of the function as a dictionary and have no effect on any other part of the function. Parameter annotations are defined by a colon after the parameter name, followed by an expression evaluating to the value of the annotation. Return annotations are defined by a literal `->`, followed by an expression.
```
>>> def f(ham: 42, eggs: int = 'spam') -> "Nothing to see here":
...     print("Annotations:", f.__annotations__)
...     print("Arguments:", ham, eggs)
...
>>> f('wonderful')
Annotations: {'eggs': <class 'int'>, 'return': 'Nothing to see here', 'ham': 42}
Arguments: wonderful spam
```

## Control Flow
- In Python, like in C, any non-zero integer value is true; zero is false. The condition may also be a string or list value, in fact any sequence; anything with a non-zero length is true, empty sequences are false.
- If you need to modify the sequence you are iterating over while inside the loop (for example to duplicate selected items), it is recommended that you first make a copy. Iterating over a sequence does not implicitly make a copy. The slice notation makes this especially convenient:
```
>>> for w in words[:]:  # Loop over a slice copy of the entire list.
...     if len(w) > 6:
...         words.insert(0, w)
...
>>> words
['defenestrate', 'cat', 'window', 'defenestrate']
```
- In Python 3, `range()` returns an iterator rather than list.
- Loop statements may have an `else` clause; it's executed when the loop terminates through exhaustion of the list (with `for`) or when the condition becomes false (with `while`), but not when the loop is terminated by a `break` statement:
```
>>> for i in range(2):
...     print str(i)
... else:
...     print '4'
...
0
1
4
>>> for i in range(2):
...     print str(i)
...     break
... else:
...     print '4'
...
0
```
- A `try` statement's `else` clause runs when on exception occurs, and a loop's `else` clause runs when no `break` occurs.
- `pass` does nothing. It's commonly used for creating minimal classes or as a place-holder.

### Looping Techniques
- When looping through dictionaries, the key and corresponding value can be retrieved at the same time using the items() method.
```
>>>
>>> knights = {'gallahad': 'the pure', 'robin': 'the brave'}
>>> for k, v in knights.items():
...     print(k, v)
...
gallahad the pure
robin the brave
```
- When looping through a sequence, the position index and corresponding value can be retrieved at the same time using the enumerate() function.
```
>>> for i, v in enumerate(['tic', 'tac', 'toe']):
...     print(i, v)
...
0 tic
1 tac
2 toe
```
- To loop over two or more sequences at the same time, the entries can be paired with the zip() function.
```
>>> questions = ['name', 'quest', 'favorite color']
>>> answers = ['lancelot', 'the holy grail', 'blue']
>>> for q, a in zip(questions, answers):
...     print('What is your {0}?  It is {1}.'.format(q, a))
...
What is your name?  It is lancelot.
What is your quest?  It is the holy grail.
What is your favorite color?  It is blue.
```

# Data Structures

- Sequence objects may be compared to other objects with the same sequence type. The comparison uses lexicographical ordering:
```
(1, 2, 3)              < (1, 2, 4)
[1, 2, 3]              < [1, 2, 4]
'ABC' < 'C' < 'Pascal' < 'Python'
(1, 2, 3, 4)           < (1, 2, 4)
(1, 2)                 < (1, 2, -1)
(1, 2, 3)             == (1.0, 2.0, 3.0)
(1, 2, ('aa', 'ab'))   < (1, 2, ('abc', 'a'), 4)
```

## Lists
- `list.append(x)` is equivalent to `list[len(list):] = [x]`
- `list.extend(L)` is equivalent to `list[len(list):] = L`
- `list.pop([i])` remove the i-th item and return it. The parameter is optional. If no index is specified, it removes and returns the last item in the list.
- `list.copy()` returns a shallow copy of the list. Equivalent to `list[:]`.
- You can use a list as a stack by `append()` and `pop()`.
- List is not efficient for queue. `collections.deque` is designed to have fast appends and pops from both end:
```
>>> from collections import deque
>>> queue = deque(["Eric", "John", "Michael"])
>>> queue.append("Terry")
>>> queue.popleft()
'Eric'
>>> queue.appendleft("Graham")  # not useful for queue
```
- If index is negative, it is relative to the end of the string: `len(s) + idx`. But note that -0 is still 0.
```
>>> a = range(10)
>>> a[1:-5]
[1, 2, 3, 4]
>>> a[6:15]
[6, 7, 8, 9]
```

### List Comprehensions
- List comprehensions provide a concise way to create lists. Common applications are to make new lists where each element is the result of some operations applied to each member of another sequence or iterable, or to create a subsequence of those elements that satisfy a certain condition:
```
>>> [(x, y) for x in [1,2,3] for y in [3,1,4] if x != y]
[(1, 3), (1, 4), (2, 3), (2, 1), (2, 4), (3, 1), (3, 4)]
```
- The initial expression in a list comprehension can be any arbitrary expression, including another list comprehension.

### del
- `del a[:]` is equivalent to `a.clear()`, but `del a` delete the entire variable, referencing the name `a` hereafter is an error.

## Tuples and Sequences
- Tuples are immutable, and usually contain an heterogeneous sequence of elements that are accessed via unpacking or indexing (or even by attribute in the case of namedtuples). Lists are mutable, and their elements are usually homogeneous and are accessed by iterating over the list.
- Empty tuples are constructed by an empty pair of parentheses; a tuple with one item is constructed by following a value with a comma (it is not sufficient to enclose a single value in parentheses). Ugly, but effective:
```
empty = ()
singleton = 'hello',
x, y, z = (1, 2, 3)     # unpacking
```

## Sets
- Set operations:
```
>>> a = set('abracadabra')
>>> b = set('alacazam')
>>> a                                  # unique letters in a
{'a', 'r', 'b', 'c', 'd'}
>>> a - b                              # letters in a but not in b
{'r', 'd', 'b'}
>>> a | b                              # letters in either a or b
{'a', 'c', 'r', 'd', 'b', 'm', 'z', 'l'}
>>> a & b                              # letters in both a and b
{'a', 'c'}
>>> a ^ b                              # letters in a or b but not both
{'r', 'd', 'b', 'm', 'z', 'l'}
```
- Set comprehensions are also supported (but note that `{}` returns empty dictionary rather than empty set):
```
>>> a = {x for x in 'abracadabra' if x not in 'abc'}
>>> a
{'r', 'd'}
```

## Dictionaries
- Dictionaries are indexed by keys, which can be any immutable type, including tuples if they contain only strings, numbers, or tuples.
- The `dict()` constructor builds dictionaries directly from sequences of key-value pairs:
```
>>> dict([('sape', 4139), ('guido', 4127), ('jack', 4098)])
{'sape': 4139, 'jack': 4098, 'guido': 4127}
```
- In addition, dict comprehensions can be used to create dictionaries from arbitrary key and value expressions:
```
>>>
>>> {x: x**2 for x in (2, 4, 6)}
{2: 4, 4: 16, 6: 36}
```
- When the keys are simple strings, it is sometimes easier to specify pairs using keyword arguments:
```
>>> dict(sape=4139, guido=4127, jack=4098)
{'sape': 4139, 'jack': 4098, 'guido': 4127}
```

## Modules
- Definitions from a module can be imported into other modules or into the main module (interactive mode).
- The file name is the module name with the suffix **.py** appended. Within a module, the module’s name (as a string) is available as the value of the global variable `__name__`.
- If you intend to use a function often you can assign it to a local name:
```
fib = fibo.fib
fib(500)
```
- A module can contain executable statements as well as function definitions. These statements are intended to initialize the module. They are executed only the first time the module name is encountered in an import statement. (They are also run if the file is executed as a script.)
- Each module has its own private symbol table, which is used as the global symbol table by all functions defined in the module. There is a variant of the import statement that imports names from a module directly into the importing module’s symbol table.
```
from fibo import *
fib(500)
```
This imports all names except those beginning with an underscore `_`.

- For efficiency reasons, each module is only imported once per interpreter session. Therefore, if you change your modules, you must restart the interpreter – or, if it’s just one module you want to test interactively:
```
import imp
imp.reload(module_name)
```
- When the module is executed, it acts just as if you import it, but with the `__name__` set to `__main__`.
- When a module named **spam** is imported, the interpreter first searches for a built-in module with that name. If not found, it then searches for a file named **spam.py** in a list of directories given by the variable **sys.path**. **sys.path** is initialized from these locations:
 - The directory containing the input script (or the current directory when no file is specified).
 - PYTHONPATH (a list of directory names, with the same syntax as the shell variable PATH).
 - The installation-dependent default.
- To speed up loading modules, Python caches the compiled version of each module in the **__pycache__** directory under the name ***.pyc**. 
- You can use the `-O` or `-OO` switches on the Python command to reduce the size of a compiled module. The `-O` switch removes assert statements, the `-OO` switch removes both assert statements and __doc__ strings. "Optimized" modules hava a **.pyo** suffix and usually smaller.
- A program doesn’t run any faster when it is read from a .pyc or .pyo file than when it is read from a .py file; the only thing that’s faster about .pyc or .pyo files is the speed with which they are loaded.
- The variable `sys.path` is a list of strings that determines the interpreter’s search path for modules. It is initialized to a default path taken from the environment variable **PYTHONPATH**, or from a built-in default if **PYTHONPATH** is not set. You can modify it using standard list operations:
```
>>> import sys
>>> sys.path.append('/ufs/guido/lib/python')
```
- The built-in function `dir()` is used to find out which names a module defines. It returns a sorted list of strings. If you want a list of built-in functions and variables, use `dir('builtins')`

### Packages
- Packages are a way of structuring Python's module namespace by using "dotted module names".
- The **__init__.py** files are required to make Python treat the directories as containing packages. In the simplest case, **__init__.py** can just be an empty file, but it can also execute initialization code for the package or set the `__all__` variable.
- When the user writes `from package import *`:
 - if a package's **__init__.py** defines a list `__all__`, then the list of module names will be imported
 - if `__all__` is not defined, no submodule will be imported
- You can also write relative imports:
```
from . import echo
from ..filters import equalizer
```
- Note that relative imports are based on the name of the current module. Since the name of the main module is always "__main__", modules intended for use as the main module of a Python application must always use absolute imports.

# Input and Output
- 