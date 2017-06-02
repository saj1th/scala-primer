autoscale: true


![inline, 75%](img/logo.png)

#[fit] A Scala Primer For Spark

---

# Agenda
- Why Scala?
- Getting Started
- Literals, Values, Variables, and Types
- Expressions / Control Structures
- First-Class Functions
- Object-Oriented Scala
- Collections
- Case Classes and Pattern Matching

---

# Why Scala?


---

Martin Odersky and team at EPFL created Scala in 2004 to provide a high-performance, concurrent-ready, statically typed JVM language which supports both functional and object-oriented programming

-
> When Matei Zaharia set out to create Spark, he wanted a programming language that provided LINQ style interface  (where people write functions inline, etc). He also wanted it to be on the JVM in order to easily interact with the Hadoop filesystem and related data formats. The presence of this kind of functional syntax along with static typing, type inference, pattern matching, actor libraries etc resulted in Scala being the choice

^ Eric Raymond 

^ cathedral  -> a near-perfect building that takes a long time to build. Once built, it stays unchanged for a long time.

^ bazaar -> is adapted and extended each day by the people working in it. 

^ Scala  more like a bazaar than a cathedral

^ It is designed to be extended and adapted by the people programming in it - it puts the tools for building such constructs into your hands.

---

Compatibility
> Scala code can reuse existing Java libraries - as it allows us to call Java methods, access Java fields, inherit from Java classes, and implement Java interfaces

Conciseness 
> Scala’s syntax avoids some of the boilerplate that burdens Java programs

Statically typed
> Scala  has an advanced static type system. It allows us to parameterize types with generics, combine types using intersections (e.g., A with B with C), and hide details of types using abstract types. Type inference and Pattern matching addresses the general concerns with statically typed languages - verbosity and lack of flexibility

---

## [fit]Scala for Spark

- Spark supports Scala, Java, Python, R, and SQL

- Data Engineers often prefer Scala & Java for building resilient infra for Big Data

- Data Scientists often use Python, R, and SQL

- These aren't hard boundaries and often people play both roles

---

# Goals
---

Levels of expertise for Scala[^1]

Application Programmer | Library Designer | Overall Level
---|---|---
Beginning  A1 |  | Beginning
Intermediate A2 | Junion L1 | Intermediate
Expert A3 | Senior L2 | Advanced
 | Expert L3 | Expert


[^1]: http://www.scala-lang.org/old/node/8610 

---

 __A1  (Beginning application programmer)__
	- Statements and expressions
	- Class, object, def, val, var, import, package
	- Infix notation for method calls
	- Simple closures & For-expressions
	- Collections with map, filter, etc

__L1  (Junior library designer)__
	- Type parameters, Traits, Lazy vals
	- Control abstraction, Currying
	- By-name parameters

__A2  (Intermediate application programmer)__
	- Pattern matching,  Trait composition
	- Recursion (tail recursion)
	- ~~XML literals~~

---

__L2 (Senior library designer)__
- Variance annotations, Existential types 
- Self type annotations and the cake pattern
- Structural types (static duck typing)
- Defining map/flatmap/withFilter for new kinds of for-expressions
- Extractors

__A3  (Expert application programmer)__
- Folds
- Streams and other lazy data structures
- Actors
- Combinator parsers

### We'll cover A1, L1, A2 and some sections in A3 and L2

---

# Getting Started

---

## [fit]Scala is object-oriented

Scala is an object-oriented language in pure form - every value is an object and every operation is a method call. 

```scala
1 + 2 //invoking a method named + defined in class Int
```   


Scala is more advanced than most other languages when it comes to composing objects. Objects are constructed by mixin composition, which takes the members of a class and adds the members of a number of traits to them.

```scala
 class Iter extends StringIterator(args(0)) with RichIterator
 ```

^ Java supports primitive types which are not objects and also allow static fields and methods that are not members of any objects

^ Traits and mixin composition would be covered in detail in subsequent slides

---
## [fit]Scala is functional

Scala treats functions are first-class values. It allows us to  pass functions as arguments to other functions, return them as results from functions, or store them in variables. We could also define a function inside another function  and also define functions without giving them a name

```scala
	val xs = 1 to 3
	val it = xs.iterator
	eventually { it.next() shouldBe 3 }	
```

Scala encourages immutable data structures and referentially transparent methods (not having any side effects)

---

# [fit] Scala's Roots

- Scala adopts a large part of the syntax of Java and C#. Expressions, statements, and blocks are mostly as in Java, as is the syntax of classes, packages and imports

- Its uniform object model (every value is an object and every operation is a method call) was pioneered by Smalltalk and taken up subsequently by Ruby

- Its uniform access principle ( that there should be no syntactical difference between working with an attribute, precomputed property, or method/query of an object)  comes from Eiffel

- Its approach to functional programming is quite similar in spirit to the ML family of languages, which has SML, OCaml, and F#

- Scala standard library is influenced by Haskell. Scala’s main concurrency library - Akka, was heavily inspired by Erlang

---

# [fit]Setting up the Environment 

http://sdkman.io/

```bash


curl -s "https://get.sdkman.io" | bash

sdk list scala

sdk i scala 2.11.8
sdk i sbt

scalac -version
```

---
