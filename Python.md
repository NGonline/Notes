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
# -*- coding: utf-8 -*-
```
- In interactive mode, the last printed expression is assigned to the variable `_`. Don’t explicitly assign a value to it — you would create an independent local variable with the same name masking the built-in variable with its magic behavior.
- Python scriptes can be made directly executable by putting the line at the beginning:
```
#!/usr/bin/env python3.4
```

## Values and Types
- If you are not sure what type a value has, the interpreter can tell you: `type(1.7)`.
- There are two kinds of integers in Python 2: `int` and `long`. `sys.maxint` is platform-dependent, you need to append `L` to define a `long`. In Python 3, there is only `int`.
- Integer with a leading zero is octonary.
- `exec` is no longer a keyword in Python 3, but `nonlocal` became one.
- In Python 3, the result of `59/60` is a float. The new operator `//` performs floor division.
- For use cases which require exact decimal representation, try using the `decimal `module. Another form of exact arithmetic is supported by the `fractions` module which implements arithmetic based on rational numbers (so the numbers like 1/3 can be represented exactly).
- `round(n, d)` rounds `n` to the `d` digit. Values are rounded to the closest multiple of 10 to the power minus `d`.
- The `float.as_integer_ratio()` method expresses the value of a float as a fraction.
- Another helpful tool is the `math.fsum()` function which helps mitigate loss-of-precision during summation. It tracks “lost digits” as values are added onto a running total. That can make a difference in overall accuracy so that the errors do not accumulate to the point where they affect the final total:
```
>>>
>>> sum([0.1] * 10) == 1.0
False
>>> math.fsum([0.1] * 10) == 1.0
True
```
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
- `r'xxx'` means raw string without escaping. `r'\\'` is exactly two backslashes

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
- The reverse situation occurs when the arguments are already in a list or tuple but need to be unpacked for a function call requiring separate positional arguments. If they are not available separately, write the function call with the `*` operator to unpack the arguments out of a list or tuple:
```
args = [3, 6]
range(*args)
```
- In the same fashion, dictionaries can deliver keyword arguments with the `**`operator.

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

## Format
- The `str()` function is meant to return representations of values which are fairly human-readable, while `repr()` is meant to generate representations which can be read by the interpreter (or will force a `SyntaxError` if there is no equivalent syntax). For objects which don’t have a particular representation for human consumption, `str()` will return the same value as `repr()`. Many values, such as numbers or structures like lists and dictionaries, have the same representation using either function. Strings, in particular, have two distinct representations.
```
>>> s = 'hello world'
>>> str(s)
'hello world'
>>> repr(s)
"'hello world'"
```
- Different ways of formatting:
```
# right adjust, if the input is too long, it will be returned unchanged rather than truncated
print(str(x).rjust(4), str(y).rjust(4)) # print adds spaces between its arguments
print('{0:4d} {1:4d}'.format(x, y))

# zero padding
'-3.14'.zfill(7)

# usage of format()
'we are the {} who say "{}!"'.format('knights', 'Ni')
'This {food} is {adjective}.'.format(food='spam', adjective='absolutely horrible')
'The story of {0}, {1}, and {other}.'.format('Bill', 'Manfred', other='Georg')
```
- `!a` (apply ascii()), `!s` (apply str()) and `!r` (apply repr()) can be used to convert the value before it is formatted:
```
>>> print('The value of PI is approximately {}.'.format(math.pi))
The value of PI is approximately 3.14159265359.
>>> print('The value of PI is approximately {!r}.'.format(math.pi))
The value of PI is approximately 3.141592653589793.
```
- If you have a really long format string that you don’t want to split up, it would be nice if you could reference the variables to be formatted by name instead of by position:
```
>>> table = {'Sjoerd': 4127, 'Jack': 4098, 'Dcab': 8637678}
>>> print('Jack: {0[Jack]:d}; Sjoerd: {0[Sjoerd]:d}; Dcab: {0[Dcab]:d}'.format(table))
>>> print('Jack: {Jack:d}; Sjoerd: {Sjoerd:d}; Dcab: {Dcab:d}'.format(**table))
```
This is particularly useful in combination with the built-in function `vars()`, which returns a dictionary containing all local variables.

