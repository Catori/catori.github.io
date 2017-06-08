# Catori
Catori is a composable system for composing systems. It is inspired by 
* The Univalence Axiom from Homotopy Type Theory, 
* HLists data structure (and the Frunk library)
* Path Depdendant Types
* Particle Physics and Quantum Mechanics
* Graph theory
* Substructural type systems
* Dr. Strange

An initial implementation in Rust is a WIP.

## Influences

### Univalence Axiom
The Univalence Axiom, brings the notion of equivalence being equivalent to equality. 
https://mathoverflow.net/questions/210498/what-is-the-most-transparent-rigorous-definition-of-the-univalence-axiom

### HLists
HLists are a powerful data structure, possible only in strongly typed languages, that allow for abstraction over arity
https://stackoverflow.com/questions/11825129/are-hlists-nothing-more-than-a-convoluted-way-of-writing-tuples

### Path Dependant Types
Path Dependendant Types 
http://lampwww.epfl.ch/~amin/dot/fpdt.pdf

### Particle Physics and Quantum Mechanics
Catori is expressed as a state machine of colliding and decaying particles. 
Laziness is expressed as an unobserved wave.
Escape from state machine is expressed as wormholes
Coordination is expressed as entanglement
Qubits might even make an appearance

### Graph Theory
Branching HLists form a tree. Pivoting to other dimensions, in oder to link to non-local portions of a tree,
allow us to treat multiple interlinked trees as a general purpose graph

### Substructural Type Systems
Rust has an affine type system that enforces allowable lifetimes of data. By having no references and
only explicit duplication, Catori has an extremely tight lifetime model. Additionally, by having no references
along with lifetime enforcement, all operations consume all arguments.

### Dr. Strange
Dr. Strange loves to tilt dimensions on their sides. So do I.

## Foundations
Catori is built on a very small handful of fundamental concepts. This section introduces them, using Rust notation where useful

It is designed to be completely abstract, in the sense that its core implementation is all in terms of 
traits and ZSTs (Zero Sized Types), so there is no runtime cost to any of the core reasoning.
We will get into the runtime behavior later.

### Cat Type
In Catori, a Cat type is anything that has an Order and has defined Equality(Ord+Eq in Rust).

```rust
pub trait Cat: Ord + Eq {}
```

And any type that is both Ord and Eq can be automatically treated as a Category, with a blanket impl
```rust
impl<CAT> Category for CAT where CAT:Ord+Eq{}
```

There is  also the notion of a Default, which is any category that can self-instantiate
```rust
pub trait Default : Category + std::default::Default {}
```

And one more blanket impl to allow automatic lifting of Rust default types into Catori-space
```rust
impl<DEFAULT> Default for DEFAULT where DEFAULT:Category+std::default::Default{}
```
(From here on out, we will assume that Default is the Catori version, and not the std::default::Default version)

Every type *must* be a Cat type. 

### Paths
Paths come from both Homotopy Type Theory, as well as Path Depedendent Types. 
The notion that they are possible to express in Rust comes from HLists, largely as implemented by Frunk.
The critical difference between HLists and Catori Paths, however, is that Paths know the type of their context.
<<<Elaborate>>>

In Catori, Head and Tail termionology is replaced with Here and There. 
Traversing from Here to There makes There become your new Here.

```rust
pub trait Path<Here>: Sized + Default{
    ///A path can have any other path as its context (if your rule system allows it),
    ///but a Path will always default to having no (Nil) context.
    type Context: Cat=Nil;
    ///In addition to its Context, the only other thing a Path knows about, is its next
    ///step (There)
    ///There is also a path to itself, and also defaults to having no There to traverse to.
    type There: Path<Self::There>=Nil;
        ///The only function that a path has, is next(), which produces the next item in the path
        ///This replaces Hlists's pop() function, but true to Catori's spirit, next destroys the Head
        ///and returns just the Tail.
        fn next(self) -> Self::There {
          ///Since There is also a Path, and is also Default, the blanket implementation for all Paths
          ///is to produce the default type of its There type.
          Self::There::default()
    }
}
```
