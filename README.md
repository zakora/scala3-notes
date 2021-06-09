# Scala 3 Notes

This document contains various bits of information about Scala, most (all?) are using the version 3 of Scala.

Most of these notes were written while watching the [Effective Programming in Scala](https://www.coursera.org/learn/effective-scala) course.

## Value binding

`def` can be used to name values. It will be evaluated everytime it is called, but only when needed (lazy).

`val` is similar in the sense that it can put a name on values. However, it is evaluated only once, and done so even if it is never called.
 
```scala
def abc = 123
val test = 12.0
```


## Functions

Both `val` and `def` can be used to make functions.

```scala
val myFunc: Int => Int =
  val incr = 100
  x => x + incr

def add1(n: Int): Int = n + 1
  
// Other signature form, without a return type
val myAdd =
  (x: Int, y: Int) => x + y
```

`val` functions can be passed around and returned, functional programming style. This is not possible with `def`.


## Compound types: case classes

Record-like data structure.

```scala
case class Rectangle(width: Double, height: Double)
val rect = Rectangle(10, 2)
````

We can put functions inside them:
```scala
case class Square(width: Double):  // note the ':' here
  val area = width * width

val sq = Square(width=10)
sq.area
// returns:
// val res2: Double = 100.0
```


## Abstract types: sealed trait

Derive a case class from a sealed trait.

```scala
sealed trait Shape
case class Rectangle(w: Double, h: Double) extends Shape
case class Circle(r: Double) extends Shape
```

Useful with pattern matching:
```scala
val shape: Shape = Circle(1.0)  // note the explicit typing to Shape
val area =
  shape match
    case Rectangle(w, h) => w * h
    case Circle(r)       => 3.14 * r * r
// returns:
// val area: Double = 3.14
```


## Collection of variants: enum

Define a type that can take only the given possible values.
Pattern matching is also useful here.

```scala
enum PrimaryColor:
  case Red, Blue, Green

val myColor = PrimaryColor.Red
val test =
  mycolor match
    case PrimaryColor.Red => 0
    case _                => 1  // note the wildcard match
// returns:
// val test: Int = 0
```

Some enum methods:
```scala
PrimaryColor.values  // list possible values for that type
PrimaryColor.valueOf("Green")  // string -> value type, useful for programmatic access
```

## Collection of values: List

List holds values of the same type. They are implemented as linked-list and are immutable.

```scala
val mylist = List(1, 2, 3)  // note the type inference
// returns:
// val mylist: List[Int] = List(1, 2, 3)
```

Prepending an item is O(1) since we are effectively using a linked-list:
```scala
val myBuddies = List("Boycat", "Caroline")
val allBuddies = "Arno" :: myBuddies
val anEmptyList = List.empty[Int]
```

The `::` operator is useful with pattern matching:
```scala
allBuddies match
  case head :: tail => println(head)
  case _            => println("Oops")
// prints:
// Arno
```

Accessing elements:
```scala
allBuddies.head  // returns: val res14: String = Arno
allBuddies.tail.head  // returns: val res15: String = Boycat
allBuddies(1)  // linear time for random access by index
```

Some useful methods on List:
```scala
mylist.size  // O(n) operation
mylist.contains(2)
mylist.map( x => x + 1 )
mylist.filter( x => x < 2)
```


## Collection of values: Tuple

Tuple contains a fixed set of values. Values can be of different types (so not like List).

```scala
val triple = ("Hello", 1, false)
```

Pattern matching on tuple to extract values:
```scala
val (_, number, _) = triple  // note the wildcard to discard some values
// returns:
// val number: Int = 1
```

## Associative array: Map

Creating a Map:
```scala
Map("a" -> true, "b" -> false)  // keys must be of the same type, values must be of the same type
// returns:
// val res18: Map[String, Boolean] = Map(a -> true, b -> false)

// Or initialize an empty map:
val emptyMap = Map.empty[String, Boolean]
```


## Mutable collections

By default only immutable collections (such as List) are imported.

Mutable collections (e.g. ArrayBuffer) are availabe with an import:
```scala
import scala.collection.mutable
val myEmptyArray = mutable.ArrayBuffer.empty[Double]

// Initialize with values
val myArray = mutable.ArrayBuffer("a", "b", "c")
```


## Operations on collections

Scala provides common collection operations.

Some other collection operations:
```scala
val myList = List(1, 2, 3, 4)
myList.size                      // returns: val res32: Int = 4
myList.isEmpty                   // returns: val res33: Boolean = false
myList.nonEmpty                  // returns: val res34: Boolean = true
myList.contains(3)               // returns: val res35: Boolean = true
myList.find(x => x % 2 == 0)     // returns: val res36: Option[Int] = Some(2)
myList.filter(x => x % 2 == 0)   // returns: val res37: List[Int] = List(2, 4)
myList.exists(x => x % 2 == 0)   // returns: val res55: Boolean = true
myList.forAll(x => x % 2 == 0)   // returns: val res56: Boolean = false

// Get an element by index (or by key for Map)
myList.apply(2)                  // returns: val res59: Int = 3
myList(2)                        // returns: val res60: Int = 3

// On sequence collections
List(1, 2).head  // returns: 1
mutable.ArrayBuffer("a", "b").tail  // returns: ArrayBuffer(b)

// Operating on a Map is done with (key, value) => ...
Map("a" -> 1, "b" -> 1, "c" -> 2).map((key, value) => key -> (value + 1))
```

Functional programming related operations:
```scala
myList.flatMap(x => List(x, x + 100))  // output size may not be input List size
// returns:
// val res38: List[Int] = List(1, 101, 2, 102, 3, 103, 4, 104)
```

```scala
List(1, 2, 3).foldLeft(0)((acc, elt) => (acc + elt))
// returns:
// val res40: Int = 6

// Same foldLeft but using braces, useful for longer inner function
List(1, 2, 3).foldLeft(0) {
  (acc, elt) =>
    acc + elt
}
```

```scala
myList.groupBy(x => x % 2 == 0)
// returns:
// val res42: Map[Boolean, List[Int]] = HashMap(false -> List(1, 3), true -> List(2, 4))
```

Other collections:
- `HashMap`: a mutable Map
- `BitSet`
- `LazyList`

It's more idiomatic to reach for immutable collections first, and only turn to mutable collections when needed.


## Adding elements to collection

Add a single element:
```scala
// Prepending (+:) and appending (:+) on sequence collections
0 +: List(1, 2) :+ 3  // returns: List(0, 1, 2, 3)
"a" +: mutable.ArrayBuffer("b", "c") :+ "d"  // returns: ArrayBuffer(a, b, c, d)
```

```scala
// Adding (+) one element to a Map
Map("a" -> true, "b" -> false) + ("c" -> true)  // a single element is added
// returns:
// val res28: Map[String, Boolean] = Map(a -> true, b -> false, c -> true)
```

Concatenating `++` two collections of the same type:
```scala
val origMap = Map("a" -> true, "b" -> false)
origMap ++ Map("b" -> true, "d" -> true)  // many elements added or updated
// returns:
// val res31: Map[String, Boolean] = Map(a -> true, b -> true, d -> true)

origMap  // note that with (++) the original collections is unchanged
// returns:
// val res51: Map[String, Boolean] = Map(a -> true, b -> false)
```

On mutable collections, `++=` and `+=` are used for concatenating an adding in-place.
```scala
val myMutable = mutable.ArrayBuffer(1, 2, 3)
myMutable += 4
myMutable ++= List(5, 6)
// returns:
// ArrayBuffer(1, 2, 3, 4, 5, 6)
```

## Handling possible null: Option

Dealing with null values by using `Option`, which match either `Some(x)` or `None`.

```scala
List(1, 2, 3, 4).find(x => x == 3) match
  case Some(x) => println("We found it! " + x)
  case None    => println("Not there")
// prints:
// We found it! 3
```

Provide a default on `None` with `.getOrElse`:
```scala
List(1, 2, 3, 4).find(x => x == 5).getOrElse(10)
// returns:
// val res49: Int = 10
```


## Iterating

Range construction:

```scala
1 to 4           // 1, 2, 3, 4
0 to 10 by 2     // 0 2 4 6 8 10
5 until 0 by -1  // 5, 4, 3, 2, 1  (0 is excluded)
```

```scala
val factorial: Int => Int =
  n => (1 to n).foldLeft(1)((acc, elt) => acc * elt)
factorial(4)
// returns:
// val res67: Int = 24
```

Looping with `for … yield` (the compiler will turn this into code using `.map`, `.flatMap` and a lazy filter):
```scala
val twodee = List(List(0, 1), List(2, 3, 4, 5), List(6, 7))

// for (s) yield e
// s: sequence of generators and filters
//    generator   p <- e   are pattern matched
// e: expression
for
  sublist <- twodee   // collection
  number <- sublist   // nested collection
  if number % 2 == 0  // guard
yield number

// returns:
// val res68: List[Int] = List(0, 2, 4, 6)
```

`for … do` is synthetic sugar for a `.foreach`:
```scala
for sublist <- twodee do println(sublist)

// prints:
// List(0, 1)
// List(2, 3, 4, 5)
// List(6, 7)
```

Looping on condition is done with `while … do`:
```scala
var ii = 0
while ii < 10 do
  println(ii)
  ii += 1

// prints: numbers from 0 to 9
```


## Tail call recursion

Recursion can be used as an alternative to `for` and `while` looping constructs.

```scala
val factorial: Int => Int =
  n =>
    n match
      case 0 => 1
      case _ => n * factorial(n - 1)  // note the call to itself
```

Use tail call recursion to avoid StackOverflowError:
```scala
def factorialTC(n: Int): Int =
  def rec(n: Int, acc: Int): Int =
    n match
      case 0 => acc
      case _ => rec(n - 1, n * acc)  // note the tail call: the recursive function call is the *only* expression
  rec(n, 1)
```