## Reading and Writing Files
- Mode can be 'r' when the file will only be read, 'w' for only writing (an existing file with the same name will be erased), and 'a' opens the file for appending; any data written to the file is automatically added to the end. 'r+' opens the file for both reading and writing. The mode argument is optional; 'r' will be assumed if it’s omitted. 'b' appended to the mode opens the file in binary mode.
- To read a file’s contents, call `f.read(size)`, which reads some quantity of data and returns it as a string or bytes object. size is an optional numeric argument. When size is omitted or negative, the entire contents of the file will be read and returned. If the end of the file has been reached, `f.read()` will return an empty string.
- The returned string of `readline()` includes the final `\n`.
- If you want to read all lines of a file in a list, you can also use `list(f)` or `f.readlines()`.
- `f.tell()` returns the current position (so does `f.write()`). To change the file object's position, use `f.seek(offset, from_what)`. A `from_what` value of 0 measures from the beginning of the file (default), 1 uses the current file position, and 2 uses the end of the file as the reference point.
- In text files (those opened without a b in the mode string), only seeks relative to the beginning of the file are allowed.
- It is good practice to use the with keyword when dealing with file objects:
```
with open('workfile', 'r') as f:
    read_data = f.read()
```

## Saving Structured Data with JSON
- View JSON string: `json.dumps(x)` or `json.dump(x,f)` (`f` is a text file)
- Decode the object: `x = json.load(f)` (`f` is a text file)

# Exceptions
- Basic exception handling:
```
try:
    f = open('myfile.txt')
    s = f.readline()
    i = int(s.strip())
except OSError as err:
    print("OS error: {0}".format(err))
except (RuntimeError, TypeError, ValueError):
    print("Could not convert data to an integer.")
except:
    print("Unexpected error:", sys.exc_info()[0])
    raise
```
- An optional else clause can follow all except clauses. It's useful for code that must be executed if the try clause does not raise an exception:
```
try:
    f = open(arg, 'r')
except IOError:
    print('cannot open', arg)
else:
    print(arg, 'has', len(f.readlines()), 'lines')
    f.close()
```
- The use of the else clause is better than adding additional code to the try clause because it avoids accidentally catching an exception that wasn’t raised by the code being protected by the try-except statement.
- The except clause may specify a variable after the exception name. The variable is bound to an exception instance with the arguments stored in instance.args. For convenience, the exception instance defines __str__() so the arguments can be printed directly without having to reference .args. One may also instantiate an exception first before raising it and add any attributes to it as desired:
```
try:
   raise Exception('spam', 'eggs')
except Exception as inst:
   print(type(inst))    # the exception instance
   print(inst.args)     # arguments stored in .args
   print(inst)          # __str__ allows args to be printed directly,
                        # but may be overridden in exception subclasses
   x, y = inst.args     # unpack args
   print('x =', x)
   print('y =', y)
```
- `raise` indicates the exception to be raised. This must be either an exception instance or an exception class (or empty for a re-raise).

### User-Defined Exceptions
- Exceptions should typically be derived from the `Exception` class, either directly or indirectly:
```
class MyError(Exception):
    """My exception
    
    Attributes:
        value -- just for output
    """
    
    def __init__(self, value):
        self.value = value
    def __str__(self):
        return repr(self.value)
```

### Clean-Up Actions
- A finally clause is always executed before leaving the try statement, whether an exception has occured or not:
```
def divide(x, y):
    try:
        result = x / y
    except ZeroDivisionError:
        print("division by zero!")
    else:
        print("result is", result)
    finally:
        print("executing finally clause")
```
- Some objects define standard clean-up actions to be undertaken when the object is no longer needed, regardless of whether or not the operation using the object succeeded or failed. The `with` statement allows objects like files to be used in a way that ensures they are always cleaned up promptly and correctly:
```
with open("myfile.txt") as f:
    for line in f:
        print(line, end="")
```
- Objects which, like files, provide predefined clean-up actions will indicate this in their documentation.

