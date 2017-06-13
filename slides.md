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

Scala is an object-oriented language in pure form 

- every value is an object and 
- every operation is a method call

```scala
//invoking a method named + defined in class Int
1 + 2
```   

---
## [fit]Scala is object-oriented

Objects are constructed by mixin composition, which takes the members of a class and adds the members of a number of traits to them

```scala
 class Iter extends StringIterator(args(0)) with RichIterator
 ```

^ Java supports primitive types which are not objects and also allow static fields and methods that are not members of any objects

^ Traits and mixin composition would be covered in detail in subsequent slides

---
## [fit]Scala is functional

Scala treats functions are first-class values. It allows us to  

- pass functions as arguments to other functions
- return them as results from functions
- store them in variables
- define a function inside another function
- define functions without giving them a name

---

## [fit]Scala is functional

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

Binary Compatibility of Scala Releases [^binary-compatibility]


[^binary-compatibility]: http://docs.scala-lang.org/overviews/core/binary-compatibility-of-scala-releases.html , http://www.scala-lang.org/old/node/9346

---

## [fit]First Steps

```scala
scala> 1 + 2
res0: Int = 3

scala> println("hola")
hola

```

*res0* - an identifier (automatically generated in this case)
*Int* -  scala.Int the type (class Int in the package scala)
*3* - the value resulting from evaluating the expression 


 - Every expression in Scala has a type and a value. 
 - Type is determined at compile time and the 
 - Value is determined by executing the expression



^ All of Java’s primitive types have corresponding classes in the scala package
^ Scala compiler would use Java primitive types where possible

---

## Pop Quiz


Reason for keeping primitives in Java  [^whyprim] 



[^whyprim]: http://www.javaworld.com/article/2150208/java-language/a-case-for-keeping-primitives-in-java.html

[^whynotprim]: http://wiki.c2.com/?JavaPrimitiveTypesDiscussion

---

##Vals & Vars

```scala 
//vals can never be reassigned once initialized
//similar to a final variable in Java 
scala> val msg = "hello"
msg: String = hello

//vars can be reassigned throughout its lifetime
scala> var i = 10
i: Int = 10

```

- vals are preferred over vars
- immutability increases stability and predictability (esp concurrent or multithreaded code)
- makes debugging and reading easier


^ data stored in vals and vars get automatically deallocated by JVM during GC

---

## Type Inference


```scala 
scala> val msgGreet: String = "hello again"

```

>Often better to let the interpreter/compiler infer types than fill the code with explicit type annotations

```scala 
//Scala inferred the type of msg to be String
scala> val msg = "hello"
msg: String = hello

```

---

# Functions
```scala 
// What is the smallest positive integer whose square ends in the digits 269,696 ?
// Charles Babbage thought it'd be 99,736 - was he right?

def babbage(start: Int): Boolean = {
  var x = if (start % 2 == 1) start - 1 else start
  while ((x * x) % 1000000 != 269696) {
      x = x + 2
  }
  println(x)
  x == 99736
}
```

---

# Exercise 

Rewrite babel check without vars

---
# Exercise - Solution
```scala 

def babbageR(start: Int): Boolean = {
  def tailCheck(x: Int): Int = {
    if ((x * x) % 1000000 == 269696) {
      x
    } else {
      tailCheck(x + 2)
    }
  }

  val result = if (start % 2 == 1) {
    tailCheck(start - 1)
  } else {
    tailCheck(start)
  }

  println(result)
  result == 99736
}

```
Restriction on forward references in blocks [^fwdblock]

[^fwdblock]: http://www.scala-lang.org/files/archive/spec/2.11/04-basic-declarations-and-definitions.html

---
# Functions


```scala 

def greet() = {
  println("hola")
}

// Methods which return Unit could use the non-equals syntax. 
// As of Scala-2.10, using equals sign is preferred
def greeter() {
  println("hola")
}

```

---

# Functions

- Scala compiler does not infer function parameter types
- If the function is recursive, you must explicitly specify the function’s result type

---

# Types

In Scala, 

- all values have a type, including numerical values and functions
- all values are objects (instances of a class)

---

# class hierarchy

![inline,95%](img/types.png)

---

# Types

- Any is the supertype of all types (defines  universal methods such as equals, hashCode, and toString)
- AnyVal represents value types Double, Float, Long, Int, Short, Byte, Char, Unit, and Boolean  (non-nullable)
- AnyRef represents reference types.  Every user-defined type in Scala is a subtype of AnyRef (corresponds to java.lang.Object)

---
# Types

```scala

val list: List[Any] = List(
  "a string",
  732,  // an integer
  'c',  // a character
  true, // a boolean value
  () => "an anonymous function returning a string"
)
list.foreach(element => println(element))

```

---

# Nothing and Null


**Nothing** is a Trait and a subtype of everything. But not superclass of anything. There are no instances of Nothing. It is used to signal non-termination such as a thrown exception, program exit, or an infinite loop etc.

**Null** is a Trait and a subtype of all reference types. There exists exactly one instance of Null, and that is _null_ .
It is provided mostly for interoperability with other JVM languages and should almost never be used in Scala code.

---

# Option/Some/None
Option has exactly 2 subclasses- Some and None. None signifies no result from the method.

>idiomatic way of initializing, setting, and accessing a variable

```scala

// 1) initialize with Option and None
var firstName = None: Option[String]

// 2) set the value with Some
firstName = Some("Al")

// 3) access the value, typically with getOrElse
println(firstName)
println(firstName.getOrElse("No name given"))
```

----

# Option/Some/None

```scala
//Exercise
def toInt(in: String): ???= {
    try {
        ???
    } catch {
        ???
    }
}

```


---

# Strings


> Built on Java’s String and adds  features like multiline literals and string interpolation

- == checks for true equality, not object reference equality
- multiline String can be created using triple-quotes """

```scala
val str = "mundo"
println(s" hola $str")
```

---


---
---
---
---
---
---
---
---
---
---
---
---
---
---
---
