Python Style Guide
==================
    
    1. [Lint](#lint)
    1. [Imports](#imports)
    1. [Packages](#packages)
    
    
## Python Language Rules

### Lint

Run `pylint` over your code.

#### Definition
pylint is a tool for finding bugs and style problems in Python source code. It finds problems that are typically        caught by a compiler for less dynamic languages like C and C++. Because of the dynamic nature of Python, some           warnings may be incorrect; however, spurious warnings should be fairly infrequent.

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

### Packages

## Python Style Rules
