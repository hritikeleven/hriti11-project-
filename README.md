# hriti11-project- for hacktoberfest
What’s New In Python 3.10 python for all
Release
3.10.0

Date
October 19, 2021

Editor
Pablo Galindo Salgado

This article explains the new features in Python 3.10, compared to 3.9.

For full details, see the changelog.

Summary – Release highlights
New syntax features:

PEP 634, Structural Pattern Matching: Specification

PEP 635, Structural Pattern Matching: Motivation and Rationale

PEP 636, Structural Pattern Matching: Tutorial

bpo-12782, Parenthesized context managers are now officially allowed.

New features in the standard library:

PEP 618, Add Optional Length-Checking To zip.

Interpreter improvements:

PEP 626, Precise line numbers for debugging and other tools.

New typing features:

PEP 604, Allow writing union types as X | Y

PEP 613, Explicit Type Aliases

PEP 612, Parameter Specification Variables

Important deprecations, removals or restrictions:

PEP 644, Require OpenSSL 1.1.1 or newer

PEP 632, Deprecate distutils module.

PEP 623, Deprecate and prepare for the removal of the wstr member in PyUnicodeObject.

PEP 624, Remove Py_UNICODE encoder APIs

PEP 597, Add optional EncodingWarning

New Features
Parenthesized context managers
Using enclosing parentheses for continuation across multiple lines in context managers is now supported. This allows formatting a long collection of context managers in multiple lines in a similar way as it was previously possible with import statements. For instance, all these examples are now valid:

with (CtxManager() as example):
    ...

with (
    CtxManager1(),
    CtxManager2()
):
    ...

with (CtxManager1() as example,
      CtxManager2()):
    ...

with (CtxManager1(),
      CtxManager2() as example):
    ...

with (
    CtxManager1() as example1,
    CtxManager2() as example2
):
    ...
it is also possible to use a trailing comma at the end of the enclosed group:

with (
    CtxManager1() as example1,
    CtxManager2() as example2,
    CtxManager3() as example3,
):
    ...
This new syntax uses the non LL(1) capacities of the new parser. Check PEP 617 for more details.

(Contributed by Guido van Rossum, Pablo Galindo and Lysandros Nikolaou in bpo-12782 and bpo-40334.)

Better error messages
SyntaxErrors
When parsing code that contains unclosed parentheses or brackets the interpreter now includes the location of the unclosed bracket of parentheses instead of displaying SyntaxError: unexpected EOF while parsing or pointing to some incorrect location. For instance, consider the following code (notice the unclosed ‘{‘):

expected = {9: 1, 18: 2, 19: 2, 27: 3, 28: 3, 29: 3, 36: 4, 37: 4,
            38: 4, 39: 4, 45: 5, 46: 5, 47: 5, 48: 5, 49: 5, 54: 6,
some_other_code = foo()
Previous versions of the interpreter reported confusing places as the location of the syntax error:

File "example.py", line 3
    some_other_code = foo()
                    ^
SyntaxError: invalid syntax
but in Python 3.10 a more informative error is emitted:

File "example.py", line 1
    expected = {9: 1, 18: 2, 19: 2, 27: 3, 28: 3, 29: 3, 36: 4, 37: 4,
               ^
SyntaxError: '{' was never closed
In a similar way, errors involving unclosed string literals (single and triple quoted) now point to the start of the string instead of reporting EOF/EOL.

These improvements are inspired by previous work in the PyPy interpreter.

(Contributed by Pablo Galindo in bpo-42864 and Batuhan Taskaya in bpo-40176.)

SyntaxError exceptions raised by the interpreter will now highlight the full error range of the expression that constitutes the syntax error itself, instead of just where the problem is detected. In this way, instead of displaying (before Python 3.10):

>>>
>>> foo(x, z for z in range(10), t, w)
  File "<stdin>", line 1
    foo(x, z for z in range(10), t, w)
           ^
SyntaxError: Generator expression must be parenthesized
now Python 3.10 will display the exception as:

>>>
>>> foo(x, z for z in range(10), t, w)
  File "<stdin>", line 1
    foo(x, z for z in range(10), t, w)
           ^^^^^^^^^^^^^^^^^^^^
SyntaxError: Generator expression must be parenthesized
This improvement was contributed by Pablo Galindo in bpo-43914.

A considerable amount of new specialized messages for SyntaxError exceptions have been incorporated. Some of the most notable ones are as follows:

Missing : before blocks:

>>>
>>> if rocket.position > event_horizon
  File "<stdin>", line 1
    if rocket.position > event_horizon
                                      ^
SyntaxError: expected ':'
(Contributed by Pablo Galindo in bpo-42997)

Unparenthesised tuples in comprehensions targets:

>>>
>>> {x,y for x,y in zip('abcd', '1234')}
  File "<stdin>", line 1
    {x,y for x,y in zip('abcd', '1234')}
     ^
SyntaxError: did you forget parentheses around the comprehension target?
(Contributed by Pablo Galindo in bpo-43017)

Missing commas in collection literals and between expressions:

>>>
>>> items = {
... x: 1,
... y: 2
... z: 3,
  File "<stdin>", line 3
    y: 2
       ^
SyntaxError: invalid syntax. Perhaps you forgot a comma?
(Contributed by Pablo Galindo in bpo-43822)

Multiple Exception types without parentheses:

>>>
>>> try:
...     build_dyson_sphere()
... except NotEnoughScienceError, NotEnoughResourcesError:
  File "<stdin>", line 3
    except NotEnoughScienceError, NotEnoughResourcesError:
           ^
SyntaxError: multiple exception types must be parenthesized
(Contributed by Pablo Galindo in bpo-43149)

Missing : and values in dictionary literals:

>>>
>>> values = {
... x: 1,
... y: 2,
... z:
... }
  File "<stdin>", line 4
    z:
     ^
SyntaxError: expression expected after dictionary key and ':'

>>> values = {x:1, y:2, z w:3}
  File "<stdin>", line 1
    values = {x:1, y:2, z w:3}
                        ^
SyntaxError: ':' expected after dictionary key
(Contributed by Pablo Galindo in bpo-43823)

try blocks without except or finally blocks:

>>>
>>> try:
...     x = 2
... something = 3
  File "<stdin>", line 3
    something  = 3
    ^^^^^^^^^
SyntaxError: expected 'except' or 'finally' block
(Contributed by Pablo Galindo in bpo-44305)

Usage of = instead of == in comparisons:
