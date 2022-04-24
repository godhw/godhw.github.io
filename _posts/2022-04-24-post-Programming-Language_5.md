---
title: "[SML] First-class functions, Subtyping"
categories:
  - Programming Language
tags:
  - sml
  - lacture
  - computer science
last_modified_at: 2022-04-24T22:00:00 +09:00
---
Let`s talk about most important thing in functional programming
{: .notice--accent}

{% include toc %}

Functional programming
=======
- avoiding mutation in most/all cases
- using functions as values (first-class function)
- Style encouraging recursion and recursive data structures
- Style closer to mathematical definitions
- programming idioms using laziness
- functional language makes it easy

## first-class function

can use function wherever we use values

- most common use is as an argument/result of another function
- the other function is called a **higher-order function** (return func, ...)
- powerful way to factor out common functionality

```sml
(* f is higher order, g is first-class *)
fun f (g, ...) = g (...)
```
## function closures

functions can use bindings from outside the function definition (in scope where function is defined)

- makes first-class functions much more powerful
- distinction between terms first-class fucntions and function closures is not universally understood
- important conceptual distinction even if terms get muddled

## higher-order functions and polymorphic

- higher-order functions are often so 'generic' and 'reusable' that they have polymorphic types (e.g. 'a)
- but there are higher-order functions that are **not polymorphic**
- and there are non-higher-order functions that are polymorphic (length : 'a list -> int)
- always a good idea to understand the type of a function
- type is inferred based on how arguments are used
    * describles which types must be exactly something and which can be anything but the same (e.g. 'a)
    
## anonymous function

- definitions unnecessarily at top-level(opposite local) are still poor style
- function binding is not an expression, so func binding cannot exist in tuple
- anonymous functions is an expression form of function binding
- similar to lambda in python3
```sml
fun trip y = 3*y; (* function binding *)
fn y => 3*y; (* anonymous function *)
(* no function name, just an argument pattern *)
```

### using anonymous function

- most common use in argument to a higher-order function
- don't need a name just to pass a function
- **But, cannot use an anonymous function for a recursive function**
    * because there is no name for making recursive calls
    * if not for recursion, fun bindings would be syntactic sugar for val bindings and anonymous functions

```sml
fun triple x = 3*x
val triple = fn y => 3*y
```

## higher-order function hall-of-fame

iterator

### map

```sml
fun map (f, xs) =
    case xs of
    [] => []
  | x::xs' => (f x)::(map(f,xs'))
(* val map : ('a -> 'b) * 'a list -> 'b list *)
```
- name is standard for any data structure
- use it all the time once know it : saves a little space, but more importantly, communicates what you are doing
- similar predefined function : List.map
- returns the list that all elements applied the function

### filter

```sml
fun filter (f,xs) =
    case xs of
    [] => []
  | x::xs' => if f x
              then x::(filter(f,xs'))
              else filter(f,xs')
(* val filter : ('a -> bool) * 'a list -> 'a list *)
```

- so use it whenever your computation is a filter
- similar predefined function : sList.filter

## generalizing
first-class functions are useful anywhere for any kind of data

- can pass several functions as arguments
- can put functions in data strutures (tuples, lists, etc.)
- can return functions as results
- can write higher-order fucntions that traverse your own data structures
- useful whenever you want to abstract over what to compute with

returning functions<br>
REPL never prints unnecessary parentheses and `t1 -> t2 -> t3 -> t4 means t1->(t2->(t3->t4))
{: .notice--warning}

## Type synonyms

- just for convenience, it doesn't let us do anything new
- use related to modularity

```sml
type date = int * int * int;
```

### type vs datatype
- `datatype` introduces a new type name distinct from all existing types
- `type` is just another name

## type generality

when we write function that appends two string lists, type checker says no type binding...
```sml
(* expected *)
string list * string list -> string list

(* but real *)
'a list * 'a list -> 'a list
```

- the type 'a is more general
- more general types can be used as any less general type
- t1 is more general than t2 if can take t1, replace its type variables consistently, and get t2

## subtype
similar to subclass

- if s and t represent type and s is a subtype of t (`s <: t`)
    * an instance of type s can be safely used in a context where an instance of type t is expected