# Classes

## Scope and Namespace
- The statements executed by the top-level invocation of the interpreter, either read from a script file or interactively, are considered part of a module called `__main__`.
- The namespace containing the built-in names is created when the Python interpreter starts up, and is never deleted. The global namespace for a module is created when the module definition is read in; normally, module namespaces also last until the interpreter quits. The local namespace for a function is created when the function is called, and deleted when the function returns or raises an exception that is not handled within the function.
- Although scopes are determined statically, they are used dynamically. At any time during execution, there are at least three nested scopes whose namespaces are directly accessible:
 - the innermost scope, which is searched first, contains the local names
 - the scopes of any enclosing functions, which are searched starting with the nearest enclosing scope, contains non-local, but also non-global names
 - the next-to-last scope contains the current module’s global names
 - the outermost scope (searched last) is the namespace containing built-in names
- To rebind variables found outside of the innermost scope, the `nonlocal` statement can be used; if not declared `nonlocal`, those variable are read-only (an attempt to write to such a variable will simply create a new local variable in the innermost scope, leaving the identically named outer variable unchanged). `global` is similar, if not declared, the variable is read-only.
- A special quirk of Python is that – if no `global` statement is in effect – assignments to names always go into the innermost scope. Assignments do not copy data — they just bind names to objects. The same is true for deletions. In particular, `import` statements and function definitions bind the module or function name in the local scope.
- The `global` statement can be used to indicate that particular variables live in the global scope and should be rebound there; the `nonlocal` statement indicates that particular variables live in an enclosing scope and should be rebound there.
```
def scope_test():
    def do_local():
        spam = "local spam"
    def do_nonlocal():
        nonlocal spam
        spam = "nonlocal spam"
    def do_global():
        global spam
        spam = "global spam"
    spam = "test spam"
    do_local()
    print("After local assignment:", spam)
    do_nonlocal()
    print("After nonlocal assignment:", spam)
    do_global()
    print("After global assignment:", spam)

scope_test()
print("In global scope:", spam)

# After local assignment: test spam
# After nonlocal assignment: nonlocal spam
# After global assignment: nonlocal spam
# In global scope: global spam
```

## Class Objects
- Class objects support two kinds of operations: attribute references and instantiation:
```
class MyClass:
    """A simple example class"""
    def f(self):
        return 'hello world'
MyClass.f()
MyClass.__doc__
x = MyClass()
```
- When a class defines an `__init__()` method, class instantiation automatically invokes it.
- Often, the first argument of a method is called `self`. This is nothing more than a convention: the name self has absolutely no special meaning to Python.

## Instance Objects
- The only operations understood by instance objects are attribute references.
- Suppose `x` is an instance of `MyClass`. `x.f` is a valid method reference, since `MyClass.f` is a function, but `x.i` is not, since `MyClass.i` is not. But `x.f` is not the same thing as `MyClass.f` — it is a method object, not a function object.
- The call `x.f()` is exactly equivalent to `MyClass.f(x)`.
- Any function object that is a class attribute defines a method for instances of that class. It is not necessary that the function definition is textually enclosed in the class definition: assigning a function object to a local variable in the class is also ok:
```
def f1(self, x, y):
    return min(x, x+y)
    
class C:
    f = f1
    def g(self):
        return 'hello world'
    h = g
```
- Each value is an object, and therefore has a class. It is stored as `object.__class__`.

## Class and Instance Variables
- Instance variables are for data unique to each instance and class variables are for attributes and methods shared by all instances of the class:
```
class Dog:
    kind = 'canine'
    def __init__(self, name):
        self.name = name
```

