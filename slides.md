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
- AnyRef represents reference types.  
- Every user-defined type in Scala is a subtype of AnyRef (corresponds to java.lang.Object in JRE)


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


**Nothing** 

- is a Trait and a subtype of everything. 
- there are no instances of Nothing. 
- used to signal non-termination such as a thrown exception, program exit, or an infinite loop etc

**Null** 

- is a Trait and a subtype of all reference types. 
- there exists exactly one instance of Null, and that is _null_ .
- provided mostly for interoperability with other JVM languages and should almost never be used in Scala code
- often abused to represent an absent optional value; scala provides better altrenatives

---

# Option/Some/None

- Option[A] is a container for an optional value of type A
- If the value of type A is present, the Option[A] is an instance of Some[A]
- If the value is absent, the Option[A] is the object None

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
# Strings


> Built on Java’s String and adds  features like multiline literals and string interpolation

- == checks for true equality, not object reference equality
- multiline String can be created using triple-quotes """
- dollar sign operators ($) (with optional braces) can be used to note references to external data

```scala
val str = "mundo"
println(s" hola $str")
```

---
# Tuples

A tuple is an ordered container of two or more values, all of which may have different types.

- useful when you need to logically group values, representing them as a coherent unit
- unlike lists and arrays, however, there is no way to iterate through elements in a tuple

```scala

scala> val info = (10, "hello", true)
info: (Int, String, Boolean) = (10,hello,true)

scala> println(info._2)
hello

```

---

# Built-in Control Structures

Scala's control structures include

_if, while, for, try, match, and function calls_

 Almost all of Scala’s control structures result in some value - an approach taken by functional languages

---

# If-Else Expressions

> if (<Boolean expression>) <expression>
    else <expression>



```scala
val x = 10; val y = 20
val max = if (x > y) x else y


//What would be the type of result
val result = if ( false ) "what does this return?"

```

---

# Match Expressions

Match expressions are similar to "switch" statements, where a single input item is evaluated and the first pattern that is "matched" is executed and its value returned

Scala’s match expressions can match diverse items as types, regular expressions, numeric ranges, and data structure contents


```scala

val x = 10; val y = 20

val max = x > y match {
  case true => x
  case false => y
}
```

---
# Match Expressions

```scala
val status = 500

val message = status match {
  case 200 =>
    "ok"
  case 400 | 401 => {
    println("ERROR - we called the service incorrectly")
    "error"
  }
  case 500 => {
    println("ERROR - the service encountered an error")
    "error"
  }
}

```

---

**Matching with value/variable binding**

```scala
val stat = message match {
  case "ok" => 200
  case other => {
    println(s"Couldn't parse $other")
    -1
  }
}

```

**Matching with wildcard operator**

```scala
val stat = message match {
  case "ok" => 200
  case _ => {
    println(s"Couldn't parse $message")
    -1
  }
}


```

---

**Matching with pattern guards**

```scala

val response: String = null

response match {
  case s if s != null => println(s"Received '$s'")
  case s => println("Error! Received a null response")
}

```

**Matching with type**

```scala

val x: Int = 12180
val y: Any = x

val z = y match {
  case x: String => s"'x'"
  case x: Double => f"$x%.2f"
  case x: Float => f"$x%.2f"
  case x: Long => s"${x}l"
  case x: Int => s"${x}i"
}


```


---

# while and do/while loops

> while (<Boolean expression>) statement


```scala

var x = 0
do {
  println(s"Here I am, x = $x")
  x += 1
}
while (x < 5)

```

Scala has more expressive and more functional ways to handle loops than while and do/while loops

---


# for  comprehension

> for (<identifier> <- <iterator>) [yield] [<expression>]

If yield keyword is specified, the return value of every expression that gets invoked will be returned as a collection

```scala

val list = for (x <- 1 to 7) yield { s"day $x" }

for (day <- list) { print(s"$day, ")}

```

---

# for  comprehension

Iterator Guards

>  for (<identifier> <- <iterator> if <Boolean expression>) ...

```scala

val quote = "Faith,Hope,,Charity"
for {
  t <- quote.split(",")
  if t != null
  if t.nonEmpty
} {
  println(t)
}


```

---
# for  comprehension

Value Binding

```scala

 val powersOf2 = for (i <- 0 to 8; pow = 1 << i) yield pow

 ```

---
# Functions

Fnctions are named, reusable expressions in Scala

In functional programming a __pure function__ is one that

- Has one or more input parameters
- Performs calculations using only the input parameters
- Returns a value
- Always returns the same value for the same input
- Does not use or affect any data outside the function
- Is not affected by any data outside the function

They are stateless and orthogonal to external data such as files, databases, sockets, global variables, or other shared data

---
# Functions
It is hard to write useful applications with pure functions alone

- seek ways to reduce the number of unpure functions.
- keep unpure functions clearly named and organized in such a way that they can be easily identified

__Syntax: Defining a Function__

```scala

def <identifier>(<identifier>: <type>[, ... ]): <type> = <expression>

```

---

# Functions

A Scala function in it's basic form is a named wrapper for an expression

```scala 

def greet =  println("hola")

def greet() = {
  println("hola")
}

```

A convention in the Scala community is to omit parentheses for no-argument methods that have no side effects, like the size of a collection


---

# Functions


```scala 

// Methods which return Unit could use the non-equals syntax. 
// As of Scala-2.10, using equals sign is preferred
def greeter() {
  println("hola")
}

```

---

# Option/Some/None

```scala
//Exercise
def toInt(in: String): ??? = {
    try {
        ???
    } catch {
        ???
    }
}

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

- Scala compiler does not infer function parameter types
- If the function is recursive, you must explicitly specify the function’s result type



---
# Tail Recursion

Invoking a recursive function too many times eventually uses up all of the allocated stack space and may result in "Stack Overflow" error

Functions whose last statement is the recursive invocation itself can be optimized for tail-recursion by the Scala compiler

```scala

// rewrite to support tailrec
@annotation.tailrec
def power(x: Int, n: Int): Long = {
  if (n >= 1) x * power(x, n-1)
    else 1
}

```

---

# Functions

Scala supports

- Nested Functions
- Calling Functions with Named Parameters
- Parameters with Default Values

---
# Functions 
Vararg Parameters

```scala
def sum(items: Int*): Int = {
  ???
}

```



---

# Functions 

Parameter Groups 

```scala

def max(x: Int)(y: Int) = if (x > y) x else y
val larger = max(20)(39)

```
> more about parameter groups in currying


---

### Parameterized (Polymorphic) Functions 


```scala
    def <function-name>[type-name](parameter-name>: <type-name>): <type-name>...
```

Instead of defining functions to be used with individual types - we could  parameterize the type

```scala
def toss[A](a: A, b: A): A = {
  if (scala.util.Random.nextInt > 0) a else b
}

toss[Int] (1, 0)
toss[String] ("head", "tail")

//since scala provides type inference we can call
toss(1, 0)
toss("head", "tail")


```

---

# Functions

Scala supports only rank-1 polymorphism

```scala

def singletonList[A](a: A): List[A] = List(a)


// This does not compile, because all type variables have to be fixed at the invocation site. 
def apply[A,B](f: A => List[A], b: B, s: String): (List[B], List[String]) = (f(b), f(s))

// won't compile
def apply[A,B](f: A => List[A], b: B): (List[B]) = (f(b))

// won't compile
def apply[A](f: A => List[A], b: Int): (List[Int]) = (f(b))


```

---
# Function Types

Type of a function is a  grouping of its input types and return value type

```scala
 
 //has type (Int) => Int
 def double(x: Int): Int = x * 2

//explicit type required to distinguish it as a function value and not a function invocation
val myDouble: (Int) => Int = double

//Function types with a single parameter can leave off the parentheses
val myDouble: Int => Int = double

// wildcard operator _ serves as a placeholder for a future invocation
val myDouble = double _

```


---
# Higher order functions

Scala supports functions that accept other functions as parameters and/or use functions as return values

```scala

def safeStringOp(s: String, f: String => String) = {
  if (s != null) Some(f(s)) else None
}
def reverser(s: String) = s.reverse

println(safeStringOp("-", reverser).getOrElse("-"))

```

---
# Lambdas / Anonymous functions
 
is a function literal which lacks a name

```scala

val doubler = (x: Int) => x * 2

```

---

# Lambdas / Anonymous functions

```scala

def replicate[T] : ( Int, T, List[T] ) => List[T] = {
  ( n :Int, t :T, list :List[T] ) => {
    if( n <= 0 ) list else t :: replicate( n-1, t, list )
  }
}

// Nil Represents an emptry List 
val repeatedStrs : List[String] =  ???
val repeatedNums : List[Int] = ???

```

---

# Placeholder Syntax

shortened form of lambdas - replacing named parameters with wildcard operators (_)

```scala
scala> val doubler: Int => Int = _ * 2
doubler: Int => Int = <function1>

```


- the explicit type of the function is specified outside the literal  
- the parameters are used no more than once
- we can use two or more underscores to refer different parameters

---

# Placeholder Syntax

```scala

def safeStringOp(s: String, f: String => String) = {
  if (s != null) Some(f(s)) else None
}

// operationally the same as s => s.reverse, but simplified with placeholder syntax
safeStringOp("ab", _.reverse)
safeStringOp(null, _.reverse)

// multiple params

def combination(x: Int, y: Int, f: (Int,Int) => Int) = f(x,y)

combination(23, 12, _ * _)
combination(23, 12, _ + _)

```
---

# Partially Applied Functions 

Partially Applied Functions and Currying gives a way to reuse a function invocation and retain some of the parameters


```scala

 def factorOf(x: Int, y: Int) = y % x == 0
 
 //shortcurt
 val f = factorOf _

//partially apply the function by using
//wildcard operator to take the place of one of the parameters
val isEven = factorOf(2, _: Int)

val y = isEven(78)
 ```


---

# Currying

Currying is a cleaner way to partially apply functions

Instead of breaking up a parameter list into applied and unapplied parameters, define functions with multiple parameter lists

```scala

def factorOf(x: Int)(y: Int) = y % x == 0

val isEven = factorOf(2) _

isEven(32)
```


A function with multiple parameter lists is considered to be a chain of multiple functions. Each separate parameter list is considered to be a separate function call.

---

# Closures

In scala, we could refer to variables defined  outside of the scope of the function

```scala
val addMore = (x: Int) => x + more

// x is a bound variable because it has a meaning in the context of the function
// more is a free variable because the function literal does not itself give a meaning to it

var more = 1
val addMore = (x: Int) => x + more
addMore(10)

```
 The term "closure" arises from the act of "closing" the function literal by "capturing" the bindings of its free variables.

---

# Functions


```scala

def factorOf(x: Int, y: Int) = y % x == 0
val isEven = factorOf(2, _: Int)
isEven(42)

def factorOf(x: Int)(y: Int) = y % x == 0
val isEven1 = factorOf(2) _
isEven1(42)

def factorOf(x: Int) = (y:Int) => y % x == 0
val isEven = factorOf(2)
isEven(42)

```

---

# Parameters by Name

a by-name parameter can take either 

- a value or 
- a function that eventually returns the value

```scala

def doubles(x: => Int) = {
  println("Now doubling " + x)
  x * 2
}
def f(i: Int) = { println(s"Hello from f($i)"); i }
doubles(5)
doubles( f(8) )

```

---

# Partial Functions

---
# Functions vs Methods

Exercise