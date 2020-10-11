# VARIANCES (가변성)
제네릭 클래스에서 해당 클래스에 대해서 불변, 가변, 역가변같이 다양한 형태로 제네릭 클래스를 만들 수 있다.

``` Scala
class Foo[+A] // A covariant class
class Bar[-A] // A contravariant class
class Baz[A]  // An invariant class
```

## Covariance
T에 대해서 +T로 표현하는 방식이다. 만약 A라는 타입으로 제네릭 클래스를 선언했을 경우 A의 서브 타입인 B로 해당 데이터로 사용될 수 있다.

``` Scala
abstract class Animal {
  def name: String
}
case class Cat(name: String) extends Animal
case class Dog(name: String) extends Animal

def printAnimalNames(animals: List[Animal]): Unit =
  animals.foreach {
    animal => println(animal.name)
  }

val cats: List[Cat] = List(Cat("Whiskers"), Cat("Tom"))
val dogs: List[Dog] = List(Dog("Fido"), Dog("Rex"))

printAnimalNames(cats) // prints: Whiskers, Tom
printAnimalNames(dogs) // prints: Fido, Rex
```
위에 예제와 같이 Animal의 서브클래스인 Cat과 Dog은 `printAnimalNames` 메소드를 잘 실행시키는 모습을 볼 수 있다.

## Contravariance
T에 대해서 -T로 표현하는 방식이다. 만약 A라는 타입으로 제네릭 클래스를 선언했을 경우 A의 서브 타입인 B로 해당 데이터로 사용될 수 있다.

``` Scala
abstract class Printer[-A] {
  def print(value: A): Unit
}

class AnimalPrinter extends Printer[Animal] {
  def print(animal: Animal): Unit =
    println("The animal's name is: " + animal.name)
}

class CatPrinter extends Printer[Cat] {
  def print(cat: Cat): Unit =
    println("The cat's name is: " + cat.name)
}

def printMyCat(printer: Printer[Cat], cat: Cat): Unit =
  printer.print(cat)

val catPrinter: Printer[Cat] = new CatPrinter
val animalPrinter: Printer[Animal] = new AnimalPrinter

printMyCat(catPrinter, Cat("Boots")) // prints The cat's name is: Boots
printMyCat(animalPrinter, Cat("Boots")) // prints The animal's name is: Boots
```

## Invariance
제네릭 클래스는 이 가변성을 기본으로 한다. 다른 어떤 가변성을 허락하지 않는다. 

``` Scala
class Container[A](value: A) {
  private var _value: A = value
  def getValue: A = _value
  def setValue(value: A): Unit = {
    _value = value
  }
}

val catContainer: Container[Cat] = new Container(Cat("Felix"))
val animalContainer: Container[Animal] = catContainer
animalContainer.setValue(Dog("Spot"))
val cat: Cat = catContainer.getValue // Oops, we'd end up with a Dog assigned to a Cat
```
위와 같이 실행할 때 헤네릭이기에 가능할 것 같다고 생각할 수 있다. 하지만 `Invariance` 에서는 세이프하지 못하다. 여기서 `Container`가 Invariance가 아닌 covariant였다면 잘 동작했을 것이다.

# UPPER TYPE BOUNDS
`type parameters` and `abstract type members`는 type에 대해서 굉장히 불안정하게 만든다. 그래서 해당 타입에 대하여 **upper type bound** `T <: A`을 사용하게 되는데 이것은 T는 A에 대한 서브 타입이라는 것을 표시한다.
``` Scala
abstract class Animal {
 def name: String
}

abstract class Pet extends Animal {}

class Cat extends Pet {
  override def name: String = "Cat"
}

class Dog extends Pet {
  override def name: String = "Dog"
}

class Lion extends Animal {
  override def name: String = "Lion"
}

class PetContainer[P <: Pet](p: P) {
  def pet: P = p
}

val dogContainer = new PetContainer[Dog](new Dog)
val catContainer = new PetContainer[Cat](new Cat)

// this would not compile
val lionContainer = new PetContainer[Lion](new Lion)
```
위에 예시를 보게 되면 `Pet`의 서브 타입인 `Dog`과 `Cat`은 정상적으로 만들어지지만 `Lion`은 compile 되지 않는다.


# LOWER TYPE BOUNDS


