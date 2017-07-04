autoscale: true


![inline, 75%](img/logo.png)

#[fit] A Scala Primer For Spark - Part II

---

# Classes

---

# Classes, fields, and methods

A class is a blueprint for objects. Once you define a class, you can create objects from the class blueprint with the keyword new.

```scala

class ChecksumAccumulator {
  // members (fields and methods)
  // go here
}

new ChecksumAccumulator
```

---
# Classes, fields, and methods

```scala

class ChecksumAccumulator {
  //Scala’s default access level is public
  private var sum = 0

  // it is often better to explicitly provide the result types of
  // public methods even when the compiler would infer it for you
  def add(b: Byte): Unit = sum += b
  def checksum(): Int = ~(sum & 0xFF) + 1
}

val chk = new ChecksumAccumulator

chk.add('a')
chk.add(10)
chk.checksum


```

---

# Functional Objects

Functional objects are objects that do not have any mutable state

Rational number :- a number that can be expressed as a ratio n/d , where n and d are integers, except that d cannot be zero.

```scala

//class parameters -> similar to function parameters
class Rational(n: Int, d: Int)

val oneHalf = new Rational(1, 2)

```

---

# Class parameters

Class parameters are available for initializing fields (values and variables in a class) or for passing to functions,  but once the class has been created the parameters aren’t available

The Scala compiler will gather the class parameters and any code you place in the class body, which isn’t part of a field or a method definition and create the primary constructor

```scala
class Rational(n: Int, d: Int) {
  println("Created " + n + "/" + d)
}
```

---

# Classes Java vs Scala

In Java, classes have constructors, which can take parameters; whereas in Scala, classes can take parameters directly

The Scala notation is more concise—class parameters can be used directly in the body of the class; there’s no need to define fields and write assignments that copy constructor parameters into fields

This can yield substantial savings in boilerplate code, especially for small classes

---

# Notes

Scala implicitly imports members of packages java.lang, scala, and singleton object named Predef, into every Scala source file

Predef, which resides in package scala, contains many useful methods like println and require

---

# Functional Objects

A precondition is a constraint on values passed into a method or constructor, a requirement which callers must fulfill


```scala

class Rational(n: Int, d: Int) {
  require(d != 0)
  override def toString = n + "/" + d
}

```

---

# Adding Fields

By adding the keywords val or var before a class parameter, the class parameter then becomes a field in the class.

```scala

class Rational(val n: Int, val d: Int) {
  require(d != 0)
  override def toString = n + "/" + d

  def add(that: Rational): Rational =
    new Rational(n * that.d + that.n * d, d * that.d)
}

val n1 = new Rational(10, 20)
val n2 = new Rational(30, 60)
n1.add(n2)

```

---

#Defining operators


```scala

class Rational(n: Int, d: Int) {
  require(d != 0)
  private val g = gcd(n.abs, d.abs)

  val numer = n / g
  val denom = d / g

  def this(n: Int) = this(n, 1)

  def +(that: Rational): Rational =
    new Rational(numer * that.denom + that.numer * denom, denom * that.denom)

  def *(that: Rational): Rational = new Rational(numer * that.numer, denom * that.denom)

  override def toString = numer + "/" + denom

  private def gcd(a: Int, b: Int): Int =
    if (b == 0) a else gcd(b, a % b)
}

```

---

#Defining operators

```scala

class Rational(n: Int, d: Int) {
  require(d != 0)

  private val g = gcd(n.abs, d.abs)

  val numer = n / g
  val denom = d / g

  def this(n: Int) = this(n, 1)

  def +(that: Rational): Rational =
    new Rational(numer * that.denom + that.numer * denom, denom * that.denom)

  def *(that: Rational): Rational = new Rational(numer * that.numer, denom * that.denom)

  override def toString = numer + "/" + denom

  def *(i: Int): Rational =  new Rational(numer * i, denom)

  private def gcd(a: Int, b: Int): Int =
    if (b == 0) a else gcd(b, a % b)
}

val r = new Rational(1,2)

r * 2

2 * r

```

---

# Implicit conversions

2 * r is equivalent to 2.*(r). Int class contains no multiplication method that takes a Rational argument

An implicit conversion can automatically convert integers to rational numbers when needed

```scala

implicit def intToRational(x: Int) = new Rational(x)

val r = new Rational(1,2)

println(3 * r)

```
Because implicit conversions can have pitfalls if used indiscriminately the compiler warns when compiling the implicit conversion definition. To turn off the warnings take either of these actions:

