Intent HQ Scala Style Guidelines
=================

This repository contains style guidelines for writing Scala code at Intent HQ. Here are our goals for style
guides in this repository:

1. Our style guidelines should be clear.
2. We all should agree on our style guidelines.
3. We should change this guideline as we learn and as Scala evolves.
4. Keep the [scalastyle-config.xml](https://github.com/intenthq/scala-style-guide/blob/master/scalastyle-config.xml) in our projects in line with this guide as much as possible.

# Whitespace

* Use two spaces for indentation.
* Add a newline to the end of every file.

# Line Length

* Use a maximum line length of 120 characters.

# Names

## Functions, Classes and Variables

Use camel case for all function, class and local variable names. Classes and class/object level constants start with an upper case letter, and values and
functions start with a lower case. Here are some examples:

```scala
class MyClass {
  // Note that val constants use upper camel-case.
  // This is to avoid mistakes when pattern matching on values of constants.
  val ThisIsAConstant = ???

  def thisIsAFunction: Int = {
    val thisIsALocalVariable = ???
    ???
  }
}
```
### Acronyms

Use camel case for acroynms, for example, `HttpUtil`. For consistency, this includes class names like `Ttl` and `Json` as well as common terms such as `Db` and `Io`. Follow normal camel-casing rules.

```scala
val httpIoJsonToXmlParser = new HttpIoJsonToXmlParser()
```

## Package Objects

Package names should be all lowercase with no underscores.

File names for package objects must match the most specific name in the package.
For example, for the `com.paypal.mypackage` package object, the file
should be named `mypackage.scala` and the file should be structured like this:


```scala
package com.paypal

package object mypackage {
  //...
}
```

# Classes

## Public Methods

Limit the number of public methods in your class as much as possible.

# Throwables

Please consider not using them in the first place, and use type-safe `Either`/`Either3` instead. If you have to use a `Throwable`, then:

1. Prefer non-case classes when defining exceptions and errors unless you plan on pattern matching on the exception.
2. If providing a cause is desirable and not mandatory then define it as an option. Note, the use of ```.orNull```.

```scala
class CrazyException(msg: String, cause: Option[Throwable] = None) extends Exception(msg, cause.orNull)
class SuperCrazyError(msg: String, cause: Option[Throwable] = None) extends Error(msg, cause.orNull)
```

# Imports

## Ordering

IntelliJ's code style configuration allows for automatic grouping of imports by namespace.
We use the following ordering, which you should add to your IntelliJ configuration
(found in Preferences > Code Style > Scala):

```
java
___blank line___
scala
___blank line___
akka
spray
all other imports
___blank line___
com.globaldawn
com.intenthq
```

You should also regularly run IntelliJ's Optimize Imports (Edit > Optimize Imports) against
your code before merging with `master`, to maintain import cleanliness.

## Location

Most `import`s in a file should go at the top, right below the package name. The
only time you should break that rule is if you import some or all names from
an `object` from inside a `class`. For example:

```scala
package mypackage

import a.b.c.d
import d.c.b.a

class MyClass {
  import Utils._
  import MyClassCompanion.A
}

object MyClassCompanion {
  case class A()
}

object Utils {
  case class Util1()
  case class Util2()
}
```

## Predef

Never `import` anything inside of Scala's
['Predef'](http://www.scala-lang.org/api/2.10.3/index.html#scala.Predef$)
object. It is automatically imported for you, so it's redundant to manually
`import`. IntelliJ will often try to import it, so be aware.

# Values

Always use `val`. If you find a situation where you think you have to use `var`, consider asking others for advice on another approach. If you still think you need `var`, consider whether you are solving the correct problem with the correct language.

## Modifiers

Optional variable modifiers should be declared in the following order: `override access (private|protected) final implicit lazy`.

For example,

```scala
private implicit lazy val someSetting = ...
```

# Functions

Rules to follow for all functions:

* Always put a space after `:` characters in function signatures.
* Always put a space after `,` in function signatures
* Always add explicit parameter and return types - it will help catch type problems sooner!.

```scala
def add(a: Int, b: Int): Int = a + b
```

## Public Functions
All public functions and methods, including those inside `object`s must have Scaladoc, including a function overview, information on parameters, and information on the return value. See the [Scaladoc](#scaladoc-comments-and-annotations) section for more details.

Here is a complete example of a function inside of an `object`.

```scala
object MyObject {
  /**
   * returns the static integer 123
   * @return the number 123
   */
  def myFunction: Int = {
    123
  }
}
```

### Parameter Lists

If a function has a parameter list with fewer than 70 characters, put all parameters on the same line:

```scala
def add(a: Int, b: Int): Int = {
  ...
}
```

If a function has long or complex parameter lists, follow these rules:

1. Put the first parameter on the same line as the function name.
2. Put the rest of the parameters each on a new line, aligned with the first parameter.
3. If the function has multiple parameter lists, align the opening parenthesis with the previous one and align
parameters the same as #2.

Example:

```scala
def lotsOfParams(aReallyLongParameterNameOne: Int,
                 aReallyLongParameterNameTwo: Int,
                 aReallyLongParameterNameThree: Int,
                 aReallyLongParameterNameFour: Int)
                (implicit adder: Adder,
                 reader: Reader): Int = {
  ...
}
```

For function names over 30 characters, try to shorten the name. If you can't,
start the parameter list on the next line and indent everything 2 spaces:

```scala
def aVeryLongMethodThatShouldHaveAShorterNameIfPossible(
  aParam: Int,
  anotherParam: Int,
  aThirdParam: Int)
 (implicit iParam: Foo,
  bar: Bar): String = {
  ...
}
```

In all cases, the function's return type still has to be written directly
following the last closing parenthesis.

#### Calling Functions

When calling functions with numerous arguments, place the first parameter on the
same line as the function and align the remaining parameters with the first:

```scala
fooBar(someVeryLongFieldName,
       andAnotherVeryLongFieldName,
       "this is a string",
       3.1415)
```

For functions with very long names, start the parameter list on the second line
and indent by 2 spaces:

```scala
aMuchLongerMethodNameThatShouldProbablyBeRefactored(
  aParam,
  anotherParam,
  aThirdParam)
```

It's your choice whether to place closing parenthesis directly following the
last parameter or on a new line (in "dangling" style).

You can do this:

```scala
aLongMethodNameThatReturnsAFuture(
  aParam,
  anotherParam,
  aThirdParam
).map { res =>
  ...
}
```

Or this:

```scala
aLongMethodNameThatReturnsAFuture(
  aParam,
  anotherParam,
  aThirdParam).map { res =>
  ...
}
```

### Returning Values

The last expression of the function should always be the intended return value.

Use `()` if you want to discard a non-`Unit` return value.

```scala
def post(url: String): StatusCode = ???

// Bad
def returnNothing(url: String): Unit = {
  post(url)
}

// Good
def returnNothing(url: String): Unit = {
  post(url)
  ()
}
```

Do not use use `return` as it
[behaves differently](https://tpolecat.github.io/2014/05/09/return.html).

The last statement should always match the return type

```scala
def returnFive(): Int = {

}
```

## Anonymous functions

Anonymous functions start on the same line as preceding code. Declarations
start with ` { ` (note the space before and after the `{`). Arguments are then
listed on the same line. A few more notes:

* Do not use braces after the argument list, just start the function body on the next line.
* Argument types are not necessary. You should use them if it makes the code clearer though.

Here's a complete example example:

```scala
Option(123).map { number =>
  println(s"the number plus one is: ${number + 1}")
}
```

### Exceptions to the Rule

Use parentheses and an underscore for anonymous functions that are:

* single binary operations
* a single method invocation on the input
* two or fewer unary methods on the input

Examples:

```scala
val list = List("list", "of", "strings")
list.filter(_.length > 2)
list.filter(_.contains("i"))
```

## Passing named functions

If the function takes a single argument, then arguments and underscores should be omitted.

For example:
```scala
Option(123).map(println)
```

is preferred over

```scala
Option(123).map(println(_))
```

## Calling functions

Prefer dot notation except for these specific scenarios:
1. Specs2 matchers
2. Akka ```pipeTo```, ```!```, ```?```

For example:
```scala
Option(someInt).map(println)
```

is preferred over

```scala
Option(someInt) map println
```

##### Rule Exceptions

Java <=> Scala interoperation on primitives: Scala and Java booleans, for example, are not directly compatible, and
require an implicit conversion between one another. Normally this is handled automagically by the Scala compiler, but
the following code would reveal a compiler error:

```scala
Option(true).foreach(someMethodThatTakesAJavaBoolean)
```

A named parameter is *not* necessary to achieve this. An underscore should be used to resolve the implicit conversion.
To avoid confusion, please also add a note that a Scala <=> Java conversion is taking place:

```scala
Option(true).foreach(someMethodThatTakesAJavaBoolean(_))  // lol java
```

# Logic Flows

In general, logic that handles a choice between two or more outcomes should prefer to use `match`.

## Match Statements

When you `match` on any type, follow these rules:

1. Pattern matching should be exhaustive and explicitly handle the failure/default case rather than relying on a runtime `MatchError`. (This is specific to match blocks and not case statements in partial functions.) Case classes used in pattern matching should extend a common `sealed trait` so that compiler warnings will be generated for inexhaustive matching.
2. Indent all `case` statements at the same level, and put the `=>` one space to
  the right of the closing `)`
3. Short single line expressions should be on the same line as the `case`
4. Long single line and multi-line expressions should be on the line below the case, indented one level from the `case`.
5. Do not add extra newlines in between each case statement.
6. Filters on case statements should be on the same line if doing so will not make the line excessively long.

Here's a complete example:

```scala
Option(123) match {
  case Some(i: Int) if i > 0 =>
    val intermediate = doWorkOn(i + 1)
    doMoreWorkOn(intermediate)
  case _ => 123
}
```

## Option

Flows with `Option` values should be constructed using the `fold` function as follows.

```scala
def stuff(i: Int): Int = { ... }

Option(123).fold(0) { stuff(_) + 10 }
```

Simple, single-line patterns are also acceptable for `.fold`, such as:

```scala
def stuff(i: Int): Int = { ... }

Option(123).fold(0)(stuff)
```

Similarly, simple patterns are acceptable for `.map` with `.getOrElse`, such as:

```scala
def stuff(i: Int): Int = { ... }

Option(123).map(stuff).getOrElse(0)
```

Use what you feel is most readable.

## For Comprehension

For comprehensions should generally not be wrapped in parentheses in order to recover, flatMap, etc. Instead, separate the for comprehension into its own variable and perform additional operations on that.

```scala
def userAddress(userId: Long) = for {
  user <- fetchUser(userId)
  address <- fetchAddress(user)
} yield address

userAddress.recover {
  // recover from loadAddress exception
}.flatMap {
  // pull info from address, etc.
}
```

Also, do not use `for` when you only have a single item. Always return the result directly, or favour `map`:

```scala
// Bad
for {
  user <- fetchUser(userId)
} yield user

// Good
fetchUser(userId)

// Bad
for {
  id <- getLatestId
} yield Post(id)

// Good
getLatestId.map(Post)
```

# Tests

Write your tests using [Specs2](http://etorreborre.github.io/specs2/). Each
specification is a single `class` that extends `Specification` (use of `mutable.Specification` is allowed). Put each
specification into its own file. Some other basics:

* Don't use mocking. You shouldn't need it. Consider whether you are trying to test something correctly in the first place, and whether you could avoid mocking by simply using a `trait` to abstract an interface you can test.
* Prefer using traits over defining `val` items inside the `Specification`.
* Try to avoid usage of `isolated`/`sequential` if you can help it, it slows the tests down.

# Scaladoc, Comments, and Annotations

All classes, objects, traits, and methods should be documented. Generally, follow the documentation guidelines
provided by the Scala Documentation Style Guide on [ScalaDoc](http://docs.scala-lang.org/style/scaladoc.html).

Rules:

* Classes that are instantiated via methods in a companion object **must** include ScalaDoc documentation with a code example.
* Abstract classes *should* be documented with an example of their intended implementation.
* Implicit wrapper classes **must** include ScalaDoc documentation with a code example.
* Public, protected, and package-private methods **must** include ScalaDoc documentation.
* Private methods *should* be documented, however it is left to the discretion of the developer as to the level of documentation.
* All methods **must** include `@throws` annotations if they throw an exception in their normal operation.

Use your best judgment otherwise, and err toward more documentation rather than less.

# Dependencies and SBT

Try to avoid adding dependencies by considering whether an existing one already fulfills the needs you have. If you do add a dependency, make sure you add the version number in a strict way. This is because most Java/Scala libraries do not do semver correctly:

```scala
// Bad
"com.github.seratch" %% "awscala" % "0.5.+"

// Good
"com.github.seratch" %% "awscala" % "0.5.9"
```

Think carefully about adding new modules to the SBT build. These have a hidden cost (compile times become longer, and increasingly sequential). Only abstract thinks out into common modules when you actually need something more than a few times.

# Further Reading

Several of these recommendations are adapted from the Scala Documentation Style Guide,
including [Declarations](http://docs.scala-lang.org/style/declarations.html) and
[Indentation](http://docs.scala-lang.org/style/indentation.html). The guidelines presented
in this document should supersede all other style recommendations.