# INNER CLASSES
스칼라에서도 클래스 멤버로 클래스를 선언할 수 있다. 자바와 같은 언어는 enclosing class으로 클래스를 선언하지만, 스칼라 같은 경우 outer object에 바운드하게 된다.
밑에 예시는 Graph와 관련한 자료구조를 구현하는 모습이다.

``` Scala
class Graph {
  class Node {
    var connectedNodes: List[Node] = Nil
    def connectTo(node: Node): Unit = {
      if (!connectedNodes.exists(node.equals)) {
        connectedNodes = node :: connectedNodes
      }
    }
  }
  var nodes: List[Node] = Nil
  def newNode: Node = {
    val res = new Node
    nodes = res :: nodes
    res
  }
}

val graph1: Graph = new Graph
val node1: graph1.Node = graph1.newNode
val node2: graph1.Node = graph1.newNode
val node3: graph1.Node = graph1.newNode
node1.connectTo(node2)
node3.connectTo(node1)
```
해당 코드는 아무 문제없이 정상적으로 잘 동작하게 된다. 하지만 다른 그래프 클래스에서 생성한 Node를 다른 그래프에 넣게 된다면 어떻게 될까요?

``` Scala
val graph1: Graph = new Graph
val node1: graph1.Node = graph1.newNode
val node2: graph1.Node = graph1.newNode
node1.connectTo(node2)      // legal
val graph2: Graph = new Graph
val node3: graph2.Node = graph2.newNode
node1.connectTo(node3)      // illegal!
```
이 부분은 잘 동작하지 않게 됩니다. 자바 클래스 같은 경우는 정상적으로 동작했겠지만 스칼르는 다릅니다. 왜냐하면 `graph1.Node`와 `graph2.Node`는 구별되어지기 때문입니다.
그래서 사용하고 싶다면 `Graph#Node`와 같이 사용해야 됩니다.

``` Scala
class Graph {
  class Node {
    var connectedNodes: List[Graph#Node] = Nil
    def connectTo(node: Graph#Node): Unit = {
      if (!connectedNodes.exists(node.equals)) {
        connectedNodes = node :: connectedNodes
      }
    }
  }
  var nodes: List[Node] = Nil
  def newNode: Node = {
    val res = new Node
    nodes = res :: nodes
    res
  }
}
```

# ABSTRACT TYPE MEMBERS
추상 타입은 traits나 추상 클래스와 같이 추상 타입 멤버를 가질 수 있다. 이거는 구현에 있어서 실제 타입에 대하여 구체적인 구현을 해야됨을 의미한다.


``` Scala
trait Buffer {
  type T
  val element: T
}

abstract class SeqBuffer extends Buffer {
  type U
  type T <: Seq[U]
  def length = element.length
}
```
해당 코드를 통해서 T는 Seq[U]라는 것을 통해서 구체적인 구현에 대한 가이드가 정해졌다. 추상 타입 멤버를 사용하는 경우에 이름없는 클래스를 생성하는데 사용된다.

``` Scala
abstract class IntSeqBuffer extends SeqBuffer {
  type U = Int
}


def newIntSeqBuf(elem1: Int, elem2: Int): IntSeqBuffer =
  new IntSeqBuffer {
       type T = List[U]
       val element = List(elem1, elem2)
     }
val buf = newIntSeqBuf(7, 8)
println("length = " + buf.length)
println("content = " + buf.element)
```
위에 코드는 U가 Int로 정해지면서 T type은 Int형 seq에 제한적으로 정의되어진다. 그리고 그것으로 인해서 List[U]를 통해서 구현하게 된다. 또한 밑에와 같은 경우처럼 클래스의 파라미터 타입으로 이용해서 가능하다.

``` Scala
abstract class Buffer[+T] {
  val element: T
}
abstract class SeqBuffer[U, +T <: Seq[U]] extends Buffer[T] {
  def length = element.length
}

def newIntSeqBuf(e1: Int, e2: Int): SeqBuffer[Int, Seq[Int]] =
  new SeqBuffer[Int, List[Int]] {
    val element = List(e1, e2)
  }

val buf = newIntSeqBuf(7, 8)
println("length = " + buf.length)
println("content = " + buf.element)
```

# COMPOUND TYPES
가끔씩 우리는 object의 여러 서브 타입을 표현하는 것이 필요한 경우가 있다. 스칼라에서는 **compound types**를 이용한다.

