# 스칼라는 무엇이냐?
#### 멀티 파라다임 랭귀지 (OOP + functinal)

## OOP
Scala is a pure object-oriented language in the sense that every value is an object. Types and behaviors of objects are described by classes and traits. Classes can be extended by subclassing, and by using a flexible mixin-based composition mechanism as a clean replacement for multiple inheritance.

## Functional
Scala is also a functional language in the sense that every function is a value. Scala provides a lightweight syntax for defining anonymous functions, it supports higher-order functions, it allows functions to be nested, and it supports currying. Scala’s case classes and its built-in support for pattern matching provide the functionality of algebraic types, which are used in many functional languages. Singleton objects provide a convenient way to group functions that aren’t members of a class.

Furthermore, Scala’s notion of pattern matching naturally extends to the processing of XML data with the help of right-ignoring sequence patterns, by way of general extension via extractor objects. In this context, for comprehensions are useful for formulating queries. These features make Scala ideal for developing applications like web services.

모든 함수가 값이 될 수 있음. 케이스 클래스와 패턴 일치에 대한 지원이 많으 있음. 싱글톤 객체는 함수를 그룹화 하는 편리한 기능을 제공

## 정적 타입
Scala’s expressive type system enforces, at compile-time, that abstractions are used in a safe and coherent manner. In particular, the type system supports:

강제로 타입에 대하여 정적으로 하도록 함. 안정적으로 사용하기 위해 컴파일 타임에 추상화함.

Type inference means the user is not required to annotate code with redundant type information. In combination, these features provide a powerful basis for the safe reuse of programming abstractions and for the type-safe extension of software.

타입 추론을 통해서 반복적으로 타입을 작성하지 않아도 되며 그것을 통해 프로그래밍 추사황를 안전하게 하며 확장성을 확보할 수 있다.

## 확장성
In practice, the development of domain-specific applications often requires domain-specific language extensions. Scala provides a unique combination of language mechanisms that make it straightforward to add new language constructs in the form of libraries.

In many cases, this can be done without using meta-programming facilities such as macros. For example:

Implicit classes allow adding extension methods to existing types.
String interpolation is user-extensible with custom interpolators.

## 상호작용
Scala is designed to interoperate well with the popular Java Runtime Environment (JRE). In particular, the interaction with the mainstream object-oriented Java programming language is as seamless as possible. Newer Java features like SAMs, lambdas, annotations, and generics have direct analogues in Scala.

Those Scala features without Java analogues, such as default and named parameters, compile as closely to Java as reasonably possible. Scala has the same compilation model (separate compilation, dynamic class loading) as Java and allows access to thousands of existing high-quality libraries.

자바 런타임 환경에 대하여 동작하도록 디자인됨. 그래서 컴파일 하는 과정에서 자바와 비슷하게 동작하도록 컴파일 됨.

# Basic

## Value
Named results, such as x here, are called values. Referencing a value does not re-compute it.
The type of a value can be omitted and inferred, or it can be explicitly stated:

`val x: Int = 1 + 1`

Values cannot be re-assigned:

`x = 3 // This does not compile.` 

명시적으로 변수에 대한 타입을 지정했을 때 다른 친구를 넣으면 에러가 뜸.

### 중요
명시적으로 하지 않아도 되며, 똑같은 변수를 다시 어사인하지 못함.

## Variables

Variables are like values, except you can re-assign them. You can define a variable with the var keyword.

## Functions

``` Scala
val add = (x: Int, y: Int) => x + y
println(add(1, 2)) // 3
```

## Methods
Methods look and behave very similar to functions, but there are a few key differences between them.

Methods are defined with the def keyword. def is followed by a name, parameter list(s), a return type, and a body:
``` Scala
def add(x: Int, y: Int): Int = x + y
println(add(1, 2)) // 3

def addThenMultiply(x: Int, y: Int)(multiplier: Int): Int = (x + y) * multiplier
println(addThenMultiply(1, 2)(3)) // 9

def getSquareString(input: Double): String = {
  val square = input * input
  square.toString
}
println(getSquareString(2.5)) // 6.25
```

The last expression in the body is the method’s return value. (Scala does have a return keyword, but it is rarely used.)

## Classes
``` Scala
class Greeter(prefix: String, suffix: String) {
  def greet(name: String): Unit =
    println(prefix + name + suffix)
}

val greeter = new Greeter("Hello, ", "!")
greeter.greet("Scala developer") // Hello, Scala developer!
```
The return type of the method greet is Unit, which signifies that there is nothing meaningful to return. It is used similarly to void in Java and C. (A difference is that, because every Scala expression must have some value, there is actually a singleton value of type Unit, written (). It carries no information.)

