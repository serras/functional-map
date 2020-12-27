---
layout: default
title: "Pattern matching"
permalink: /concepts/pattern-matching
---

# Pattern matching

Pattern matching is a type of conditional evaluation/execution expression. The idea of a pattern match is 
to take an entry _matched_ expression, evaluate it, inspect it, and based on its value select one among several _cases_.
Each _case_, consists of a _pattern_, that describes which possible values of the matched expression are covered by that case, 
and may also extract from within the matched expression some information (as variables).

Ideally, the _cases_ in a pattern-matching block should be **exhaustive**, so every value of the matched expression
is covered by one case, and **mutually exclusive**, so no value of the expression can match more than one case. 
However, automatically checking these conditions can be a very difficult problem. In practice, most 




## Pattern Matching in Languages

### Haskell

Patterns in Haskell can be _nested_, so that, if the top-level of a pattern is a data constructor, 
then each argument in that data constructor can also be a pattern. For instance, in:

``` Haskell
fili :: Maybe Int -> Int
fili xs = case xs of 
  Just 13 -> 14
  Just _  -> 9
  Nothing  -> 0
```
The pattern for the first case has at the top level `Just` (a data constructor for the `Maybe` type), 
and nested within it is the `9` integer literal, which is another pattern.


#### Function literals 

The [`LambdaCase`](https://ghc.gitlab.haskell.org/ghc/doc/users_guide/exts/lambda_case.html) GHC extension allows writing a function literal
(a lambda expression) just as a group of cases.


#### 


### Scala

Scala (as of the versions 2.x) provides a powerful and completely user-customisable form of pattern matching.


#### Unapply methods

In Scala, pattern-matching is implemented not as a language construct, but also as syntactic 

Every object, be it a static `object` declaration, or an instance of a `class`, can be used as a pattern
_if_ its signature includes an `unapply` method, which can be 

```Scala

```

Having the ability to craft `unapply` methods expands the powers of pattern-matching. 

##### Infix operators as patterns

If the return type of the `unapply` method is an `Option[(A, B)]`, which is to say an optional tuple of two elements, 
then it is 

For instance, `http4s` declares the following `->` object, to split from a request the method and the path: 

``` Scala
object -> {
  def unapply(req: Request): Some[(Method, Path)] =
    Some((req.method, req.pathInfo))
}
```

In this library, a service is just a function to match some requests, and we can use this object as an infix operator.
For instance, the following is a trivial Http service, to return `NotFound` to `POST` requests, and `NoContent` to anything else.
``` Scala
request match {
  case  POST -> _   => NotFound()
  case  _ => NoContent()
}
```
In the first case, we use the `->` as a pattern, to match the `POST` method and to ignore the `path`, and this pattern is 
written _between_ the method and the path, that is, infix.


##### Example: lowercasing strings

Suppose you want to match a string with another one in a way that is insensitive to capitalisation or whitespace. 
You could just do a transformation to de-capitalise and filter out whitespaces in the string, 
before you pass it on the pattern-matching.

In Scala, you can write that transformation directly into the `unapply` method, so that you can get a 

``` Scala
class LowerUnspaced(pattern: String) {
  
  def unapply(raw: String): Option[String] = {
    val str = raw.toLowerCase.filterNot(isWhitespace(_))
    if (str == pattern) Some(str) else None
  }
}

val bowRoad = new LowerUnspaced("Bow Road")

"Mornington Crescent" match {
  case bowRoad(_) => 
}
```

Rather than adding a call to the `.toLowerCase` method every time 

##### Example: http responses

[`http4s`](https://github.com/http4s/http4s/) is a Scala library for HTTP protocol, clients, and servers.
It includes a `Response` class to represent an HTTP response (as defined in standard), and a `Status` class to represent
the response status codes (200 OK, 404 NotFound, 204 NoContent, etc). When using Http clients and processing a response, 
one wants to branch off depending on what the status of a response is.

To this end, the `Status` class declares an `unapply` method to match any `Response` object whose status is, precisely, the one.

```Scala
// Note: this is code is very simplified, but for the purporses of this chapter suffices
 
class Status(code: Int){
  def unapply(resp: Response): Option[Response] =
    if (resp.status == this) Some(resp) else None
}
val Ok = new Status(200)
val NotFound = new Status(404)

// So that when processing a response we can say: 
response match {
  case Ok(_)   => "Yay"
  case NotFound(_) => "Nay"
}
```

##### Example: abstracting HTTP routes

Having patterns as `unapply` methods means that, in Scala, patterns are objects, which is to say, 
patterns are just _values_. We can abstract patterns, that is, pick up any complicated and convoluted
pattern (or set of patterns) with guards or conditions, put it into an `unapply` method for any object,
and then use that object in the patterns.

Using `http4s` again, suppose we have a REST application with many endpoints, which are very similar 
to each other: 

``` Scala
// again very simplified CRUD-like API

request match {
  case GET    -> "countries" :/ country / "regions" / region / "cities" / city => /***/
  case PUT    -> "countries" :/ country / "regions" / region / "cities" / city => /**/
  case DELETE -> "countries" :/ country / "regions" / region / "cities" / city => /**/
}

```
Instead of repeating the initial part of the path in each pattern, which can get very large, 
one can instead abstract that part as a single pattern: 

``` Scala
object CityPath {
  def unapply(path: Path): Option[(String, String, String)] = path match {
    case  "countries" :/ country / "regions" / region / "cities" / city => Some(country, region, city)
    case _ => None
  }
}

request match {
  case GET    -> CityPath(country, region, city) => /***/
  case PUT    -> CityPath(country, region, city) => /***/
  case DELETE -> CityPath(country, region, city) => /***/
}
```



In `http4s`, those

We can then 




#### Function literals

In Scala, pattern-matching is also used to declare function literals. When used in this fashion, the 
`match` keyword and the head expression are omitted, since they are understood to be just the parameter 
of the function. 

**Note actually** pattern-matching blocks are literal for _partial_ functions, which are a subclass of functions.

#### Variables

We can use any variable in scope as a pattern, using equality, by using the backtick notation: 

```Scala

val answer = 42

evalLoop match {
  case `answer` => "What?"
  case _  => "Ok"
}
```

### Kotlin

Kotlin uses [`when` expressions](https://kotlinlang.org/docs/reference/control-flow.html#when-expression) that can be used 
for matching either primitive values or [`Sealed Classes`](https://kotlinlang.org/docs/reference/sealed-classes.html).
When used with sealed classes 

### Other languages

Simpler languages such as `C`, `C++` and `Java` provide a feature.
Note that these three are imperative languages, and thus the `switch` construction is a control-flow _statement_, not an expression. 
Therefore, it is not possible in those languages to _assign_ to a variable the result of a `switch`, or to pass 
a `switch` as parameter for a function.


Future versions of Java is soon introducing its own variant of _sealed classes_ with a special 
