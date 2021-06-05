# Scala 3 Notes

This document contains various bits of information about Scala, most (all?) are using the version 3 of Scala.

Most of these notes were written while watching the [Effective Programming in Scala](https://www.coursera.org/learn/effective-scala) course.

## Variables

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

## Associtive array: Map

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