## Inheritance
- The syntax for a derived class definition looks like:
```
class DerivedClassName(modname.BaseClassName):
    ...
    ...
```
- If a requested attribute is not found in the class, the search proceeds to look in the base class.
- All methods in Python are effectively `virtual`.
- There is a simple way to call the base class method directly: `BaseClassName.methodname(self, arguments)`.
- Python has two built-in functions that work with inheritance:
 - Use `isinstance()` to check an instance’s type: `isinstance(obj, int)` will be `True` only if `obj.__class__` is `int` or some class derived from `int`.
 - Use `issubclass()` to check class inheritance: `issubclass(bool, int)` is `True` since `bool` is a subclass of int. However, `issubclass(float, int)` is False since `float` is not a subclass of `int`.
- Python supports a form of multiple inheritance as well:
```
class DerivedClassName(Base1, Base2, Base3):
    ...
    ...
```
- For most purposes, in the simplest cases, you can think of the search for attributes inherited from a parent class as depth-first, left-to-right, not searching twice in the same class where there is an overlap in the hierarchy. In fact, it is slightly more complex than that; the method resolution order changes dynamically to support cooperative calls to `super()`.

## Private Variables
- There is a convention that is followed by most Python code: a name prefixed with an underscore (e.g. `_spam`) should be treated as a non-public part of the API.
- Since there is a valid use-case for class-private members (namely to avoid name clashes of names with names defined by subclasses), there is limited support for such a mechanism, called name mangling. Any identifier of the form `__spam` (at least two leading underscores, at most one trailing underscore) is textually replaced with `_classname__spam`. This mangling is done without regard to the syntactic position of the identifier, as long as it occurs within the definition of a class.
```
class Mapping:
    def __init__(self, iterable):
        self.items_list = []
        self.__update(iterable)

    def update(self, iterable):
        for item in iterable:
            self.items_list.append(item)

    __update = update   # private copy of original update() method

class MappingSubclass(Mapping):
    def update(self, keys, values):
        # provides new signature for update()
        # but does not break __init__()
        for item in zip(keys, values):
            self.items_list.append(item)
```
- Notice that code passed to `exec()` or `eval()` does not consider the classname of the invoking class to be the current class; this is similar to the effect of the global statement, the effect of which is likewise restricted to code that is byte-compiled together. The same restriction applies to `getattr()`, `setattr()` and `delattr()`, as well as when referencing `__dict__` directly.

## Odds and Ends
- An empty class definition can simply bund together a few named data items:
```
class Employee:
    pass
    
john = Employee()
john.name = 'John Doe'
john.dept = 'computer lab'
john.salary = 1000
```
- Instance method objects have attributes, too: `m.__self__` is the instance object with the method m(), and `m.__func__` is the function object corresponding to the method.

## User-Defined Exceptions
- There are two new valid (semantic) forms for the `raise` statement:
```
raise Class     # Class must be an instance of "type" or of a class derived from it
raise Instance
```
- A class in an `except` clause is compatible with an exception if it is the same class or a base class thereof:
```
class B(Exception):
    pass
class C(B):
    pass
class D(C):
    pass
    
for cls in [B, C, D]:
    try:
        raise cls()
    except B:
        print('B')
    except C:
        print('C')
    except D:
        print('D')
# it will print B, B, B
```
- When an error message is printed for an unhandled exception, the exception’s class name is printed, then a colon and a space, and finally the instance converted to a string using the built-in function `str()`.

## Iterators
- The `for` statement calls `iter()` on the container object. The function returns an iterator object that defines the method `__next__()` which accesses elements in the container one at a time. When there are no more elements, `__next__()` raises a `StopIteration` exception which tells the for loop to terminate. You can call the `__next__()` method using the `next()` built-in function.
```
s = 'abc'
it = iter(s)
next(it)
```
- It's easy to add iterator behavior to your classes:
```
class Reverse:
    """Iterator for looping over a sequence backwards"""
    def __init__(self, data):
        self.data = data
        self.index = len(data)
    def __iter__(self):
        return self
    def __next__(self):
        if self.index == 0:
            raise StopIteration
        self.index = self.index - 1
        return self.data[self.index]
```

