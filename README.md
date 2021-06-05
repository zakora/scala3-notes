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

`def` and `val` can also be used to make functions, shown later.


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


## Collection of values: enum

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
