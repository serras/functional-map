---
layout: default
title: "Algebraic data types"
permalink: /concepts/pattern-matching
---

An Algebraic Data Type (ADT) is a language feature to introduce a new type, together with all possible ways to build a value of that type.

An Algebraic Data Type (ADT) declares a type of values and several alternative (*) _value constructors_, 
which is to say, distinctive ways to build a value of the ADT using different elements.

An ADT declares _all_ possible forms that values in the ADT can have. Thus, they are a **closed** data model, which means that outside 
of the ADT there is no way to _extend_ it with alternative constructors. 
This is in contrast to abstract classes in object-oriented languages, such as Java or Go, 
which are **open** for the user to extend with new subclasses.


Because an ADT can be seen as an alternative of records, where each record is a combination of several fields, 
in the "categorical" jargon ADTs are often described as "sum of products".


## Cross-language comparison


### Haskell


### Scala 2

The Scala language (version 2) provides low-lever support for ADTs using sealed traits and case classes (or objects).

- The `sealed` keyword constrains the programmer to declare _every_ subclass 
  of the sealed class within the same text file as the sealed class. 
  Thus, it is a meta-textual low-level mechanism to enforce the _closed_ principle of ADT.

- The `case` keyword for a class indicates that the class is like a normal record, with 
  every field being immutable and public, and a copy method.


#### Value constructors vs types.

Note that, unlike in Haskell, each case class in Scala does not merely declare a value constructor for a type, 
but it also declares a type. For example, `Some[A]` is a subtype of `Option[A]` and thus a way to build a 
value of the ADT `Option[A]`, but `Some[_]` is also a distinct type to Scala type-system.

Thus, one can declare variables, class fields, function parameters, or function result types as `Some`: 

```Scala
def dwarves(fili: Some[Int]): Some[Char] = {
  val kili: Some[Char] = Some('a')
  kili
}
```

Whereas in Haskell, a type declaration can only refer to `Maybe`, not to `Just`. `Just` does not exist in Haskell's type-level, 
and the following Haskell equivalent is rejected:

```Haskell
dwarves :: Just Int -> Just Char 
dwarves fili = let kili = Just 'a' in kili
```

This makes for some interesting use of case annotations... one can declare a record in which some fields are optional, 
but in some places declare that the fields are known to be present, as follows: 

``` scala
case class Coffee[F[_] <: Option[_]](coffee: Int, milk: F[Int])

val latte: Coffee[Some] = Coffee(5, Some(3))
val noir: Coffee[None] = Coffee(5, None)
```

### Scala 3 

Scala 3 introduces a higuer-level support for [ADT](https://dotty.epfl.ch/docs/reference/enums/adts.html), 
as a natural extension of enumerations. This new syntax is closer to Haskell, in that value constructors (subclasses)
are contained within the top-level declaration of the ADT. However, every "value constructor" is still a type of its own.


### Kotlin

Kotlin provides constructs similar to those in Scala 2, for supporting ADTs, such as 
[sealed classes](https://kotlinlang.org/docs/reference/sealed-classes.html), 
and case classes (or [_data_ classes](https://kotlinlang.org/docs/reference/data-classes.html).


### Java

Traditionally, Java lacked support for ADTs, since its type system was centered on subtyping and _open-closed_ object-oriented inheritance.
However, several recent proposals for language evolution are improving the support for ADTs.

- [JEP 360](https://openjdk.java.net/jeps/360) introduces a form of sealed classes. Like Scala, each _value constructor_ 
is itself a distinct type from that of the sealed class. Like Haskell, all alternatives are explicitly enumerated 
within the declaration of the sealed class.
- [JEP 384](https://openjdk.java.net/jeps/384) introduces support for _record classes_, similar to the case classes of Scala.