### Generators
- Generators are a simple and powerful tool for creating iterators. They are written like regular functions but use the `yield` statement whenever they want to return data. Each time `next()` is called on it, the generator resumes where it left-off (it remembers all the data values and which statement was last executed):
```
def reverse(data):
    for index in range(len(data)-1, -1, -1):
        yield data[index]
```
- Another key feature is that the local variables and execution state are automatically saved between calls.
- Some simple generators can be coded succinctly as expressions using a syntax similar to list comprehensions but with parentheses instead of brackets. These expressions are designed for situations where the generator is used right away by an enclosing function:
```
xvec = [10, 20, 30]
yvec = [7, 5, 3]
sum(x*y for x,y in zip(xvec, yvec)) # dot product
```

# The Standard Library

## Operating System Interface
- The built-in `dir(os)` and `help(os)` functions are useful as interactive aids for working with large modules like `os`.
- For daily file and directory management tasks, the `shutil` module provides a higher lever interface that is easier to use:
```
import shutil
shutil.copyfile('data.db', 'archive.db')
shutil.move('/build/executables', 'installdir')
```
- The `glob` module provides a function for making file lists from directory wildcard searches:
```
import glob
glob.glob('*.py')
```
- The `getopt` module processes `sys.argv` using the conventions of the Unix `getopt()` function. More powerful and flexible command line processing is provided by the `argparse` module.
- The most direct way to terminate a script is to use `sys.exit()`.

## String Pattern Matching
- When only sinple capabilities are needed, string methods are preferred to `re` module because they are easier to read and debug.

## Mathematics
- The `random` module provides tools for making random selections:
```
random.choice(['apple', 'pear', 'banana'])
random.sample(range(100), 10)
random.randrange(6)
```

## Internet Access
- There are a number of modules for accessing the internet and processing internet protocols. Two of the simplest are `urllib.request` for retrieving data from URLs and `smtplib` for sending mail:
```
from urllib.request import urlopen
for line in urlopen('http://tycho.usno.navy.mil/cgi-bin/timer.pl'):
    line = line.decode('utf-8')  # Decoding the binary data to text.
    if 'EST' in line or 'EDT' in line:  # look for Eastern Time
        print(line)

import smtplib
server = smtplib.SMTP('localhost')
server.sendmail('soothsayer@example.org', 'jcaesar@example.org',
"""To: jcaesar@example.org
From: soothsayer@example.org

Beware the Ides of March.
""")
server.quit()
```

## Dates and Times
```
from datetime import date
now = date.today()
now.strftime("%m-%d-%y. %d %b %Y is a %A on the %d day of %B.")
birthday = date(1964, 7, 31)
age = now - birthday
print age.days
```

## Data Compression
- Common data archiving and compression formats are directly supported by modules including: `zlib`, `gzip`, `bz2`, `lzma`, `zipfile` and `tarfile`:
```
>>> import zlib
>>> s = b'witch which has which witches wrist watch'
>>> len(s)
41
>>> t = zlib.compress(s)
>>> len(t)
37
>>> zlib.decompress(t)
b'witch which has which witches wrist watch'
>>> zlib.crc32(s)
226805979
```

## Performance Measurement
- The `timeit` module quickly demonstrates a modest performance advantage:
```
>>> from timeit import Timer
>>> Timer('t=a; a=b; b=t', 'a=1; b=2').timeit()
0.57535828626024577
>>> Timer('a,b = b,a', 'a=1; b=2').timeit()
0.54962537085770791
```
- In contrast to `timeit`‘s fine level of granularity, the `profile` and `pstats` modules provide tools for identifying time critical sections in larger blocks of code.