``` Scala
trait Cloneable extends java.lang.Cloneable {
  override def clone(): Cloneable = {
    super.clone().asInstanceOf[Cloneable]
  }
}
trait Resetable {
  def reset: Unit
}

def cloneAndReset(obj: ?): Cloneable = {
  val cloned = obj.clone()
  obj.reset
  cloned
}
```
해당 obj로 `Cloneable`이 오게되는 경우 `.clone()`은 되겠지만 `.reset`은 되지 않는다. `Resetable`이 오는 경우 `reset`은 되겠지만 `.clone()`은 안된다. 이럴 경우 스칼라는 방법이 있다.

``` Scala
def cloneAndReset(obj: Cloneable with Resetable): Cloneable = {
  //...
}
```
이렇게 선언하는 경우 두 가지 모두 구현체에 대해서 받아서 사용할 수 있다.
추가적으로 계속 사용하고 싶다면 `A with B with C ... { refinement }` 이런 형태도 계속 만들어나갈 수 있다.

# SELF-TYPE
trait에서 다른 trait에게 의존성을 가지게 되는 경우를 의미한다.
이 경우에 조금 특별한 식별자를 사용해야 하며 다른 trait과 굉장히 비슷해 보이지만 완전히 다르다.

``` Scala
trait User {
  def username: String
}

trait Tweeter {
  this: User =>  // reassign this
  def tweet(tweetText: String) = println(s"$username: $tweetText")
}

class VerifiedTweeter(val username_ : String) extends Tweeter with User {  // We mixin User because Tweeter required it
	def username = s"real $username_"
}

val realBeyoncé = new VerifiedTweeter("Beyoncé")
realBeyoncé.tweet("Just spilled my glass of lemonade")  // prints "real Beyoncé: Just spilled my glass of lemonade"
```
위 코드를 보게 되면 `this: User =>` 를 활용해서 다른 trait에 의존적으로 바뀌게 된다.
`extends Tweeter with User` 그리고 이것은 User가 반드시 필요하기 때문에 같이 상속받는 것이다.

# IMPLICIT PARAMETERS
**implicit** 라는 키워드가 있는 파라미터를 가질 수 있다. 만약 스칼라가 해당 파라미터에 대해서 제대로 사용하지 못하는 경우 (구현이 제대로 되어있지 않는 것을 넘긴 경우) 스칼라는 해당 값과 맞는 타입을 찾고 자동으로 그것에 대한 것을 넘겨준다.

스칼라가 이러한 파라미터를 찾는 위치는 두가지 경우가 있다. 
- implicit 파라미터 블럭이 있는 메소드가 접두사 없이 직접 엑세스 할 수 있는 implicit 정의 및 implicit 파라미터를 찾는다.
- 그런 다음 implicit 후보 유형과 연결되 모든 개체에서 implicit으로 표시된 멤버를 찾는다.

``` Scala
abstract class Monoid[A] {
  def add(x: A, y: A): A
  def unit: A
}

object ImplicitTest {
  implicit val stringMonoid: Monoid[String] = new Monoid[String] {
    def add(x: String, y: String): String = x concat y
    def unit: String = ""
  }
  
  implicit val intMonoid: Monoid[Int] = new Monoid[Int] {
    def add(x: Int, y: Int): Int = x + y
    def unit: Int = 0
  }
  
  def sum[A](xs: List[A])(implicit m: Monoid[A]): A =
    if (xs.isEmpty) m.unit
    else m.add(xs.head, sum(xs.tail))
    
  def main(args: Array[String]): Unit = {
    println(sum(List(1, 2, 3)))       // uses intMonoid implicitly
    println(sum(List("a", "b", "c"))) // uses stringMonoid implicitly
  }
}
```
위에 코드를 보게 되면 `Main`에서는 `sum`이라는 메소드를 실행시키지만 실제 `Monoid[A]`로 구현되어지지 않은 단순 리스트만을 전달한다.

그렇게 되면서 스칼라는 자연스럽게 해당 `implicit` 파라미터와 가장 어울리는 `implicit` 메소드와 파라미터를 찾게 된다.

첫번째 같은 경우 `Int` 밸류 이기 때문에 `intMonoid`가 호출되었다.그리고 두번째는 `String` 이기에 `stringMonoid`가 사용되었다.

