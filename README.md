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



### Imports

Use `imports` for packages and modules only.

#### Definition
#### Pros
#### Cons
#### Decision



### Packages

Import each module using the full pathname location of the module.

#### Definition
#### Pros
#### Cons
#### Decision



### Exceptions

Exceptions are allowed but must be used carefully.

#### Definition
#### Pros
#### Cons
#### Decision



### Global variables

Avoid global variables.

#### Definition
#### Pros
#### Cons
#### Decision



### Nested/Local/Inner Classes and Functions

Nested/local/inner classes and functions are fine.

#### Definition
#### Pros
#### Cons
#### Decision




### List Comprehensions

Okay to use for simple cases.

#### Definition
#### Pros
#### Cons
#### Decision



### Default Iterators and Operators

Use default iterators and operators for types that support them, like lists, dictionaries, and files.

#### Definition
#### Pros
#### Cons
#### Decision



### Generators

Use generators as needed.

#### Definition
#### Pros
#### Cons
#### Decision



### Lambda Functions

Okay for one-liners.

#### Definition
#### Pros
#### Cons
#### Decision



### Conditional Expressions

Okay for one-liners.

#### Definition
#### Pros
#### Cons
#### Decision



### Default Argument Values

Okay in most cases.

#### Definition
#### Pros
#### Cons
#### Decision



### Properties

Use properties for accessing or setting data where you would normally have used simple, lightweight accessor or setter methods.

#### Definition
#### Pros
#### Cons
#### Decision



### True/False evaluations

Use the "implicit" false if at all possible.

#### Definition
#### Pros
#### Cons
#### Decision



### Deprecated Language Features

Use string methods instead of the `string` module where possible. Use function call syntax instead of `apply`. Use list comprehensions and `for` loops instead of `filter` and `map` when the function argument would have been an inlined lambda anyway. Use `for` loops instead of `reduce`.

#### Definition
#### Pros
#### Cons
#### Decision



### Lexical Scoping

Okay to use.

#### Definition
#### Pros
#### Cons
#### Decision



### Function and Method Decorators

Use decorators judiciously when there is a clear advantage.

#### Definition
#### Pros
#### Cons
#### Decision



### Threading

Do not rely on the atomicity of built-in types.

#### Definition
#### Pros
#### Cons
#### Decision



### Power Features

Avoid these features.

#### Definition
#### Pros
#### Cons
#### Decision



## Python Style Rules