- Import scala.language.implicitConversions into the scope of the implicit conversion definition
- Invoke the compiler with -language:implicitConversions

---

#Auxiliary constructors

In Scala, every auxiliary constructor must invoke another constructor of the same class as its first action. 

```scala

class Rational(n: Int, d: Int) {
  require(d != 0)
  val numer: Int = n
  val denom: Int = d

  // auxiliary constructor
  def this(n: Int) = this(n, 1) 

  //...
}

```

---

# Apply Methods

Methods named "apply"  referred to as a default method or an injector method, can be invoked without the method name

```scala

class Multiplier(factor: Int) {
  def apply(input: Int) = input * factor
}

val tripleMe = new Multiplier(3)

val tripled = tripleMe(10)


```

---

# Lazy Values

Lazy values, are only created the first time they are instantiated. 
They are in a sense a cached function result.

```scala

class RandomPoint {
  val x = { println("creating x"); util.Random.nextInt }
  lazy val y = { println("now y"); util.Random.nextInt }
}

val p = new RandomPoint()

println(s"Location is ${p.x}, ${p.y}")

println(s"Location is ${p.x}, ${p.y}")

```

Lazy values are a great way to ensure that time/performance-sensitive operations can be executed only once in a class’s lifetime. They are popularly used to store file-based properties, open database connections, and other immutable data that should only be initialized if it is really necessary. By initializing this data in a lazy val’s expression, you can ensure that it will only operate if the lazy val is accessed at least once in the class instance’s lifetime.


---

# Nested CLasses

Like expressions and functions, classes can be nested inside each other. 
A nested class may access the fields and methods of its parent class(es) in addition to its own.


---

# Abstract Classes

An abstract class is a class designed to be extended by other classes but not instantiated itself. 
It is useful to create a usable base type while delegating the implementation.

```scala

abstract class Car {
  val year: Int
  val automatic: Boolean = true
  def color: String
}

class RedMini(val year: Int) extends Car {
  def color = "Red"
}

class Mini(val year: Int, val color: String) extends Car

```

---

# Anonymous Classes

```scala

abstract class Listener { def trigger }

class Listening {
  var listener: Listener = null
  def register(l: Listener) { listener = l }
  def sendNotification() { listener.trigger }
}

val notification = new Listening()

notification.register(new Listener {
  def trigger =  println(s"Trigger at ${new java.util.Date}") 
 }
)
notification.sendNotification()

```


---

# Packages

Packages are Scala’s (and Java’s) system for code organization. 
They make it possible to organize Scala code by directory using period-separated paths. Use the package keyword at the top of a Scala source file to declare all classes in that file to be included in the package.

```scala

package <identifier>

```

---

# Packages


```scala

//importing the entire contents of a package
import collection.mutable._

// selected classes
import collection.mutable.{Queue,ArrayBuffer}

// Alias
import collection.mutable.{Map=>MutMap}
```


---

# Packaging Syntax

```scala

//less common
package com {
  package hifx {
    class Config(val baseUrl: String = "http://localhost")
  }
}
```

---

# Privacy Controls

protected :- limits access to code from the same class or its subclasses

private :- to limit their access to only the class in which they are defined


```scala

package com.hifx.example
 
// Private top-level class
private class Example {
  def sayHello(): Unit = println("Hello World")
}

```

---


# Qualified access

```scala

package outside {
  package inside {
    object Messages {
      // Accessible up to package 'inside'
      private[inside] val Insiders = "Hello Friends"

      // Accessible up to package 'outside'
      private[outside] val Outsiders = "Hello People"
    }

    object InsideGreeter {
      def sayHello(): Unit =
        // Can access both messages
        println(Messages.Insiders + " and " + Messages.Outsiders)
    }
  }

  object OutsideGreeter {
    def sayHello(): Unit =
      // Can only access the 'Outsiders' message
      println(inside.Messages.Outsiders)
  }
}

```

---

# Final and Sealed Classes

Final class members can never be overridden in subclasses. Marking a value, variable, or method with the final keyword ensures that the implementation is the one that all subclasses will use. 
Entire classes can be marked as final as well, preventing any possible subclasses of that class


Sealed classes restrict the subclasses of a class to being located in the same file as the parentclass. 
By sealing a class, you can write code that makes safe assumptions about its hierarchy.


---

# Extending classes


```scala

abstract class Element {
  def contents: Array[String]
  val height = contents.length
  val width = if (height == 0) 0 else contents(0).length
}

class ArrayElement(conts: Array[String]) extends Element {
  def contents: Array[String] = conts
}

class ArrayElement(conts: Array[String]) extends Element {
  val contents: Array[String] = conts
}

class ArrayElement(val contents: Array[String]) extends Element

```

