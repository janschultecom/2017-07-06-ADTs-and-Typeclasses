
# Example 1 - ADT Sum type
```scala
sealed trait TrafficLight
object Red extends TrafficLight
object Yellow extends TrafficLight
object Green extends TrafficLight

val trafficLight:TrafficLight = Green

def show(light: TrafficLight) = light match {
  case Green => "Go!"
  case Yellow => "Wait for it..."
  case Red => "Stop!"
}

println(show(trafficLight))
```

# Example 2 - ADT Product type
```scala
type X = Double
type Y = Double
type Point = (X,Y) // same as Tuple2[Double,Double]

val p1 = (5,3)
println(p1._1)
println(p1._2)

val (x,y) =  p1
println(s"x: $x, y:$y")
```

# Example 3 - ADT Record type
```scala
case class Person(name:String, age:Int)

val jan = Person("Jan", 32)
println(jan.name)
println(jan.age)

val product : Product = jan
println(product.productArity)
```

# Example 4 - ADT Composition
```scala
sealed trait Maybe
case object Empty extends Maybe
case class Just(value:Any) extends Maybe


def compute(i:Int):Maybe = if(i >= 0) {
  Just(i + 1)
} else {
  Empty
}

println(compute(1))
println(compute(-1))

val just = Just(0)
//just.value + 1
// case class Just[A](value:A) extends Maybe
```

# Example 5 - ADT Composition & parametrization
```scala
sealed trait Xor[+L,+R]
case class Left[L](value:L) extends Xor[L,Nothing]
case class Right[R](value:R) extends Xor[Nothing,R]

def div(x:Int,y:Int): String Xor Int = if(y != 0){
  Right(x / y)
} else {
  Left("Divisor is zero")
}

println(div(6,2))
println(div(6,0))
```

# Example 6 - ADT Recursive type
```scala
sealed trait Menu
case class MenuItem(name:String) extends Menu
case class MenuCategory(name:String, subMenu:Menu*)

val perfumes = MenuCategory("perfumes", MenuItem("Men"), MenuItem("Women"))
println(perfumes)

# Example 7 - Pro Tip #2
```scala
sealed trait PriceType
object PriceType {
  case object White extends PriceType
  case object Red extends PriceType
  case object Purchase extends PriceType
}

case class Price(value:Double, currency:String, priceType:PriceType)

val whitePrice = Price(10,"EUR", PriceType.White)

println(whitePrice)
```

# Example 8 - Pro Tip #3
```scala
sealed trait PriceType
object PriceType {
  case object White extends PriceType
  case object Red extends PriceType
  case object Purchase extends PriceType
}

import PriceType._

case class Price[T <: PriceType](value:Double, currency:String)

val whitePrice = Price[White.type](10,"EUR")
val redPrice = Price[Red.type](10,"EUR")

def printWP(whitePrice:Price[White.type]) =
  println(whitePrice)

printWP(whitePrice)
printWP(redPrice)
```

# Example 9 - Expression problem
```scala
sealed trait Shape {
  def area:Double
  def perimeter:Double
}
case class Rectangle(width:Double,height:Double)
  extends Shape {
  override def area = width * height
}
case class Triangle(width:Double, height:Double) extends Shape {
  override def area = 1.0 / 2.0 * width * height
}

case class Circle(radius:Double) extends Shape {
  override def area = Math.PI * radius * radius
}

println(Triangle(2.0,1.0).area)
println(Circle(1.0).area)
```

# Example 10 - Area typeclass
```scala
sealed trait Shape
case class Rectangle(width:Double,height:Double)
case class Circle(radius:Double)

trait Area[E] {
  def area(elem:E):Double
}

implicit val rectangleArea = new Area[Rectangle] {
  override def area(in: Rectangle): Double = in.width * in.height
}

def displayArea[I](in:I)(implicit tc:Area[I]) = {
  val ar:Double = tc.area(in)
  println(s"Area: $ar")
}

displayArea(Rectangle(10,5))

object syntax {
  implicit class AreaOps[E](elem:E)(implicit tc:Area[E]) {
    def toArea = tc.area(elem)
  }
}

import syntax._

println(Rectangle(2,2) toArea)

implicit val circleArea = new Area[Circle] {
  override def area(elem: Circle): Double = Math.PI * elem.radius * elem.radius
}

println(Circle(1) toArea)
```

# Example 11 - Show typeclass
```scala
import cats._
import cats.instances.all._
import cats.syntax.show._

case class Person(name:String, age:Int)
val jan = Person("Jan",32)

object ShowInstances {
  implicit val testShow = new Show[Person] {
    def show(p:Person) = s"Person(name=${p.name},age=${p.age})"
  }

  implicit val logShow = new Show[Person] {
    def show(p:Person) = s"[name=${p.name}]"
  }

  implicit val jsonShow = new Show[Person] {
    def show(p:Person) = s"""{ "name":"${p.name}","age":${p.age}}"""
  }
}

import ShowInstances.testShow //logShow or jsonShow

println(jan show)
```
