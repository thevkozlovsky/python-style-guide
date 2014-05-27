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

Variables that are declared at the module level.

#### Pros

Occasionally useful.

#### Cons

Has the potential to change module behavior during the import, because assignments to module-level variables are done when the module is imported.

#### Decision

Avoid global variables in favor of class variables. Some exceptions are:

  * Default options for scripts.
  * Module-level constants. For example: `PI = 3.14159`. Constants should be named using all caps with underscores; see [Naming](#naming) below.
  * It is sometimes useful for globals to cache values needed or returned by functions.
  * If needed, globals should be made internal to the module and accessed through public module level functions; see [Naming](#naming) below.
  
==========



### Nested/Local/Inner Classes and Functions

Nested/local/inner classes and functions are fine.

#### Definition

A class can be defined inside of a method, function, or class. A function can be defined inside a method or function. Nested functions have read-only access to variables defined in enclosing scopes.

#### Pros

Allows definition of utility classes and functions that are only used inside of a very limited scope. Very [ADT](http://en.wikipedia.org/wiki/Abstract_data_type)-y.

#### Cons

Instances of nested or local classes cannot be pickled.

#### Decision

They are fine.

==========



### List Comprehensions

Okay to use for simple cases.

#### Definition

List comprehensions and generator expressions provide a concise and efficient way to create lists and iterators without resorting to the use of `map()`, `filter()`, or `lambda`.

#### Pros

Simple list comprehensions can be clearer and simpler than other list creation techniques. Generator expressions can be very efficient, since they avoid the creation of a list entirely.

#### Cons

Complicated list comprehensions or generator expressions can be hard to read.

#### Decision

Okay to use for simple cases. Each portion must fit on one line: mapping expression, `for` clause, filter expression. Multiple `for` clauses or filter expressions are not permitted. Use loops instead when things get more complicated.

**Yes**
```python
  result = []
  for x in range(10):
      for y in range(5):
          if x * y > 10:
              result.append((x, y))

  for x in xrange(5):
      for y in xrange(5):
          if x != y:
              for z in xrange(5):
                  if y != z:
                      yield (x, y, z)

  return ((x, complicated_transform(x))
          for x in long_generator_function(parameter)
          if x is not None)

  squares = [x * x for x in range(10)]

  eat(jelly_bean for jelly_bean in jelly_beans
      if jelly_bean.color == 'black')
```

**No**
```python
  result = [(x, y) for x in range(10) for y in range(5) if x * y > 10]

  return ((x, y, z)
          for x in xrange(5)
          for y in xrange(5)
          if x != y
          for z in xrange(5)
          if y != z)
```
==========



### Default Iterators and Operators

Use default iterators and operators for types that support them, like lists, dictionaries, and files.

#### Definition

Container types, like dictionaries and lists, define default iterators and membership test operators ("in" and "not in").

#### Pros

The default iterators and operators are simple and efficient. They express the operation directly, without extra method calls. A function that uses default operators is generic. It can be used with any type that supports the operation.

#### Cons

You can't tell the type of objects by reading the method names (e.g. has_key() means a dictionary). This is also an advantage.

#### Decision

Use default iterators and operators for types that support them, like lists, dictionaries, and files. The built-in types define iterator methods, too. Prefer these methods to methods that return lists, except that you should not mutate a container while iterating over it.

**Yes**
```python
for key in adict: ...
if key not in adict: ...
if obj in alist: ...
for line in afile: ...
for k, v in dict.iteritems(): ...
```

**No**
```python
for key in adict.keys(): ...
if not adict.has_key(key): ...
for line in afile.readlines(): ...
```
==========



### Generators

Use generators as needed.

#### Definition

A generator function returns an iterator that yields a value each time it executes a yield statement. After it yields a value, the runtime state of the generator function is suspended until the next value is needed.

#### Pros

Simpler code, because the state of local variables and control flow are preserved for each call. A generator uses less memory than a function that creates an entire list of values at once.

#### Cons

None.

#### Decision

Fine. Use "Yields:" rather than "Returns:" in the doc string for generator functions.

==========



### Lambda Functions

Okay for one-liners.

#### Definition

Lambdas define anonymous functions in an expression, as opposed to a statement. They are often used to define callbacks or operators for higher-order functions like `map()` and `filter()`.

#### Pros

Convenient.

#### Cons

Harder to read and debug than local functions. The lack of names means stack traces are more difficult to understand. Expressiveness is limited because the function may only contain an expression.

#### Decision

Okay to use them for one-liners. If the code inside the lambda function is any longer than 60–80 chars, it's probably better to define it as a regular (nested) function.

For common operations like multiplication, use the functions from the `operator` module instead of lambda functions. For example, prefer operator.mul to `lambda x, y: x * y`.

==========



### Conditional Expressions

Okay for one-liners.

#### Definition

Conditional expressions are mechanisms that provide a shorter syntax for if statements. For example: `x = 1 if cond else 2`.

#### Pros

Shorter and more convenient than an if statement.

#### Cons

May be harder to read than an if statement. The condition may be difficult to locate if the expression is long.

#### Decision

Okay to use for one-liners. In other cases prefer to use a complete if statement.

==========



### Default Argument Values

Okay in most cases.

#### Definition

You can specify values for variables at the end of a function's parameter list, e.g., `def foo(a, b=0)`:. If `foo` is called with only one argument, `b` is set to `0`. If it is called with two arguments, `b` has the value of the second argument.

#### Pros

Often you have a function that uses lots of default values, but—rarely—you want to override the defaults. Default argument values provide an easy way to do this, without having to define lots of functions for the rare exceptions. Also, Python does not support overloaded methods/functions and default arguments are an easy way of "faking" the overloading behavior.

#### Cons

Default arguments are evaluated once at module load time. This may cause problems if the argument is a mutable object such as a list or a dictionary. If the function modifies the object (e.g., by appending an item to a list), the default value is modified.

#### Decision

Okay to use with the following caveat:

Do not use mutable objects as default values in the function or method definition.

**Yes**
```python
def foo(a, b=None):
  if b is None:
    b = []
```

**No**
```python
def foo(a, b=[]):
  ...
def foo(a, b=time.time()):  # The time the module was loaded???
  ...
def foo(a, b=FLAGS.my_thing):  # sys.argv has not yet been parsed...
  ...
```

==========



### Properties

Use properties for accessing or setting data where you would normally have used simple, lightweight accessor or setter methods.

#### Definition

A way to wrap method calls for getting and setting an attribute as a standard attribute access when the computation is lightweight.

#### Pros

Readability is increased by eliminating explicit get and set method calls for simple attribute access. Allows calculations to be lazy. Considered the Pythonic way to maintain the interface of a class. In terms of performance, allowing properties bypasses needing trivial accessor methods when a direct variable access is reasonable. This also allows accessor methods to be added in the future without breaking the interface.

#### Cons

Properties are specified after the getter and setter methods are declared, requiring one to notice they are used for properties farther down in the code (except for readonly properties created with the `@property` decorator - see below). Must inherit from `object`. Can hide side-effects much like operator overloading. Can be confusing for subclasses.

#### Decision

Use properties in new code to access or set data where you would normally have used simple, lightweight accessor or setter methods. Read-only properties should be created with the `@property` [decorator](#function-and-method-decorators).

Inheritance with properties can be non-obvious if the property itself is not overridden. Thus one must make sure that accessor methods are called indirectly to ensure methods overridden in subclasses are called by the property (using the Template Method DP).

**Yes**
```python
import math

class Square(object):
    """A square with two properties: a writable area and a read-only perimeter.

    To use:
    >>> sq = Square(3)
    >>> sq.area
    9
    >>> sq.perimeter
    12
    >>> sq.area = 16
    >>> sq.side
    4
    >>> sq.perimeter
    16
    """

    def __init__(self, side):
     self.side = side

    def __get_area(self):
     """Calculates the 'area' property."""
     return self.side ** 2

    def ___get_area(self):
     """Indirect accessor for 'area' property."""
     return self.__get_area()

    def __set_area(self, area):
     """Sets the 'area' property."""
     self.side = math.sqrt(area)

    def ___set_area(self, area):
     """Indirect setter for 'area' property."""
     self.__set_area(area)

    area = property(___get_area, ___set_area,
                 doc="""Gets or sets the area of the square.""")

    @property
    def perimeter(self):
     return self.side * 4
     
```

==========



### True/False evaluations

Use the "implicit" false if at all possible.

#### Definition
#### Pros
#### Cons
#### Decision

==========



### Deprecated Language Features



#### Definition

Python evaluates certain values as `false` when in a boolean context. A quick "rule of thumb" is that all "empty" values are considered `false` so `0, None, [], {}, ''` all evaluate as `false` in a boolean context.

#### Pros

Conditions using Python booleans are easier to read and less error-prone. In most cases, they're also faster.

#### Cons

May look strange to C/C++ developers.

#### Decision

Use the "implicit" false if at all possible, e.g., `if foo:` rather than `if foo != []:`. There are a few caveats that you should keep in mind though:

* Never use `==` or `!=` to compare singletons like None. Use `is` or `is not`.
* Beware of writing `if x:` when you really mean `if x is not None:`—e.g., when testing whether a variable or argument that defaults to `None` was set to some other value. The other value might be a value that's false in a boolean context!
* Never compare a boolean variable to `False` using `==`. Use `if not x:` instead. If you need to distinguish `False` from `None` then chain the expressions, such as `if not x and x is not None:`.
* For sequences (strings, lists, tuples), use the fact that empty sequences are false, so `if not seq:` or `if seq:` is preferable to `if len(seq):` or `if not len(seq):`.
* When handling integers, implicit false may involve more risk than benefit (i.e., accidentally handling `None` as 0). You may compare a value which is known to be an integer (and is not the result of `len()`) against the integer 0.

**Yes**
```python
if not users:
    print 'no users'

if foo == 0:
    self.handle_zero()

if i % 10 == 0:
    self.handle_multiple_of_ten()
```
         
**No**
```python
if len(users) == 0:
    print 'no users'

if foo is not None and not foo:
    self.handle_zero()

if not i % 10:
    self.handle_multiple_of_ten()
```
* Note that `'0'` (i.e., 0 as string) evaluates to true.

==========



### Deprecated Language Features

Use string methods instead of the `string` module where possible. Use function call syntax instead of `apply`. Use list comprehensions and `for` loops instead of `filter` and `map` when the function argument would have been an inlined lambda anyway. Use `for` loops instead of `reduce`.

#### Definition

Current versions of Python provide alternative constructs that people find generally preferable.

#### Pros
#### Cons
#### Decision

We do not use any Python version which does not support these features, so there is no reason not to use the new styles.

**Yes**
```python
words = foo.split(':')

[x[1] for x in my_list if x[2] == 5]

map(math.sqrt, data)    # Ok. No inlined lambda expression.

fn(*args, **kwargs)
```

**No**
```python
words = string.split(foo, ':')

map(lambda x: x[1], filter(lambda x: x[2] == 5, my_list))

apply(fn, args, kwargs)
```

==========



### Lexical Scoping

Okay to use.

#### Definition

A nested Python function can refer to variables defined in enclosing functions, but can not assign to them. Variable bindings are resolved using lexical scoping, that is, based on the static program text. Any assignment to a name in a block will cause Python to treat all references to that name as a local variable, even if the use precedes the assignment. If a global declaration occurs, the name is treated as a global variable.

An example of the use of this feature is:

```python
def get_adder(summand1):
    """Returns a function that adds numbers to a given number."""
    def adder(summand2):
        return summand1 + summand2

    return adder
```

#### Pros

Often results in clearer, more elegant code. Especially comforting to experienced Lisp and Scheme (and Haskell and ML and …) programmers.

#### Cons

Can lead to confusing bugs. Such as this example based on [PEP-0227](http://www.python.org/dev/peps/pep-0227/):

```python
i = 4
def foo(x):
    def bar():
        print i,
    # ...
    # A bunch of code here
    # ...
    for i in x:  # Ah, i *is* local to Foo, so this is what Bar sees
        print i,
    bar()
```

So `foo([1, 2, 3])` will print `1 2 3 3`, not `1 2 3 4`.

#### Decision

Okay to use.

==========



### Function and Method Decorators

Use decorators judiciously when there is a clear advantage.


==========


### Threading

Do not rely on the atomicity of built-in types.

While Python's built-in data types such as dictionaries appear to have atomic operations, there are corner cases where they aren't atomic (e.g. if `__hash__` or `__eq__` are implemented as Python methods) and their atomicity should not be relied upon. Neither should you rely on atomic variable assignment (since this in turn depends on dictionaries).

Use the Queue module's `Queue` data type as the preferred way to communicate data between threads. Otherwise, use the threading module and its locking primitives. Learn about the proper use of condition variables so you can use `threading.Condition` instead of using lower-level locks.

==========



### Power Features

Avoid these features.

#### Definition

Python is an extremely flexible language and gives you many fancy features such as metaclasses, access to bytecode, on-the-fly compilation, dynamic inheritance, object reparenting, import hacks, reflection, modification of system internals, etc.

#### Pros

These are powerful language features. They can make your code more compact.

#### Cons

It's very tempting to use these "cool" features when they're not absolutely necessary. It's harder to read, understand, and debug code that's using unusual features underneath. It doesn't seem that way at first (to the original author), but when revisiting the code, it tends to be more difficult than code that is longer but is straightforward.

#### Decision

Avoid these features in your code.


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