자동으로 `implicit`으로 선언된 `intMonoid`는 바로 접근이 가능하기에 `main`에서 바로 전달되어서 사용되어지고 `stringMonoid` 또한 똑같다.
```
Scala will first look for implicit definitions and implicit parameters that can be accessed directly (without a prefix) at the point the method with the implicit parameter block is called.
```

# IMPLICIT CONVERSIONS

모르겠으....

# POLYMORPHIC METHODS
스칼라에 있는 메소드는 타입에 의해서 파라메터화 될 수 있다. 이것은 제네릭 클라스와 비슷하게 `[]` 와 같은 형식으로 타입을 넣을 수 있다.

``` Scala
def listOfDuplicates[A](x: A, length: Int): List[A] = {
  if (length < 1)
    Nil
  else
    x :: listOfDuplicates(x, length - 1)
}
println(listOfDuplicates[Int](3, 4))  // List(3, 3, 3, 3)
println(listOfDuplicates("La", 8))  // List(La, La, La, La, La, La, La, La)
```
`listOfDuplicates` 메소드는 A라는 타입을 기준으로 해당 코드가 동작하도록 만들었다. 

`listOfDuplicates[Int](3, 4)` 코드는 Int 밸류라는 것을 명시적으로 사용했고 뒤에 있는 3이라는 숫자가 4번 들어간 List를 만들었다.

하지만 스칼라에서는 가끔씩 `listOfDuplicates("La", 8)`처럼 타입을 선언해주지 않더라도 자동으로 사용할 수 있게 해준다.

# TYPE INFERENCE
스칼라 컴파일러는 가끔 타입과 관련해서 자동으로 추론해주기에 명기적으로 작성하지 않아도 된다.

## Omitting the type
``` Scala
val businessName = "Montreux Jazz Café"

def squareOf(x: Int) = x * x
```
여기서 `businessName`이 string 이라는 것을 자동으로 대입해주며 `squareOf`의 리턴타입이 Int라는 것 또한 자동으로 추론해준다.

하지만 재귀함수 같은 경우 스칼라에서 정확하게 알 수 없다.
``def fac(n: Int) = if (n == 0) 1 else n * fac(n - 1)``

`polymorphic methods`, `generic classes`과 같은 경우는 일반적으로 타입에 대해서 추론을 해준다.

``` Scala
case class MyPair[A, B](x: A, y: B)
val p = MyPair(1, "scala") // type: MyPair[Int, String]

def id[T](x: T) = x
val q = id(1)              // type: Int
```

## Parameters

``` Scala
Seq(1, 3, 4).map(x => x * 2)  // List(2, 6, 8)
```
여기서 보는 map이라는 메소드는 `f: A => B` 형식을 가지고 있다. 그런데 여기서 `Seq`를 사용하고 있고 그러기에 A의 타입은 자연스럽게 B의 타입이 된다는 것을 알 수 있다.

## When not to rely on type inference
명시적으로 사용자에게 사용되는 API와 같은 경우는 코드 가독성을 위해서 명시적으로 타입을 작성하는 것을 추천한다.

그리고 가끔식 타입 추론은 너무 특별한 케이스에 대해서 추론한다.
``` Scala
var obj = null

obj = new AnyRef
```
이것은 컴파일되지 않는다. 왜냐하면 obj는 Null이었고 벌써 타입이 null으로 추론되었기 때문에 새로운 값이 어사인 되는 것이 불가능하다.

# OPERATORS
스칼라에서 모든 연산은 메소드이다. 어떤 메소드는 **infix operator**로 사용되어 진다. 밑에 예제는 infix operator로 인해서 읽기 쉬워진다.

``` Scala
10.+(1)

10 + 1
```

## Defining and using operators
우리는 원하는 연산자들을 직접 만들 수가 있다.

``` Scala
case class Vec(x: Double, y: Double) {
  def +(that: Vec) = Vec(this.x + that.x, this.y + that.y)
}

val vector1 = Vec(1.0, 1.0)
val vector2 = Vec(2.0, 2.0)

val vector3 = vector1 + vector2
vector3.x  // 3.0
vector3.y  // 3.0
```
해당 코드는 `Vec`에 대해서 두개의 class를 더해서 새로운 class를 만들 수 있도록 연산자를 만들었다.

