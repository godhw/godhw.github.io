---
title: "[SML] start sml"
categories:
  - Programming Language
tags:
  - sml
  - lacture
  - computer science
last_modified_at: 2022-04-24T22:00:00 +09:00
---
Let`s talk about SML, functional programming
{: .notice--accent}

{% include toc %}

variable binding
============
Define new variable; this means that the values are bound
```ml
val x = e;
```
**syntax:**
- Keyword `val` and `=` and `;`
- Variable `x`
- Expression `e` : many forms of these, most containing subexpressions

syntax vs semantics
============
- Syntax is just how you write something
- Semantics is what that something means
    * Type-checking (before program runs) : extend **static environment**
    * Evaluation (as program runs) : extend **dynamic environment**
```ml
val x = 42;
(* type-checking : static env {x : int}
 * evaluation : dynamic env {x : 42}
 *)
```

Expressions
====
- There are many kinds of expressions
- Expression can contain any subexpression...
- Precise definitions can predict complex programs
- Every kind of expression has
    * Syntax
    * Type-checking rules : produces a type
    * Evaluation rules : produces a value after type-check
    
## variables
- Syntax : sequence of letters, digits, `_`, not starting with digit
- Type-checking : look up type in current static environment
- Evaluation : look up value in current dynamic environment

## Addition
- Syntax : `e1` + `e2` where `e1` and `e2` are expressions
- Type-checking : `e1`, `e2`, and `e1 + e2` must be of the same type(`int` or `real`)
- Evaluation : if `e1` evaluates to v1 and `e2` evaluates to v2, `e1 + e2` evaluates to sum of v1 and v2

## Values
- All values are expressions but the opposite is not
- 21, 99 have type int
- true, false have type bool
- () has type unit : it means nothing
- "string", "str" have type string
- 3.14, 2.0 have type real


## conditional expression
- Syntax : if `e1` then `e2` else `e3`
- Type-checking :
    *  `e1` must have type bool
    * `e2` and `e3` must be of the same type but have any type
    * so, the type of the entire expression is also `e2`, `e3` type
- Evaluation : evaluate `e1` : true -> evaluate `e2`, false -> evaluate `e2`

## less-than expression
- Syntax : `e1 < e2`
- Type-checking : 
    * `e1` and `e2` must be of the same type 
    * the type of entire expression is bool
- Evaluation : evaluate `e1` to v1 and `e2` to v2 and produce `true` if v1 less than v2 else `false`

function
=====
It has arguments and result
``` ml
(* `: type` can be omitted *)
fun pow (x : int, y : int) : int =
    if y = 0
    then 1
    else x * pow(x, y-1)
(* val pow = fn : int * int -> int *)
```
- The use of `*` in type syntax is not multiplication
- Cannot refer to later function bindings
    * So, helper functions must come before their uses
    * Need special construct for mutual recursion `and`
- use **Recursion** : that is more powerful than loops
- but sometimes loops are appropriate solutions
- functions can be passed as parameters of another function or returned from another function (First-class function)

## function bindings
- Syntax : `fun x0 (x1 : t1, ..., xn : tn) = e
- Evaluation : **A FUNCTION IS A VALUE!** (not evaluation)
    * add `x0` to dynamic env so later expressions can call it
  
**Type-checking**

To type-check a function binding, we type-check the body e in a static environment that (in addition to all the earlier bindings) maps x1 to t1, ... xn to tn (arguments) and x0 to `t1 * ... * tn -> t` (for recursion).
{: .notice--warning}

## function calls
-Syntax : `e0 (e1, ..., en)`
    * parentheses optional if there is only one argument
- Type-checking : `e0` has some type `t1 * ... * tn -> t`, `e1` to t1, ... `en` to tn => e0 (e1, ..., en) has type t
- Evaluation : extend dynamic environment
    * evaluate `e0` to a function x0
    * evaluate arguments to values
    * result is evaluation of e in an environment that is extended
    
## comparisons
- `=`(equal), `<>`(inequality) : can be used any "equality type" but not with real 
- `>`, `<`, `>=`, `<=` : can be used with real