## Case Classes
By default, instances of case classes are immutable, and they are compared by value (unlike classes, whose instances are compared by reference). This makes them additionally useful for pattern matching.

``` Scala
val point = Point(1, 2)
val anotherPoint = Point(1, 2)
val yetAnotherPoint = Point(2, 2)

if (point == anotherPoint) {
  println(point + " and " + anotherPoint + " are the same.")
} else {
  println(point + " and " + anotherPoint + " are different.")
} // Point(1,2) and Point(1,2) are the same.

if (point == yetAnotherPoint) {
  println(point + " and " + yetAnotherPoint + " are the same.")
} else {
  println(point + " and " + yetAnotherPoint + " are different.")
} // Point(1,2) and Point(2,2) are different.
```

## Object
Objects are single instances of their own definitions. You can think of them as singletons of their own classes.

``` Scala
object IdFactory {
  private var counter = 0
  def create(): Int = {
    counter += 1
    counter
  }
}

val newId: Int = IdFactory.create()
println(newId) // 1
val newerId: Int = IdFactory.create()
println(newerId) // 2
```

## Traits
Traits are abstract data types containing certain fields and methods. In Scala inheritance, a class can only extend one other class, but it can extend multiple traits.

``` Scala
trait Greeter {
  def greet(name: String): Unit
}

trait Greeter {
  def greet(name: String): Unit =
    println("Hello, " + name + "!")
}
```

## Main Method
The main method is the entry point of a Scala program. The Java Virtual Machine requires a main method, named main, that takes one argument: an array of strings.


``` Scala
object Main {
  def main(args: Array[String]): Unit =
    println("Hello, Scala developer!")
}
```

# Unified Types
In Scala, all values have a type, including numerical values and functions. The diagram below illustrates a subset of the type hierarchy.

## Scala Type Hierarchy
`Any` is the supertype of all types, also called the top type. It defines certain universal methods such as `equals`, `hashCode`, and `toString`. `Any` has two direct subclasses: `AnyVal` and `AnyRef`.

AnyVal represents value types. There are nine predefined value types and they are non-nullable: Double, Float, Long, Int, Short, Byte, Char, Unit, and Boolean. Unit is a value type which carries no meaningful information.

AnyRef represents reference types. All non-value types are defined as reference types. Every user-defined type in Scala is a subtype of AnyRef. If Scala is used in the context of a Java runtime environment, AnyRef corresponds to java.lang.Object.

``` Scala
val list: List[Any] = List(
  "a string",
  732,  // an integer
  'c',  // a character
  true, // a boolean value
  () => "an anonymous function returning a string"
)

list.foreach(element => println(element))

// result
a string
732
c
true
<function>
```