## Quality Control
- The `doctest` module provides a tool for scanning a module and validating tests embedded in a program’s docstrings. Test construction is as simple as cutting-and-pasting a typical call along with its results into the docstring:
```
def average(values):
    """Computes the arithmetic mean of a list of numbers.

    >>> print(average([20, 30, 70]))
    40.0
    """
    return sum(values) / len(values)

import doctest
doctest.testmod()   # automatically validate the embedded tests
```
- The `unittest` module is not as effortless as the `doctest` module, but it allows a more comprehensive set of tests to be maintained in a separate file:
```
import unittest
class TestStatisticalFunctions(unittest.TestCase):
    def test_average(self):
        self.assertEqual(average([20, 30, 70]), 40.0)
        self.assertEqual(round(average([1, 5, 7]), 1), 4.3)
        with self.assertRaises(ZeroDivisionError):
            average([])
        with self.assertRaises(TypeError):
            average(20, 30, 70)

unittest.main() # Calling from the command line invokes all tests
```

## Output Formatting
- The `reprlib` module provides a version of `repr()` customized for abbreviated displays of large or deeply nested containers:
```
>>> import reprlib
>>> reprlib.repr(set('supercalifragilisticexpialidocious'))
"set(['a', 'c', 'd', 'e', 'f', 'g', ...])"
```
- The pprint module offers more sophisticated control over printing both built-in and user defined objects in a way that is readable by the interpreter:
```
>>> import pprint
>>> t = [[[['black', 'cyan'], 'white', ['green', 'red']], [['magenta',
...     'yellow'], 'blue']]]
...
>>> pprint.pprint(t, width=30)
[[[['black', 'cyan'],   #  adds line breaks and indentation
   'white',
   ['green', 'red']],
  [['magenta', 'yellow'],
   'blue']]]
```
- The `textwrap` module formats paragraphs of text to fit a given screen width.
- The `locale` module accesses a database of culture specific data formats. The grouping attribute of locale’s format function provides a direct way of formatting numbers with group separators:
```
>>> import locale
>>> locale.setlocale(locale.LC_ALL, 'English_United States.1252')
'English_United States.1252'
>>> conv = locale.localeconv()          # get a mapping of conventions
>>> x = 1234567.8
>>> locale.format("%d", x, grouping=True)
'1,234,567'
>>> locale.format_string("%s%.*f", (conv['currency_symbol'],
...                      conv['frac_digits'], x), grouping=True)
'$1,234,567.80'
```

## Templating
- This allows users to customize their applications without having to alter the application:
```
>>> from string import Template
>>> t = Template('${village}folk send $$10 to $cause.')
>>> t.substitute(village='Nottingham', cause='the ditch fund')
'Nottinghamfolk send $10 to the ditch fund.'
```
- The `substitute()` method raises a `KeyError` when a placeholder is not supplied in a dictionary or a keyword argument. For mail-merge style applications, user supplied data may be incomplete and the `safe_substitute()` method may be more appropriate — it will leave placeholders unchanged if data is missing:
```
>>> t = Template('Return the $item to $owner.')
>>> d = dict(item='unladen swallow')
>>> t.substitute(d)
Traceback (most recent call last):
  ...
KeyError: 'owner'
>>> t.safe_substitute(d)
'Return the unladen swallow to $owner.'
```
- Template subclasses can specify a custom delimiter. For example, a batch renaming utility for a photo browser may elect to use percent signs for placeholders such as the current date, image sequence number, or file format:
```
import time, os.path
photofiles = ['img_1074.jpg', 'img_1076.jpg', 'img_1077.jpg']
class BatchRename(Template):
    delimiter = '%'
format = "Ashley_%n%f"
t = BatchRename(format)
date = time.strftime('%d%b%y')
for i, filename in enumerate(photofiles):
    base, ext = os.path.splitext(filename)
    newname = t.substitute(d=date, n=i, f=ext)
    print('{0} --> {1}'.format(filename, newname))
    
# img_1074.jpg --> Ashley_0.jpg
# img_1076.jpg --> Ashley_1.jpg
# img_1077.jpg --> Ashley_2.jpg
```
- Another application for templating is separating program logic from the details of multiple output formats. This makes it possible to substitute custom templates for XML files, plain text reports, and HTML web reports.

