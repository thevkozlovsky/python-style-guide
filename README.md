Python Style Guide
==================

  * [Python Language Rules](#python-language-rules)    
    * [Lint](#lint)
    * [Imports](#imports)
    * [Packages](#packages)
    * [Exceptions](#exceptions)
    * [Global variables](#global-variables)
    * [Nested/Local/Inner Classes and Functions](#nestedlocalinner-classes-and-functions)
    * [List Comprehensions](#list-comprehensions)
    * [Default Iterators and Operators](#default-iterators-and-operators)
    * [Generators](#generators)
    * [Lambda Functions](#lambda-functions)
    * [Conditional Expressions](#conditional-expressions)
    * [Default Argument Values](#default-argument-values)
    * [Properties](#properties)
    * [True/False evaluations](#truefalse-evaluations)
    * [Deprecated Language Features](#deprecated-language-features)
    * [Lexical Scoping](#lexical-scoping)
    * [Function and Method Decorators](#function-and-method-decorators)
    * [Threading](#threading)
    * [Power Features](#power-features)
  * [Python Style Rules](#python-style-rues)
    * [Semicolons](#semicolons)
    * [Line length](#line-length)
    * [Parentheses](#parentheses)
    * [Indentation](#indentation)
    * [Blank Lines](#blank-lines)
    * [Whitespace](#whitespace)
    * [Shebang Line](#shebang-line)
    * [Comments](#comments)
    * [Classes](#classes)
    * [Strings](#strings)
    * [Files and Sockets](#files-and-sockets)
    * [TODO Comments](#todo-comments)
    * [Imports formatting](#imports-formatting)
    * [Statements](#statements)
    * [Access Control](#access-control)
    * [Naming](#naming)
    * [Main](#main)
    
## Python Language Rules

### Lint

Run `pylint` over your code.

#### Definition
pylint is a tool for finding bugs and style problems in Python source code. It finds problems that are typically       caught by a compiler for less dynamic languages like C and C++. Because of the dynamic nature of Python, some          warnings may be incorrect; however, spurious warnings should be fairly infrequent.

#### Pros
Catches easy-to-miss errors like typos, using-vars-before-assignment, etc.

#### Cons
`pylint` isn't perfect. To take advantage of it, we'll need to sometimes: a) Write around it b) Suppress its            warnings or c) Improve it.

#### Decision
Make sure you run `pylint` on your code. Suppress warnings if they are inappropriate so that other issues are not hidden.

To suppress warnings, you can set a line-level comment:

    dict = 'something awful'  # Bad Idea... pylint: disable=redefined-builtin
    
pylint warnings are each identified by a alphanumeric code (`C0112`) and a symbolic name (`empty-docstring`). Prefer the symbolic names in new code or when updating existing code.

If the reason for the suppression is not clear from the symbolic name, add an explanation.

Suppressing in this way has the advantage that we can easily search for suppressions and revisit them.

You can get a list of pylint warnings by doing `pylint --list-msgs`. To get more information on a particular message, use `pylint --help-msg=C6409`.

Prefer `pylint: disable` to the deprecated older form `pylint: disable-msg`.

Unused argument warnings can be suppressed by using `_' as the identifier for the unused argument or prefixing the argument name with `unused_'. In situations where changing the argument names is infeasible, you can mention them at the beginning of the function. For example:

```python
    def foo(a, unused_b, unused_c, d=None, e=None):
        _ = d, e
        return a
```

==========

### Imports

Use `imports` for packages and modules only.

#### Definition

Reusability mechanism for sharing code from one module to another.

#### Pros

The namespace management convention is simple. The source of each identifier is indicated in a consistent way; `x.Obj` says that object `Obj` is defined in module `x`.

#### Cons

Module names can still collide. Some module names are inconveniently long.

#### Decision

Use `import x` for importing packages and modules. 
Use `from x import y` where `x` is the package prefix and `y` is the module name with no prefix. 
Use `from x import y as z` if two modules named `y` are to be imported or if `y` is an inconveniently long name.

For example the module `sound.effects.echo` may be imported as follows:

```python
from sound.effects import echo
...
echo.EchoFilter(input, output, delay=0.7, atten=4)
```

Do not use relative names in imports. Even if the module is in the same package, use the full package name. This helps prevent unintentionally importing a package twice.

==========



### Packages

Import each module using the full pathname location of the module.

#### Definition

...


#### Pros

Avoids conflicts in module names. Makes it easier to find modules.


#### Cons

Makes it harder to deploy code because you have to replicate the package hierarchy.


#### Decision

All new code should import each module by its full package name.

Imports should be as follows:

```python
# Reference in code with complete name.
import sound.effects.echo

# Reference in code with just module name (preferred).
from sound.effects import echo
```
==========



### Exceptions

Exceptions are allowed but must be used carefully.

#### Definition

Exceptions are a means of breaking out of the normal flow of control of a code block to handle errors or other exceptional conditions.


#### Pros

The control flow of normal operation code is not cluttered by error-handling code. It also allows the control flow to skip multiple frames when a certain condition occurs, e.g., returning from N nested functions in one step instead of having to carry-through error codes.


#### Cons

May cause the control flow to be confusing. Easy to miss error cases when making library calls.

#### Decision

Exceptions must follow certain conditions:

  * Raise exceptions like this: `raise MyException('Error message')` or `raise MyException`. Do not use the two-argument form (`raise MyException, 'Error message'`) or deprecated string-based exceptions (`raise 'Error message'`).
  * Modules or packages should define their own domain-specific base exception class, which should inherit from the built-in Exception class. The base exception for a module should be called `Error`.
```python
class Error(Exception):
    pass
```
  * Never use catch-all `except:` statements, or catch `Exception` or `StandardError`, unless you are re-raising the exception or in the outermost block in your thread (and printing an error message). Python is very tolerant in this regard and `except:` will really catch everything including misspelled names, sys.exit() calls, Ctrl+C interrupts, unittest failures and all kinds of other exceptions that you simply don't want to catch.
  * Minimize the amount of code in a `try/except` block. The larger the body of the `try`, the more likely that an exception will be raised by a line of code that you didn't expect to raise an exception. In those cases, the `try/except` block hides a real error.
  * Use the `finally` clause to execute code whether or not an exception is raised in the `try` block. This is often useful for cleanup, i.e., closing a file.
  * When capturing an exception, use `as` rather than a comma. For example:
```python
try:
    raise Error
except Error as error:
    pass
```

==========



### Global variables

Avoid global variables.

#### Definition
#### Pros
#### Cons
#### Decision

==========



### Nested/Local/Inner Classes and Functions

Nested/local/inner classes and functions are fine.

#### Definition
#### Pros
#### Cons
#### Decision

==========



### List Comprehensions

Okay to use for simple cases.

#### Definition
#### Pros
#### Cons
#### Decision

==========



### Default Iterators and Operators

Use default iterators and operators for types that support them, like lists, dictionaries, and files.

#### Definition
#### Pros
#### Cons
#### Decision

==========



### Generators

Use generators as needed.

#### Definition
#### Pros
#### Cons
#### Decision

==========



### Lambda Functions

Okay for one-liners.

#### Definition
#### Pros
#### Cons
#### Decision

==========



### Conditional Expressions

Okay for one-liners.

#### Definition
#### Pros
#### Cons
#### Decision

==========



### Default Argument Values

Okay in most cases.

#### Definition
#### Pros
#### Cons
#### Decision

==========



### Properties

Use properties for accessing or setting data where you would normally have used simple, lightweight accessor or setter methods.

#### Definition
#### Pros
#### Cons
#### Decision

==========



### True/False evaluations

Use the "implicit" false if at all possible.

#### Definition
#### Pros
#### Cons
#### Decision

==========



### Deprecated Language Features

Use string methods instead of the `string` module where possible. Use function call syntax instead of `apply`. Use list comprehensions and `for` loops instead of `filter` and `map` when the function argument would have been an inlined lambda anyway. Use `for` loops instead of `reduce`.

#### Definition
#### Pros
#### Cons
#### Decision

==========



### Lexical Scoping

Okay to use.

#### Definition
#### Pros
#### Cons
#### Decision

==========



### Function and Method Decorators

Use decorators judiciously when there is a clear advantage.

#### Definition
#### Pros
#### Cons
#### Decision

==========



### Threading

Do not rely on the atomicity of built-in types.

#### Definition
#### Pros
#### Cons
#### Decision

==========



### Power Features

Avoid these features.

#### Definition
#### Pros
#### Cons
#### Decision



## Python Style Rules

### Semicolons

Do not terminate your lines with semi-colons and do not use semi-colons to put two commands on the same line.

==========



### Line length

Maximum line length is 80 characters.

==========



### Parentheses

Use parentheses sparingly.

==========



### Indentation

Indent your code blocks with 4 spaces.

==========



### Blank Lines

Two blank lines between top-level definitions, one blank line between method definitions.

==========



### Whitespace

Follow standard typographic rules for the use of spaces around punctuation.

==========



### Shebang Line

Most `.py` files do not need to start with a `#!` line. Start the main file of a program with `#!/usr/bin/python` with an optional single digit 2 or 3 suffix per [PEP-394](http://www.python.org/dev/peps/pep-0394/).

==========



### Comments

Be sure to use the right style for module, function, method and in-line comments.

==========



### Classes

If a class inherits from no other base classes, explicitly inherit from `object`. This also applies to nested classes.

==========


### Strings

Use the `format` method or the `%` operator for formatting strings, even when the parameters are all strings. Use your best judgement to decide between `+` and `%` (or `format`) though.

==========



### Files and Sockets

Explicitly close files and sockets when done with them.

==========



### TODO Comments

Use `TODO` comments for code that is temporary, a short-term solution, or good-enough but not perfect.

==========


### Imports formatting

Imports should be on separate lines.

==========



### Statements

Generally only one statement per line.

==========



### Access Control

If an accessor function would be trivial you should use public variables instead of accessor functions to avoid the extra cost of function calls in Python. When more functionality is added you can use `property` to keep the syntax consistent.

==========



### Naming

`module_name`, `package_name`, `ClassName`, `method_name`, `ExceptionName`, `function_name`, `GLOBAL_CONSTANT_NAME`, `global_var_name`, `instance_var_name`, `function_parameter_name`, `local_var_name`.

==========



### Main

Even a file meant to be used as a script should be importable and a mere import should not have the side effect of executing the script's main functionality. The main functionality should be in a main() function.

==========

