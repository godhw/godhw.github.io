---
title: "[SML] ML Modules"
categories:
  - Programming Language
tags:
  - sml
  - lacture
  - computer science
last_modified_at: 2022-04-25T22:00:00 +09:00
---
Let`s talk about structure, signature, module
{: .notice--accent}

for larger programs, one top-level sequence of bindings is poor, especially because a binding can use all earlier (non-shadowed) bindings<br>
Inside a module, can use earlier bindings as usual and can have any kind of binding<br>
Outside a module, refer to earlier modules' bindings (e.g. List.foldl)
{: .notice--warning}

{% include toc %}

Module
====

## structure

used to define modules

```sml
structure MyModule = struct bindings end
```

- this is just namespace management
    * giving a hierarchy to names to avoid shadowing
    * allows different modules to reuse names (e.g. map)

## signature

- a type of a module
    * what bindings does it have and what are their types
- can define a signature and ascribe it to modules
- hiding bindings and type definition are real value of signature
    * hiding implementation details is the most important strategy for writing correct, robust, reusable software
    * functions already do well for some forms of hiding
- to make private top-level functions, signatures omit bindings

```sml
signature SIGNAME =
sig types-for-bindings end
(* can include variables, types, datatypes, and exceptions defined in module *)

structure MyModule :> SIGNAME =
struct bindings end
(* module will not type-check unless it matches the signature, meaning it has all the bindings at the right types *)
```

## library spec and invariants

### library spec(properties)
- externally visible guarantees, up to library writer
- guaranteed by library
- no infinite loops or exceptions

### invariants
- part of the implementation, not the module's spec
- rules for writers
- code maintains and relies on this

## abstract types

- by revealing the datatype definition, writers let clients violate invariants by directly creating values
    * at best a comment saying "must use constructor"
- an ADT must hide the concrete type definitions so clients cannot create invariant-violating values of the type directly
- so in a signature, `type foo` means the type exists, but clients do not know its definition
- so, nothing client can do to violate invariants and properties

### two key restrictions

to use signatures for hiding

- deny bindings exist (val, fun bindings, constructor)
- make types abstract (so clients cannot create values of them or access their pieces directly)

## signature matching

`structure Foo :> BAR` is allowed if :
- every non-abstract type in BAR is provided in Foo, as specified
- every abstract type in BAR is provided in Foo in some way
    * can be a datatype or a type synonym
- every val-binding in BAR is provided in Foo, possibly with a more general and/or less abstract internal type
- every exception in BAR is provided in Foo

equivalent implemnetation
==========

a key purpose of abstraction is to allow different implementations to be **equivalent**
- no client can tell which you are using
- so can improve/replace/choose implementations later
- easier to do if you start with more abstract signatures
- different invariants, but same properties -> equivalent

## interesting thing

- given a signature with an abstract type, different structures can :
    * have that signature
    * but implement the abstract type differently
- such structures might or might not be equivalent
- this is a crucial feature for type system and module properties
    * different modules have different internal invariants