## Binary Data Record Layouts
- The `struct` module provides `pack()` and `unpack()` functions for working with variable length binary record formats.

## Multi-Threading
- The following code shows how the high level `threading` module can run tasks in background while the main program continues to run:
```
import threading, zipfile

class AsyncZip(threading.Thread):
    def __init__(self, infile, outfile):
        threading.Thread.__init__(self)
        self.infile = infile
        self.outfile = outfile
    def run(self):
        f = zipfile.ZipFile(self.outfile, 'w', zipfile.ZIP_DEFLATED)
        f.write(self.infile)
        f.close()
        print('Finished background zip of:', self.infile)

background = AsyncZip('mydata.txt', 'myarchive.zip')
background.start()
print('The main program continues to run in foreground.')
background.join()    # Wait for the background task to finish
print('Main program waited until background was done.')
```
- The preferred approach to task coordination is to concentrate all access to a resource in a single thread and then use the `queue` module to feed that thread with requests from other threads. Applications using `Queue` objects for inter-thread communication and coordination are easier to design, more readable, and more reliable.

## Logging
- The `logging` module offers a full featured and flexible logging system.
```
import logging
logging.warning('Warning:config file %s not found', 'server.conf')
```
- By default, informational and debugging messages are suppressed and the output is sent to standard error.

## Weak References
- The `weakref` module provides tools for tracking objects without creating a reference. When the object is no longer needed, it is automatically removed from a weakref table and a callback is triggered for weakref objects. Typical applications include caching objects that are expensive to create:
```
import weakref, gc
class A:
    def __init__(self, value):
        self.value = value
    def __repr__(self):
        return str(self.value)

a = A(10)                   # create a reference
d = weakref.WeakValueDictionary()
d['primary'] = a            # does not create a reference
d['primary']                # fetch the object if it is still alive

del a                       # remove the one reference
gc.collect()                # run garbage collection right away

d['primary']                # entry was automatically removed
```

## Working with Lists
- The `array` module provides an `array()` object that is like a list that stores only homogeneous data and stores it more compactly.
```
from array import array
# an array of numbers stored as two byte unsigned binary numbers (typecode "H") rather than the usual 16 bytes per entry for regular lists of Python int objects
a = array('H', [4000, 10, 700, 22222])
```
- The `collections` module provides a `deque()` object that is like a list with faster appends and pops from the left side but slower lookups in the middle.
- `bisect` module provides functions for bisection:
```
import bisect
import random
random.seed(1)
l=[]
for i in range(1,15):
    r=random.randint(1,100)
    position=bisect.bisect_left(l,r)
    bisect.insort_left(l,r)
```
- The `heapq` module provides functions for implementing heaps based on regular lists.
```
>>> from heapq import heapify, heappop, heappush
>>> data = [1, 3, 5, 7, 9, 2, 4, 6, 8, 0]
>>> heapify(data)                      # rearrange the list into heap order
>>> heappush(data, -5)                 # add a new entry
>>> [heappop(data) for i in range(3)]  # fetch the three smallest entries
[-5, 0, 1]
```

## Decimal Floating Point Arithmetic
- The `decimal` module offers a `Decimal` datatype for decimal floating point arithmetic. Compared to the built-in float implementation of binary floating point, the class is especially helpful for:
 - financial applications and other uses which require exact decimal representation,
 - control over precision,
 - control over rounding to meet legal or regulatory requirements,
 - tracking of significant decimal places, or
 - applications where the user expects the results to match calculations done by hand.
```
>>> from decimal import *
>>> Decimal('1.00') % Decimal('.10')
Decimal('0.00')
>>> 1.00 % 0.10
0.09999999999999995
>>> sum([Decimal('0.1')]*10) == Decimal('1.0')
True
>>> sum([0.1]*10) == 1.0
False
>>> getcontext().prec = 36
>>> Decimal(1) / Decimal(7)
Decimal('0.142857142857142857142857142857142857')
```