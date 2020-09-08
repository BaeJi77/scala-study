# REGULAR EXPRESSION PATTERNS (정규 표현식)
어떤 String value이든 `.r`이라는 키워드를 사용해서 정규표현식으로 만들어질 수 있다.

``` Scala
import scala.util.matching.Regex

val numberPattern: Regex = "[0-9]".r

numberPattern.findFirstMatchIn("awesomepassword") match {
  case Some(_) => println("Password OK")
  case None => println("Password must contain a number")
}
```
the `numberPattern` is a `Regex` (regular expression). password에 숫자가 있는 것을 확인하기 위해서 사용됨.

``` Scala
import scala.util.matching.Regex

val keyValPattern: Regex = "([0-9a-zA-Z- ]+): ([0-9a-zA-Z-#()/. ]+)".r

val input: String =
  """background-color: #A03300;
    |background-image: url(img/header100.png);
    |background-position: top center;
    |background-repeat: repeat-x;
    |background-size: 2160px 108px;
    |margin: 0;
    |height: 108px;
    |width: 100%;""".stripMargin

for (patternMatch <- keyValPattern.findAllMatchIn(input))
  println(s"key: ${patternMatch.group(1)} value: ${patternMatch.group(2)}")

key: background-color value: #A03300
key: background-image value: url(img/header100.png)
key: background-position value: top center
key: background-repeat value: repeat-x
key: background-size value: 2160px 108px
key: margin value: 0
key: height value: 108px
key: width value: 100
```
위에 있는 예는 해당 결과에 대해서 그룹이 나눠서 사용할 수 있다.
`:`을 기준으로 앞에는 group(1), 뒤에는 group(2)로 매칭해서 사용가능하다.

# EXTRACTOR OBJECTS
`extractor object`은 `unapply` method와 같이 있는 `object`이다. `apply` method는 생산자와 같은데 이것은 Object를 만드는데 사용된다.

`unapply`는 `pattern maching`이나 `partial functions`에서 자주 사용된다.

``` Scala
import scala.util.Random

object CustomerID {

  def apply(name: String) = s"$name--${Random.nextLong}"

  def unapply(customerID: String): Option[String] = {
    val stringArray: Array[String] = customerID.split("--")
    if (stringArray.tail.nonEmpty) Some(stringArray.head) else None
  }
}

val customer1ID = CustomerID("Sukyoung")  // Sukyoung--23098234908
customer1ID match {
  case CustomerID(name) => println(name)  // prints Sukyoung
  case _ => println("Could not extract a CustomerID")
}
```
`CustomerID`을 단순히 호출하는 것만으로 해당 객체를 만들었다. 그리고 `unapply`를 통해서 이상하게 만들어진 이름을 역으로 만들어 냈다.

`CustomerID(name) => println(name)`를 통해서 우리는 `unapply` 메소드를 호출하고 있다. `CustomerID.unapply(customer1ID)`


``` Scala
val customer2ID = CustomerID("Nico")
val CustomerID(name) = customer2ID
println(name)  // prints Nico

==

val name = CustomerID.unapply(customer2ID).get
```

``` Scala
val CustomerID(name2) = "--asdfasdfasdf"
```
이 부분은 실행이 되는 거니?ㅋㅋㅋㅋㅋㅋ

---

# FOR COMPREHENSIONS
Comprehensions have the form `for (enumerators) yield e`, where `enumerators` refers to a semicolon-separated list of enumerators.

이해를 돋기 위한 열거되어진 것들을 사용하는 경우가 존재함. 그리고 그 열자는 보통 필터에 사용되거나 새로운 변수를 만드는데 사용됨.

``` Scala
case class User(name: String, age: Int)

val userBase = List(
  User("Travis", 28),
  User("Kelly", 33),
  User("Jennifer", 44),
  User("Dennis", 23))

val twentySomethings =
  for (user <- userBase if user.age >=20 && user.age < 30)
  yield user.name  // i.e. add this to a list

twentySomethings.foreach(name => println(name))  // prints Travis Dennis
```
여기서 중요한 것은 `for`과 `if`를 통해서 데이터가 필터가 되었고 그것에 필터된 데이터만 `twentySomethings`의 `List[String]`이 되어졌다.


``` Scala
def foo(n: Int, v: Int) =
   for (i <- 0 until n;
        j <- 0 until n if i + j == v)
   yield (i, j)

foo(10, 10) foreach {
  case (i, j) =>
    println(s"($i, $j) ")  // prints (1, 9) (2, 8) (3, 7) (4, 6) (5, 5) (6, 4) (7, 3) (8, 2) (9, 1)
}
```
이 같은 경우는 pair number를 리턴하게 된다. 여기서 중요한 것은 `if i + j == v` 라는 것이고 이것을 통해서 i에 매칭되는 j가 하나씩 나오게 되었다. 만약 `if`가 없었다고 한다면 밑에 처럼 나왔을 것이다.

``` Scala
(0, 0) (0, 1) (0, 2) (0, 3) (0, 4) (0, 5) (0, 6) (0, 7) (0, 8) (0, 9) (1, 0) ...
```

`COMPREHENSIONS`는 List에 국한되지 않고 `map`, `flatMap`, `withFilter`와 같은 데이터 타입에도 사용될 수 있다. 그리고 `yield`를 사용하지 않은 경우 `Unit`를 리턴하기 때문에 밑에 예제처럼 바로 사용할 수 있다.
``` Scala
def foo(n: Int, v: Int) =
   for (i <- 0 until n;
        j <- 0 until n if i + j == v)
   println(s"($i, $j)")

foo(10, 10)
```

---

# GENERIC CLASSES
타입을 파라미터로 받아서 사용하는 class. 특히 collection과 관련된 class를 정의하고 사용하는 경우 많이 볼 수 있다.

### Defining a generic class

``` Scala
class Stack[A] {
  private var elements: List[A] = Nil
  def push(x: A) { elements = x :: elements }
  def peek: A = elements.head
  def pop(): A = {
    val currentTop = peek
    elements = elements.tail
    currentTop
  }
}
```
여기서는 `Stack`를 구현하는데 `Stack[A]`라고 정의하면서 `A`라는 친구를 미리 정의하지 않고 정의합니다.

### Usage
``` Scala
val stack = new Stack[Int]
stack.push(1)
stack.push(2)
println(stack.pop)  // prints 2
println(stack.pop)  // prints 1

class Fruit
class Apple extends Fruit
class Banana extends Fruit

val stack = new Stack[Fruit]
val apple = new Apple
val banana = new Banana

stack.push(apple)
stack.push(banana)
```
간단하게 `Int`를 받아서 사용하는 `Stack`를 만들 수도 있습니다. 하지만 밑에 처럼 하나를 상속받을 클래스들을 묶어서 사용할 수도 있습니다.
