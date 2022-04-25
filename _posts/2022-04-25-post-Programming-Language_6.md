---
title: "[SML] lexical scope, function closures, and idioms"
categories:
  - Programming Language
tags:
  - sml
  - lacture
  - computer science
last_modified_at: 2022-04-25T10:00:00 +09:00
---
Let`s talk about SML, functional programming
{: .notice--accent}


{% include toc %}

lexical scope
====
vs dynamic scope : use environment where function is called


- semantic : function body is evaluated in the environment where the function was defined(created) extended with the function argument
- nothing changes th this rule when we take and return functions

current environment vs old environment

## closure

- function value has two parts
    * the code
    * the environment that was current when the function was defined
- this is a pair but cannot access the pieces
- this pair is called a function closure
- function call evaluates the code part in the environment part(extended with the function argument)

## examples

### returning a function
```sml
val x = 1
fun f(y) = let val x = y+1 in fn z => x+y+z end
val x = 3
val g = f 4
val y = 5
val z = g 6
(* z = 15 *)
```

### passing a function

```sml
fun f(g) = let val x = 3 in g(2) end
val x = 4
fun h(y) x + y
val z = f(h)
(* x = 3 is ignored, z = 6 *)
```

## why lexical scope
there are three reasons

- function meaning does not depend on variable names used
- functions can be type-checked and reasoned about where defined
    * when use dynamic scope : never know when it will be overriding(shadowing)
- closures can easily store the data(private data) they need

### dynamic scope
- dynamic scope is occasionally convenient in some situations
    * so some languages(Racket) have special ways to do it
    * but most do not bother
- exception handling is more like dynamic scope
    * raise e transfers control to the current innermost handler
    
## iterator

### when things evaluate
- function body is not evaluated untill the function is called
- function body is evaluated every time the function is called
- a variable binding evaluates its expression when the binding is evaluated, not every time the variable is used

with closures, this means we can avoid repeating computations that do not dn arguments

```sml
(* too many computes string.size s *)
fun allShorterThan1 (xs, s) =
    filter(fn x => String.size x < String.size s, xs)

(* computes string.size s once *)
fun allShorterThan2 (xs ,s) =
    let val i = String.size s
    in filter(fn x => String.size x < i, xs) end
```

### fold
- accumulates an answer by repeatedly applying f to answer so far
    * fold(f, acc, [x1,x2,x3,x4]) computes
    * f(f(f(f(acc,x1),x2),x3),x4) (like foldl)
- foldl : x1, x2, x3, x4
- foldr : x4, x3, x2, x1

```sml
fun fold (f,acc,xs) =
    case xs of
    [] => acc
  | x::xs' => fold(f, f(acc,x), xs')
(* val fold = fn : ('a * 'b -> 'a) * 'a * 'b list -> 'a *)
```

### why iterators?

- these "iterator-like" functions are not built into the language
    * just programming pattern
    * though many languages have built-in support, which often allows stopping early without **resorting to exceptions**
- this pattern separates recursive traversal from data processing
- functions like `map`, `filter`, and `fold` are much more powerful thanks to closures and lexical scope
- function passed in can use any private data in its environment
- Iterator doesn't even know the data is there or what type it has

idioms
=======
- pass functions with private data to **iterators**
- combine functions (e.g. composition)
- **currying** (multi-arg functions and partial application)
- callbacks (e.g., in reactive programming)
- implementing an ADT with a record of functions

## combine function

```sml
fun compose (f,g) = fn x => f (g x)
```

- ML standard library provides infix operator `o`
- same with `val compose = f o g`
- as in math, function composition is right to left
- **pipelines** of functions are common in functional programming and many programmers prefer left-to-right
    * can define our own infix operator

```sml
infix |>
fun x |> f = f x
fun compose i = i |> g |> f
```

### backup function

```sml
fun backup (f,g) =
    fn x => case f x of
            NONE => g x
          | SOME y => y
(* g is backup function *)
(* ('a -> 'b option) * ('a -> 'b) -> 'a -> 'b) *)
```

## currying

- previously encoded n arguments via one n-tuple in functions
- another way : take one argument and return a function that takes another argument and ...; called **currying** after famous logician Haskell Curry
- callees can just think "multi-argument function with spaces instead of a tuple pattern"
    * different than tupling : caller and callee must use same technique
```sml
fun sorted3 x y z = z >= y andalso y >= x
val t1 = sorted3 7 9 11
```
syntactic sugar for :

``` sml
val sorted3 = fn x => fn y => fn z => z >= y andaoso y >= x
val t1 = ((sorted3 7) 9) 11
```

### curried fold
- fold in ML
- foldl has f take argument in opposite order (x, acc)

```sml
fun fold f acc xs =
    case xs of
    [] => acc
  | x::xs' => fold f (f(acc,x)) xs'
```

### too few arguments

if caller provides too few arguments, we get back a closure waiting for the remaining arguments
- called partial application (modularity)
- convenient and useful
- can be done with any curried function

```sml
fun sum_inferior xs = fold (fn (x,y) => x+y) 0 xs

val sum = fold (fn (x,y) => x+y) 0
(* both are ready for pass xs *)
(* not to write fun f x = g x, just val f = g *)
(* this is same thing *)
```

partial application is particularly nice for iterator-like functions<br>
for this reason, ML library functions of this form usually curried<br>
(e.g. List.map, List.filter, List.foldl)
{: .notice--warning}

Moreover, if use partial application to create a polymorphic function, it my not work due to the **value restriction** like warning about "type vars not generalized" and won't let you call the function<br>
nothing wrong but still must change code (e.g. val p = List.map(fn x => (x,1));)
{: .notice--accent}

### curried or tupled
- if you want to curry a tupled function or vice-versa
- if a function's arguments are in the wrong order for the partial application
- **write higher-order wrapper functions**

```sml
fun reorder_curry f x y = f y x
fun curry f x y = f (x, y)
fun uncurry f (x,y) = f x y
```

### efficiency
- tupling and currying are both constant-time operations, so it doesn't matter in most of code
- for the small part where efficiency matters
    * SML/NJ compiles tuples more efficiently
    * but many other language do better with currying ( OCaml, F#, Haskell)
    * so curring is the normal thing
    
## callback

- library takes functions to apply later, when an event occurs
    * when a key is pressed, mouse moves, data arrives (UI programming)
    * when the program enters some state (turns in a game)
- library may accept multiple callbacks
    * different callbacks may need differnet private data with different types
    * fortunately, a function's type does not include the types of bindings in its environment
    * in OOP, objects and private fields are used similarly (e.g. java swing's event-listeners)
- while it's not absolutely necessary, mutable state is reasonably appropriate here

### ML mutation

- mutable data structures are okay in some situations
- when update to state of world is appropriate model
- if need to remember something, need mutable state
- ML does this with a separate construct : **references**

### references

- new types : `t ref` where t is a type
- new expressions
    * `ref e` to create a reference with initial contents e
    * e1 := e2 to update contents (e1 must ref value)
    * !e to retrieve contents (not negation)

## ADT
closures can implement ADT(abstract data types)
- can put multiple functions in a record
- the functions can share the same private data
- private data can be mutable or immutable
- feels a lot like objects, emphasizing that OOP and functional programming have some deep similarites    