이것과 같이 여러 복잡한 표현식을 가독성을 높여서 활용할 수 있다.
``` Scala
case class MyBool(x: Boolean) {
  def and(that: MyBool): MyBool = if (x) that else this
  def or(that: MyBool): MyBool = if (x) this else that
  def negate: MyBool = MyBool(!x)
  def not(x: MyBool) = x.negate
  def xor(x: MyBool, y: MyBool) = (x or y) and not(x and y)
}
```

## Precedence (우선순위)

``` Scala
(characters not shown below)
* / %
+ -
:
= !
< >
&
^
|
(all letters)

a + b ^? c ?^ d less a ==> b | c

((a + b) ^? (c ?^ d)) less ((a ==> b) | c)
```

# BY-NAME PARAMETERS

모르겟군

# [ANNOTATIONS](https://docs.scala-lang.org/tour/annotations.html)
어노테이션은 선언에 대한 메터 정보에 대한 것과 관련이 있다.
``` Scala
object DeprecationDemo extends App {
  @deprecated("deprecation message", "release # which deprecates method")
  def hello = "hola"

  hello  
}
```
위에 예에서 `@deprecated` 와 같은 어노테이션은 해당 메소드를 사용하는데 있어서 워링을 보여준다.

이 어노테이션은 선언보다 앞서서 존재한다. 

## Annotations that ensure correctness of encodings
``` Scala
import scala.annotation.tailrec

def factorial(x: Int): Int = {

  @tailrec
  def factorialHelper(x: Int, accumulator: Int): Int = {
    if (x == 1) accumulator else factorialHelper(x - 1, accumulator * x)
  }
  factorialHelper(x, 1)
}
```
특정 조건을 만족하지 못하는 경우에 있어서 실제로 컴파일이 실패하는 경우가 발생할 수 있다.

`@tailrec` 어노테이셔은 tail-recusive를 만들도록 한다. 하지만 만약 밑에 예제처럼 만들게 되는 경우 컴파일되지 않을 것이다.

``` Scala
import scala.annotation.tailrec

def factorial(x: Int): Int = {
  @tailrec
  def factorialHelper(x: Int): Int = {
    if (x == 1) 1 else x * factorialHelper(x - 1)
  }
  factorialHelper(x)
}
```

## Annotations affecting code generation
자바와 같이 어노테이션을 통해서 코드를 자연스럽게 만들어주는 경우가 있다. 예제는 귀챃으니 찾아보세용~~

# PACKAGES AND IMPORTS

## Creating a package
자바와 매우 비슷하게 만들 수 있습니다. 막 만들 수도 있지만 자바와 비슷하게 어느정도 형식이라는 것이 존재합니다.
``` Scala
- ExampleProject
  - build.sbt
  - project
  - src
    - main
      - scala
        - users
          User.scala
          UserProfile.scala
          UserPreferences.scala
    - test
```
요렇게 만들게되면 users 라는 패키지 내부에 여러 스칼라 파일이 존재할 수 있습니다. 
그리고 패키지를 만드는데는 어느정도 룰이 있습니다.

``<top-level-domain>.<domain-name>.<project-name>``와 같은 룰을 통해서 만들어집니다.
예를 들면 구글에서 만든 자율주행관련 도메인과 실제 어떤 프로젝트인지 관련 패키지를 만든다고 해봅시다.
``` Scala
package com.google.selfdrivingcar.camera

class Lens

director structure: SelfDrivingCar/src/main/scala/com/google/selfdrivingcar/camera/Lens.scala
```

## Imports
`import` 절 같은 경우 다른 외부 패키지에 있는 접근 멤버를 가져오는 것입니다. 그래서 같은 패키지에 있는 경우는 따로 선언하지 않아도 됩니다.
``` Scala
import users._  // import everything from the users package
import users.User  // import the class User
import users.{User, UserPreferences}  // Only imports selected members
import users.{UserPreferences => UPrefs}  // import and rename for convenience
```
그 뿐만 아니라 스칼라는 어디에서든 `import`를 할 수 있습니다.
``` Scala
def sqrtplus1(x: Int) = {
  import scala.math.sqrt
  sqrt(x) + 1.0
}
```
그리고 가끔 이름 충돌로 인해서 프로젝트 루트에서 부터 패키지를 가져와야되는 경우가 존재할 수도 있습니다. 그런 경우 `__root__`를 사용하게 되면 됩니다.
``` Scala
package accounts

import _root_.users._
```

# PACKAGE OBJECTS




``` Scala

```
``` Scala

```
``` Scala

```
``` Scala

```
