---
title: "[SML] tuples, lists, local bindings, benefit of no mutation"
categories:
  - Programming Language
tags:
  - sml
  - lacture
  - computer science
last_modified_at: 2022-04-24T22:00:00 +09:00
---
Let`s talk about tuples, lists, local bindings, immutation
{: .notice--accent}

{% include toc %}

**local bindings**<br>
for style and convenience<br>
a big but natural idea: nested function bindings for efficiency<br>
why not having mutation is a valuable language feature : no need to keep track of sharing/aliasing
{: .notice--warning}

pairs(tuple)
=====
2-tuples that may have different types; tuples can have more than two parts  

**build**
- Syntax : `(e1, e2)`
- Evaluation : evaluate `e1` to v1, `e2` to v2; result is (v1, v2) that is a value
- Type-checking : If `e1` has type t1 and `e2` has type t2, then the pair expression has type t1*t2; fun : t1*t2 -> t3 ???

**access**
- Syntax : `#1 e` and `#2 e`
- Evaluation : evaluate `e` to a pair of values and return value of piece's
- Type-checking : If `e` has type `t1*t2`, then former has type t1, latter has type t2
```sml
(* Tuples can be nested *)
val x1 = (7, (true, 9));
val x2 = #1 (#2 x1) (* x1's second element's first element *)
```

lists
=======
containors have many value

**build**
- empty list is a value : `[]`
- list of values is a value; elements separated by commas : `[e1, ..., en]`
- if `e1` evaluates to v1 and `e2` to a list [v2, ..., vn], then `e1::e2` evaluates to [v1, v2, ..., vn]
- `e1`, ..., `en` must have same type

**access**
- `null e` : `true` when `e` evaluates to []
- `hd e` : evaluates to first element of list `e`
- `tl e` : evaluates to list except for the first element
- `hd`, `tl` raise exception if `e` evaluates to []

**Type-checking**
- For any type `t`, the type `t list` describes lists where all elements have type `t`
- `[]` can have any type, so, indicate this `'a list`(quote a or alpha)
- `e1::e2` : `e1` has type `t` and `e2` has type `t list`
- null : 'a list -> bool
- hd : 'a list -> 'a
- tl : 'a list -> 'a list 

let-expressions
==========
now, we can use local binding
- Syntax : `let b1 b2 ... bn in e end`; b is any binding, e is expression
- Type-checking : check each binding and e in a current static environment, whole expression's type is the type of e
- Evaluation : evaluate each binding and e in a current dynamic environment, result of whole expression is reuslt of evaluating e

## scope
where a binding is in the environment
- in later bindings and body of the let-expression unless a later or nested binding **shadows**<span class="comment">change value</span> it
- only in later bindings and body of the let-expression

## local binding
- we can define functions inside any let-expression
- functions can use bindings in the environment where they are defined
    * parameters to the outer function
    * earlier bindings in the let-expression

Nested functions<br>
good style to define helper functions inside the functions they help if they are : 1. unlikely to be useful elsewhere 2. Likely to be misused if available elsewhere 3. likely to be changed or removed later<br>
trade-off : reusing code saves effort and avoids bugs, but makes the reused code harder to change later
{: .notice--warning}

options
======
`t option` is a type for any type t (similar to list)

**build**
- `NONE` has type 'a option
- `SOME e` has type t option if t is e's type

**access**
- isSome : 'a option -> bool (similar to null)
- valOf : 'a option -> 'a (`SOME e` -> e)

immutation
======
```sml
fun sort_pair (pr : int * int) =
  if #1 pr < #2 pr
  then pr
  else (#2 pr, #1 pr)
  
fun sort_pair (pr : int * int) =
  if #1 pr < #2 pr
  then (#1 pr, #2 pr)
  else (#2 pr, #1 pr)
```
in ML, these two implementations are **indistinguishable**
- because tuples are immutable
- In languages with mutable compound data, these are different(e.g. java)
- no code can ever distinguish aliasing vs. identical copies
- no need to think about aliasing: focus on other things
- can use aliasing, which saves space, without danger
