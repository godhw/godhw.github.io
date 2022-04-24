---
title: "[SML] records, datatypes, case expressions"
categories:
  - Programming Language
tags:
  - sml
  - lacture
  - computer science
last_modified_at: 2022-04-24T22:00:00 +09:00
---
Let`s talk about records, datatypes, case expressions.
{: .notice--accent}

- Syntax : How do you write language constructs?
- Semantics : What do programs mean? (Evaluation, type checking rule)
- Idioms : What are typical patterns for using language features to express your computation? (programming pattern e.g. recursion)
- Libraries : What facilities does the language provide standard? (e.g. file, data structures)
- Tools : What do language implemnetations provide to make your job easier? (e.g. REPL, debugger, ...)
{: .notice--warning}

{% include toc %}

### 3 most important type building blocks in any language
a lot of data can be described with just these building blocks

- Each of : a t value contains values of each of t1 t2 ... tn (e.g. tuple : `int * bool` -> `int` and `bool`)
- One of : a t value contains values of on of t1 t2 ... tn (e.g. option : `int option` -> `int` or no data)
- Self reference : t value can refer to other t values
List : use all building blocks

records
======
have named fields!

- build : {f1 = e1, ..., fn = en}
- access : #fieldName e
- order of fields in a record value or type never matters

## vs tuple
- little difference between `(1,2,3)` and {f=1,g=2,h=3}
    * tuples a little shorter
    * records a little easier to rememeber what is where
    * generally a matter of taste, but for many fields, record is ususally better
- common decision for a construct's syntax is whether to refer to things **by position(tuple)** or **by name(record)**
- tuple syntax is just a different way to write certain record
    * record with field names 1, 2, ...
    * {1=e1, ..., n=en}
    * {1 :t1, ..., n:tn}

Syntactic sugar<br>
tuples are just syntactic sugar for records with fields named 1,2,...,n<br>
Syntactic: can describe the semantics entirely by the record syntax<br>
Sugar : they make the language sweeter<br>
they simplify understanding, implementing the language
{: .notice--accent}

## boolean operations
example of systactic sugar
- andalso : `e1 && e2` -> built-in operation
- orelse : `e1 || e2` -> build-in operations
- not : !e1 -> pre-defined function (e.g. hd, tl)

datatype
=======
**build**
```sml
datatype mytype = TwoInts of int * int
                | Str of string
                | Pizza (* just a value like NONE of option type*)
```
- add a new type `mytype` to the environment
- add constructors to the environment : a function that makes values of the new type or a value of the new type
    * TwoInts : int * int -> mytype
    * Str : string -> mytype
    * Pizza : mytype
- of course, can use self-reference (recursive)

**example of recursive datatype**

```sml
datatype my_int_list = Empty | Cons of int * my_int_list

val x = Cons(4,Cons(23,Cons(2008,Empty))); 
```

**access**
- check what variant it is(what constructor made it) (e.g. null, isSome)
- Extract the data (if that variant has any) (e.g. hd, tl, valOf)
- use case expression and pattern-matching!

case and pattern
========

## case

```sml
fun f x = 
    case x of
      Pizza => 3
    | TwoInts(i1,i2) => i1+i2
    | Str s => String.size s
```

- multi-branch conditional to pick branch based on variant(tag)
- extrats data and binds to variables local to that branch
- Type-checking : all branches must have same type
- Evaluation : evaluate right branch (extended by the match)
- Syntax :

```
case e0 of
    p1 => e1
  | p2 => e2
  ...
  | pn => en
```

## pattern

each pattern is a constructor name followed by the right number of variables
- syntactically most patterns look like expressions
- but patterns are not expressions (do not evaluate, just match)

it is better than condition expression :
- cannot forget a case
- cannot duplicate a case (make error)
- not forget to test the variant correctly and get an exception (like hd [])
- pattern-matching can be generalized and made more powerful

### grammar of pattern
`_` (don't care in digital logic), constant, variable, tuple, record, empty list, list(p1::p2), constructor are pattern

in list, `hd`, `tl`, `null` are functions, `[]`, `::` are constructors<br>
the reason functions are predefined is for passing as arguments to other functions and sometimes they are convenient
{: .notice--warning}

### pattern-matching

- every val-binding and func-binding uses pattern-matching
- every function in ML takes exactly one argument

variable binding example
```sml
fun sum_triple triple = 
    let val (x, y, z) = triple
    in x+y+z
    end
```
function binding example
```sml
fun sum_triple (x,y,z) = x + y + z
```
Can we distinguish one argument tuple of type `int*int*int` or three int arguments?  
**In ML, every function takes exactly one argument!!!**  
So, multi-argumnet function -> one tuple argument with tuple pattern-matching