## Type Casting
![casting](https://docs.scala-lang.org/resources/images/tour/type-casting-diagram.svg)

``` Scala
val x: Long = 987654321
val y: Float = x  // 9.8765434E8 (note that some precision is lost in this case)

val face: Char = '☺'
val number: Int = face  // 9786

val x: Long = 987654321
val y: Float = x  // 9.8765434E8
val z: Long = y  // Does not conform
```

## Nothing and Null
`Nothing` is a subtype of all types, also called the bottom type. There is no value that has type `Nothing`. A common use is to signal non-termination such as a thrown exception, program exit, or an infinite loop

`Null` is a subtype of all reference types (i.e. any subtype of AnyRef). It has a single value identified by the keyword literal `null`


# Classes
Classes in Scala are blueprints for creating objects. They can contain methods, values, variables, types, objects, traits, and classes which are collectively called members. Types, objects, and traits will be covered later in the tour.

## Defining a class
``` Scala
class Point(var x: Int, var y: Int) {

  def move(dx: Int, dy: Int): Unit = {
    x = x + dx
    y = y + dy
  }

  override def toString: String =
    s"($x, $y)"
}

val point1 = new Point(2, 3)
println(point1.x)  // 2
println(point1)  // prints (2, 3)
```

`toString`, on the other hand, does not take any arguments but returns a String value. Since `toString` overrides toString from `AnyRef`, it is tagged with the `override` keyword.

## Constructors
Constructors can have optional parameters by providing a default value like so:

``` Scala
class Point(var x: Int = 0, var y: Int = 0)

val origin = new Point  // x and y are both set to 0
val point1 = new Point(1)
println(point1.x)  // prints 1

class Point(var x: Int = 0, var y: Int = 0)
val point2 = new Point(y = 2)
println(point2.y)  // prints 2
```

In this version of the `Point` class, `x` and `y` have the default value `0` so no arguments are required.

## Private Members and Getter/Setter Syntax
``` Scala
class Point {
  private var _x = 0
  private var _y = 0
  private val bound = 100

  def x = _x
  def x_= (newValue: Int): Unit = {
    if (newValue < bound) _x = newValue else printWarning
  }

  def y = _y
  def y_= (newValue: Int): Unit = {
    if (newValue < bound) _y = newValue else printWarning
  }

  private def printWarning = println("WARNING: Out of bounds")
}

val point1 = new Point
point1.x = 99
point1.y = 101 // prints the warning
```
여기서 봤을 때 `def x`는 getter, `def x_=` 로 동작하게 됨.


``` Scala
// val로 변수를 선언하고 만들었기 때문에 값을 초기화한 이후로 데이터를 다시 어사인할 수 없음.
class Point(val x: Int, val y: Int)
val point = new Point(1, 2)
point.x = 3  // <-- does not compile

// var, val 둘 다 선언하지 않은 경우에 대해서는 private 변수로 선언됨. + val로 선언되는 것 같음.
class Point(x: Int, y: Int)
val point = new Point(1, 2)
point.x  // <-- does not compile
```

# Defalut Parameter Values
메소드를 호출하는데 있어서 defalut value를 지정할 수 있다. 파라미터를 넘기는데 있어서 값을 지정하지 않덜다도 자동으로 값이 들어간다.

``` Scala
def log(message: String, level: String = "INFO") = println(s"$level: $message")

log("System starting")  // prints INFO: System starting
log("User not found", "WARNING")  // prints WARNING: User not found
```

하지만 자바 코드에서 실행하는 경우 컴파일이 되지 않는댜.

``` Scala
// Point.scala
class Point(val x: Double = 0, val y: Double = 0)

// Main.java
public class Main {
    public static void main(String[] args) {
        Point point = new Point(1);  // does not compile
    }
}
```

# NAMED ARGUMENTS
``` Scala
def printName(first: String, last: String): Unit = {
  println(first + " " + last)
}

printName("John", "Smith")  // Prints "John Smith"
printName(first = "John", last = "Smith")  // Prints "John Smith"
printName(last = "Smith", first = "John")  // Prints "John Smith"

printName(last = "Smith", "john") // error: positional after named argument

```
파라미터 value에 대해서 넣을 수 있다. 하지만 파라미터에 대해서 명명하여 넣는 경우와 그렇지 않는 경우에 대해서는 반드시 이름이 없는 경우르 먼저 작성해야 한다.
그리고 자바에서는 동작하지 않는다.

# Traits
자바의 인터페이스와 비슷한 역할을 합니다. 필드와 함수를 공유함. 인스턴스화 할 수 없고 매개 변수가 없습니다.

## Defining and Using a trait

``` Scala
trait Iterator[A] {
  def hasNext: Boolean
  def next(): A
}

class IntIterator(to: Int) extends Iterator[Int] {
  private var current = 0
  override def hasNext: Boolean = current < to
  override def next(): Int = {
    if (hasNext) {
      val t = current
      current += 1
      t
    } else 0
  }
}


val iterator = new IntIterator(10)
iterator.next()  // returns 0
iterator.next()  // returns 1
```
사용하기 위해서 `extends` 키워드와 함수에 대하여 `override` 함수를 사용함.
타입에 대해서 `Iterator[Int]` 구현하면서 `Int`에 대해서 사용하게 됨.

## Subtyping

``` Scala
import scala.collection.mutable.ArrayBuffer

trait Pet {
  val name: String
}

class Cat(val name: String) extends Pet
class Dog(val name: String) extends Pet

val dog = new Dog("Harry")
val cat = new Cat("Sally")

val animals = ArrayBuffer.empty[Pet]
animals.append(dog)
animals.append(cat)
animals.foreach(pet => println(pet.name))  // Prints Harry Sally
```
뭔가 신기하게도 클래스를 상속받아서 사용하는 경우도 아닌데 인터페이스를 상속받아서 추상화 개념으로 같이 취급할 수 있는건가? 좋구만ㅎㅎ

# Tuples
고정되어진 수의 엘레멘트를 가진 밸류. 모든 타입은 명시적이고 value는 mutable하다. tuples는 여러 값을 리턴한다.
``` Scala
val ingredient = ("Sugar" , 25)

println(ingredient._1) // Sugar
println(ingredient._2) // 25

val (name, quantity) = ingredient
println(name) // Sugar
println(quantity) // 25

val planets =
  List(("Mercury", 57.9), ("Venus", 108.2), ("Earth", 149.6),
       ("Mars", 227.9), ("Jupiter", 778.3))
planets.foreach{
  case ("Earth", distance) =>
    println(s"Our planet is $distance million kilometers from the sun")
  case _ =>
}

val numPairs = List((2, 5), (3, -7), (20, 56))
for ((a, b) <- numPairs) {
  println(a * b)
}
```

# CLASS COMPOSITION WITH MIXINS

``` Scala
abstract class AbsIterator {
  type T
  def hasNext: Boolean
  def next(): T
}

class StringIterator(s: String) extends AbsIterator {
  type T = Char
  private var i = 0
  def hasNext = i < s.length
  def next() = {
    val ch = s charAt i
    i += 1
    ch
  }
}

trait RichIterator extends AbsIterator {
  def foreach(f: T => Unit): Unit = while (hasNext) f(next())
}

class RichStringIter extends StringIterator("Scala") with RichIterator
val richStringIter = new RichStringIter
richStringIter.foreach(println)
```
trait와 class에 대해서 서로 섞이고 그것에 대해서 상속받고 진행하면서도 서로 연관적으로 동작하도록 만들 수 있다.


# HIGHER-ORDER FUNCTIONS
높은 순서 함수는 다른 함수에 대한 결과를 활용하여 함수를 만들 수 있다. 왜냐하면, 스칼라 함수는 first-class values이기 때문이다.

대표적으로 `map`이 존재하다.

``` Scala
val salaries = Seq(20000, 70000, 40000)
val doubleSalary = (x: Int) => x * 2
val newSalaries = salaries.map(doubleSalary) // List(40000, 140000, 80000)

// 변수에 대한 타입이 존재하지 않아도 자연스럽게 Currying을 통해서 통작하도록 함.
val salaries = Seq(20000, 70000, 40000)
val newSalaries = salaries.map(x => x * 2) // List(40000, 140000, 80000)

// 컴파일러가 미리 파라미터를 아는 경우에 대해서는 밑에 같이도 사용할 수 있음
val salaries = Seq(20000, 70000, 40000)
val newSalaries = salaries.map(_ * 2)
```

## Functions that accept functions

``` Scala
object SalaryRaiser {

  def smallPromotion(salaries: List[Double]): List[Double] =
    salaries.map(salary => salary * 1.1)

  def greatPromotion(salaries: List[Double]): List[Double] =
    salaries.map(salary => salary * math.log(salary))

  def hugePromotion(salaries: List[Double]): List[Double] =
    salaries.map(salary => salary * salary)
}

object SalaryRaiser {

  private def promotion(salaries: List[Double], promotionFunction: Double => Double): List[Double] =
    salaries.map(promotionFunction)

  def smallPromotion(salaries: List[Double]): List[Double] =
    promotion(salaries, salary => salary * 1.1)

  def greatPromotion(salaries: List[Double]): List[Double] =
    promotion(salaries, salary => salary * math.log(salary))

  def hugePromotion(salaries: List[Double]): List[Double] =
    promotion(salaries, salary => salary * salary)
}
```
정확히 어떤 부분이 나아진 것일까요? 공통적으로 움직이는 로직을 함수로 표현해서 동작하도록 한 부분에 대해서는 알겠음.

하지만 이 부분이 정확히 어떤 부분에서 좋은 것이지?

## Functions that return functions

``` Scala
def urlBuilder(ssl: Boolean, domainName: String): (String, String) => String = {
  val schema = if (ssl) "https://" else "http://"
  (endpoint: String, query: String) => s"$schema$domainName/$endpoint?$query"
}

val domainName = "www.example.com"
def getURL = urlBuilder(ssl=true, domainName)
val endpoint = "users"
val query = "id=1"
val url = getURL(endpoint, query) // "https://www.example.com/users?id=1": String
```

`urlBuiler`는 `endpoint`와 `query` 두 개의 스트링을 받은 함수를 리턴함. `(String, String) => String` 

그것에 대한 새로운 함수를 리턴받고 그 함수를 가지고 새로운 형식의 함수를 만들 수 있음.


# Nested methods

``` Scala
 def factorial(x: Int): Int = {
    def fact(x: Int, accumulator: Int): Int = {
      if (x <= 1) accumulator
      else fact(x - 1, x * accumulator)
    }  
    fact(x, 1)
 }

 println("Factorial of 2: " + factorial(2)) // Factorial of 2: 2
 println("Factorial of 3: " + factorial(3)) // Factorial of 3: 6
```
