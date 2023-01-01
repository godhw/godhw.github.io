---
title: "[PL] Typing, Subtyping"
categories:
  - Programming Language
tags:
  - sml
  - racket
  - lacture
  - computer science
last_modified_at: 2022-07-03T22:00:00 +09:00
---
Let`s talk about type-checking, subtype
{: .notice--accent}

Type-checking<br>
static type checking: reject a program befire it runs to prevent the possibility of some errors. Every binding has one type, determined at compile-time=><br>
 statically typed languages(e.g. ML, Java, C#, C, C++ ...)<br>
dynamic type checking: do little or none such checking. try to treat a number as a function at run-time =><br>
 dynamically typed languages(e.g. racket, python, javascript ...)
{: .notice--warning}

{% include toc %}

Type checking
====
## Static vs Dynamic
- static : **ML**, Java, C#, C, C++, ...
- dynamic : **Racket**, python, javascript, ...

### Static type checking
- anything done to reject a program after it successfully **parses but befor it runs**
- define static checking => **type system**
  - given type to each variable, expression, ...
  - check misuse of primitives (e.g. 5/"one")
  - check variable not defined in the environment
  - but, cannot prevent logic / algorithmic errors
    - calling incorrect function
    - reversing the branches of a conditional
- avoiding dynamic checking

### Dynamic type checking
- do little or none static checking. Try to treat a number as a function at run-time
- Only check define or non-define : if use non define var or func, cannot execute => error

## When does type checker check error?
- Keystroke time: disallow it in the editor
- Compile time: disallow it if seen in code
- Link time: disallow it if seen in ocde that may be called to evaluate main
- Run time: disallow it right when we get to the division
- Later: allow execution when it's better to ignore. But, there must be an alternative

## Correctness
suppose a type system is supposed to prevent X
- sound
  - if it never accepts a program that, when run with some input, does X
  - Can say that X does occur when that program does not.
  - no false "not raise error X"
- complete
  - if it never rejects a program that, no matter what input it is run with, will not do X
  - Can accept program that X does occur
  - no false "raise error X"

- Sound and complete
  - we should be able to determine whether a part of program will be executed or not
  - this is the same as halting problem
  - impossible

## weak vs strong
- weak typing : static check => when run can `set the computer on fire`
  - ease of language implementation
  - array bound problem
- strong typing : check most things in dynamically

Type Inference
=====
- Give every binding/expression a type such that type-checking succeeds
## Implicitly typed
- ML's feature
- rarely need to write down types