---

# Using composition and inheritance

Composition and inheritance are two ways to define a new class in terms of another existing class. 

If what you’re after is primarily code reuse, you should in general prefer composition to inheritance

Inheritance suffers from the fragile base class problem, where can break subclasses by changing a superclass

---

# Objects

- An object is a type of class that can have no more than one instance (singleton)
- Instead of creating an instance with a new keyword, access the object directly by name
- An object gets automatically instantiated the first time it is accessed in a running JVM

Languages like Java provides the ability to designate certain fields and methods of a class as being "static" or "global"  meaning that they are not tied to an instance's data and so can be accessed without instantiating a class

Objects provide similar functionality - but with cleaner separation

---

#Objects

- Objects and classes are not completely decoupled. An object can extend another class, but an object cannot itself be extended
- Objects do not take any parameters as they are automatically instantiated

---

#Objects

```scala

object Hello {
  println("in Hello")
  def hi = "hi"
}
// accessed for the first time
// object instantiation/initialization occurs
println(Hello.hi)

// reuses the same global instance 
// no additional initialization
println(Hello.hi)


```

---

#Objects

```scala

object HtmlUtils {
  def removeMarkup(input: String) =
    input.replaceAll("""</?\w[^>]*>""","").replaceAll("<.*>","")
}
val html = "<html><body><h1>Introduction</h1></body></html>"
val text = HtmlUtils.removeMarkup(html)

```

---

#Companion Objects


A companion object is an object with the same name as a class or trait and is defined in the same source file as the associated file or trait. 

A companion object differs from other objects as it has access rights to the class/trait that other objects do not


One of the most common uses of a companion object is to define factory methods for class.

---

#Companion Objects

```scala

class DBConnection {
  private val props = Map(
    "url" -> DBConnection.url,
    "user" -> DBConnection.user,
    "pass" -> DBConnection.pass
  )
  println(s"Created new connection for " + props("url"))
}

object DBConnection {
  private val url = "jdbc://localhost"
  private val user = "usr"
  private val pass = "pwd"
  def apply() = new DBConnection
}

val conn = DBConnection()


```


---

#Companion Objects

```scala

class DBConnection {
  private var url = "jdbc://localhost"
  private var user = "usr"
  private var pass = "pwd"
}

object DBConnection {
  def apply(url:String, user:String, pass:String) = {
    // additional checks
    val db = new DBConnection()
    db.url = url
    db.pass = pass
    db.user = user
    db
  }
}

val conn = DBConnection("jdbc://", "user", "pwd")


```

---

#Companion Objects

```scala

class DBConnection(val url:String, val user:String, val pass:String)

object DBConnection {
  def apply(url:String, user:String, pass:String) = {
      // additional checks
      new DBConnection(url, user, pass)
  }
}

```


---


# Command-Line Applications

```scala

object Date {
  def main(args: Array[String]) {
    println(new java.util.Date)
  }
}

```

---

# Case Classes


- A case class is an instantiable class that includes several automatically generated methods
- It also includes an automatically generated companion object with its own automatically generated methods
- All methods in the class and companion object are based on the class's parameter list
- Case classes work great for data transfer objects
- They don’t work well in hierarchical class structures

---

# Case Classes

The benefit that case classes bring is convenience

Name | Location | Description
---|---|---|
apply | Object | A factory method for instantiating the case class
copy | Class | Returns a copy of the instance with any requested changes. The parameters are the class’s fields with the default values set to the current field values
equals | Class | Returns true if every field in another instance match every field in this instance. Also invocable by the operator ==
hashCode | Class | Returns a hash code of the instance’s fields, useful for hash-based collections.
toString | Class | Renders the class’s name and fields to a String.
unapply | Object | Extracts the instance into a tuple of its fields

---

# Case Classes

```scala

case class Calculator(brand: String, model: String)

val hp20b = Calculator("HP", "20b")
val hp20B = Calculator("HP", "20b")
val hp30b = Calculator("HP", "30B")

hp20b == hp20B


def calcType(calc: Calculator) = calc match {
  case Calculator("HP", "20B") => "financial"
  case Calculator("HP", "48G") => "scientific"
  case Calculator("HP", "30B") => "business"
  case Calculator(ourBrand, ourModel) => "Calculator: %s %s is of unknown type".format(ourBrand, ourModel)
  //or
  case Calculator(_, _) => "Calculator of unknown type"
  //or
  case _ => "Calculator of unknown type"
  //or
  case c@Calculator(_, _) => "Calculator: %s of unknown type".format(c)
}

```

