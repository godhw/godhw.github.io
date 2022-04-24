---
title: "[SML] nested patterns, exceptions, tail recursion"
categories:
  - Programming Language
tags:
  - sml
  - lacture
  - computer science
last_modified_at: 2022-04-24T22:00:00 +09:00
---
Let`s talk about nested patterns, exceptions, tail recursion.
{: .notice--accent}

{% include toc %}

nested pattern
========
- we can nest patterns as deep as we want like nest expressions
- often avoid hard-to-read, wordy nested case expressions
- avoid nested case expressions if nested patterns are simpler and avoid unnecessary branches or let-expressions
- Wildcard(`_`) are good style : use them instead of variables when you do not need the data
- the semantics for pattern-matching takes a pattern p and a value v and decides does it match and if so, what variable bindings are introduced

Exceptions
=========
- Exceptions are a lot like datatype constructors
- declaring an exception adds a constructor for type `exn`
- can pass values of exn anywhere (not too common to do this)
- `handle` can ahve multiple branches with patterns for type `exn`

An exception binding introduces a new kind of exception : declare exception

```sml
exception MyFirstException
exception MySecondException of int * int
```

`raise` primitive raises (a.k.a. throws) an exception

```sml
raise MyFirstException
raise (MySecondException(7,9))
```

`handle` expression can handle (a.k.a. catch) an exception, if doesn't match, exception continues to propagate

```sml
e1 handle MyFirstException => e2
        | MySecondException(x,y) => e3
```

tail recursion
=======

## call-stacks
while a program runs, there is a call stack of function calls that have started but **not yet returned**
- these stack-frames store information like the value of local variables and what is left to do in the function
- due to recursion, multiple stack-frames may be calls to the same function
- called "activation record"

## optimize recursion
- it is unnecessary to keep around a stack-frame just so it can get a callee's result and return it without any further evaluation
- ML recognizes these tail calls in the compiler and treats them differently => make recursion as efficient as a loop
- all functional-language inplementations do tail-call opt
- recursive calls are tail-calls => Tail-recursive

## moral of tail recursion
There is a methodology that can often guide this transformation
- create a helper function that takes an **accumulator**
- old base case becomes initial accumulator
- new base case becomes final accumulator

## problem
- there are certainly cases where recursive functions cannot be evaluated in a constant amount of space (e.g. process trees)
- in these cases, the natural recursive approach is the way to go
- also beware the wrath of premature optimization

## more precise
- tail-call : nothing left for caller to do, a function call in tail position
- `fun f p = e` : e is in tail position
- `if e1 then e2 else e3` is in tail position : `e2` and `e3` are in tail position
- `let b1 ... bn in e end` is in tail position : `e` is in tail position
- Function-call arguments are **not in tail position**