---

# Traits

- Traits in Scala are best described as "interfaces that can provide concrete members"
- Classes, Objects, and Traits cannot extend more than one class but can extend multiple traits at the same time
- A class importing two traits that have the same field or method, but lack an override keyword, will fail to compile.

---

# Traits

```scala

trait Base { override def toString = "Base" }
class A extends Base { override def toString = "A->" + super.toString }
trait B extends Base { override def toString = "B->" + super.toString }
trait C extends Base { override def toString = "C->" + super.toString }
class D extends A with B with C { override def toString = "D->" + super.toString }
new D()

```

---

# Mixin-class composition

```scala
abstract class AbsIterator {
  type T
  def hasNext: Boolean
  def next: T
}

trait RichIterator extends AbsIterator {
  def foreach(f: T => Unit) { while (hasNext) f(next) }
}

class StringIterator(s: String) extends AbsIterator {
  type T = Char
  private var i = 0
  def hasNext = i < s.length()
  def next = { val ch = s charAt i; i += 1; ch }
}

def split(str: String) {
  
  class Iter extends StringIterator(str) with RichIterator
  val iter = new Iter
  //or
  class Iter extends StringIterator(str)
  val iter = new Iter with RichIterator //dependency injection ?

  iter foreach println
}

split("malayalam")

```

---

# Self Types

A self type is a trait annotation that asserts that the trait must be mixed in with a specific type, or its subtype, when it is added to a class

```scala

class A { def hi = "hi" }

trait B { self: A =>
  override def toString = "B: " + hi
}

class C extends B

```

---

# Importing Instance Members

The import keyword can also be used to import members of classes and objects into the current namespace. This makes it possible to access them directly without specifying their enclosing instance (for classes) or name (for objects)


```scala

case class Receipt(id: Int, amount: Double, who: String, title: String)
val latteReceipt = Receipt(123, 4.12, "fred", "Medium Latte")
import latteReceipt._
println(s"Sold a $title for $amount to $who")


```

---

# Implicits in depth

Implicits in Scala refers to either 

- a conversion from one type to another that is made automatically
- or a value that can be passed "automatically"

---

# Implicit Conversion

If one calls a method __m__ on an object __o__ of a class __C__, and that class does not support method __m__, then Scala will look for an implicit conversion from __C__ to something that does support __m__

```scala
"abc".map(_.toInt)
```

String does not support the method map, but StringOps does, and there’s an implicit conversion from String to StringOps available (see implicit def augmentString on Predef)

```scala
implicit def augmentString(x: String): StringOps = new StringOps(x)
```

---

# Implicit Conversion

```scala

implicit def doubleToInt(x: Double) = x.toInt
val i: Int = 3.5

```

---

# Implicit Parameters

Implicit Parameters are passed to method calls like any other parameter, but the compiler tries to fill them in automatically. If it can’t, it will complain.

```scala

object ImplicitParams {
  def greet(name: String)(implicit greeting: String) = s"$greeting, $name"
  implicit val hi = "Hello"
  def test = {
    println( greet("Developers") )
  }
}

ImplicitParams.test

```

----

# Implicit Classes

```scala
object ImplicitChk {
  implicit class Hello(s: String) { def hello = s"Hello, $s" }
  def test = {
    println( "World".hello )
  }
}

ImplicitChk.test
```


----

# Type Aliases

A type alias creates a new named type for a specific, existing type (or class).

```scala

object TypeFun {
  type Whole = Int
  val x: Whole = 5

  type UserInfo = Tuple2[Int,String]
  val u: UserInfo = new UserInfo(123, "George")

  type T3[A,B,C] = Tuple3[A,B,C]
  val things = new T3(1, 'a', true)
}

```


---

#  Type Parameterization

```scala

abstract class IntStack {
  def push(x: Int): IntStack = new IntNonEmptyStack(x, this)
  def isEmpty: Boolean
  def top: Int
  def pop: IntStack
}

class IntEmptyStack extends IntStack {
  def isEmpty = true
  def top = error("EmptyStack.top")
  def pop = error("EmptyStack.pop")
}

class IntNonEmptyStack(elem: Int, rest: IntStack) extends IntStack {
  def isEmpty = false
  def top = elem
  def pop = rest
}

```

---

# Generic Version

```scala
abstract class Stack[T] {
  def push(x: T): Stack[T] = new NonEmptyStack[T](x, this)
  def isEmpty: Boolean
  def top: T
  def pop: Stack[T]
}
class EmptyStack[T] extends Stack[T] {
  def isEmpty = true
  def top = error("EmptyStack.top")
  def pop = error("EmptyStack.pop")
}
class NonEmptyStack[T](elem: T, rest: Stack[T]) extends Stack[T] {
  def isEmpty = false
  def top = elem
  def pop = rest
}
```

---

# Generic Method

```scala

def isPrefix[T](p: Stack[T], s: Stack[T]): Boolean = {
  p.isEmpty || p.top == s.top && isPrefix[T](p.pop, s.pop)
}

```

---

# Type Variance

A type parameter of a class or trait can be marked with a variance annotation, either covariant ( + ) or contravariant ( - ). Such variance annotations indicate how subtyping works for a generic class or trait.


```scala
class Parent

class Child extends Parent

```


---

# in-variant

A type parameter of a class or trait is by default in-variant


```scala

final class Array[T] extends java.io.Serializable with java.lang.Cloneable 

def foo(x : Array[Any]) = println(x.length)

foo(Array[Int](1.2,2,3))

```

---

# in-variant

```scala
//in-variant or non-variant
class C[T]
val x: C[Parent] = new C[Parent]
val x: C[Parent] = new C[Child]
val x: C[Child] = new C[Parent]

```

----

# co-variant

A covariant annotation can be applied to a type parameter of a class or trait by putting a plus sign ( + ) before the type parameter. The class or trait then subtypes covariantly with in the same direction as the type annotated parameter.

If “S” is subtype of “T” then List[S] is is a subtype of List[T].



```scala
sealed abstract class List[+A] extends AbstractSeq[A] with LinearSeq[A]

```

----

# co-variant

```scala
//co-variant
class C[+T] 
val x: C[Parent] = new C[Parent]
val x: C[Parent] = new C[Child]

val x: C[Child] = new C[Parent]
```

---

Arrays in Java are covariant

```java
String[] strings = { "abc" };
Object[] objects = strings;
// OOPS! Line below throws runtime exception: ArrayStoreException.
// Reason is that objects is actually an instance of 
// Array of Strings and we try to update it with an Integer.
objects[0] = 1;

```

---

#Contravariant 

A contravariant annotation can be applied to a type parameter of a class or trait by putting a minus sign ( - ) before the type parameter. The class or trait then subtypes contravariantly with in the opposite direction as the type annotated parameter. 

If "S" is subtype of "T" then List[T] is is a subtype of List[S].

```scala

class C[-T]
val x: C[Parent] = new C[Parent]
val x: C[Child] = new C[Parent]

val x: C[Parent] = new C[Child]
```

---

# Upper or lower bounds

```scala

// This won't compile
class Company[+T](val company:T) {
  def partnerWith(y: T):T  = y
}

```

---

# Lower type bounds:
help you to declare a type to be a supertype of another type. 
T >: A indicates that type T or abstract type T refers to a supertype of type A


```scala

class Company[+T](val company:T) {
  def partnerWith[U >: T](y: U) {

  }
}

```

---

# Upper bound types

```scala
class Company[-T](val company:T) {
  def partnerWith[U <: T](y: U) {
  }
}

```



---

# Type erasure


Type erasure is a procedure performed by Java and Scala compilers which removes all generic type information after compilation. This means that we are not able to differentiate between, say, List[Int] and List[String] at runtime.

Type erasure exist for historical reasons. Java didn’t support generics from the beginning. So when they finally added them in Java 5, they had to keep the backward compatibility in mind. They wanted to allow seamless interfacing with old, non-generic legacy code


---

# Type erasure


```scala

object Extractor {
  def extract[T](list: List[Any]) = list.flatMap {
    case element: T => Some(element)
    case _ => None
  }
}

val list = List(1, "string1", List(), "string2")
val result = Extractor.extract[String](list)
println(result) // List(1, string1, List(), string2

```

---

# Type erasure

From a strictly language point of view (without going into runtime details), this code is reasonable. We know that pattern matching is able to figure out the type of a given object without problems by deconstructing it. However, due to program being executed on JVM, all generic types are erased after compilation. Therefore pattern matching cannot really get far; everything beyond the “first level” of type is erased. Matching our variable directly on Int or String (or any non-generic type, such as MyNonGenericClass) would work fine, but matching it on T, where T is a generic parameter, cannot work. Compiler will give us a warning saying “abstract type pattern T is unchecked since it is eliminated by erasure”.

---

# Existential types
