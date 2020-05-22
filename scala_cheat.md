## Scala CheatSheet.
---

1. Substring Search
~~~Scala
val str:String = "This is great"

str.contains("This") // will return true.
str.contains("Not this") // will return false.
// work also with the element search in the list.
~~~
***
2. Declare multi-dimension Array.
~~~Scala
val multArr:Array[Array[Int]] = Array[Int].ofDim(3,3)
multArr(1)(2) = 2
~~~
***
3. for loop with multiple variable.
~~~Scala
for{
  i <- 1 to 10
  j <- 1 to 10
}{
  println(i, j)
}
~~~
***

4. Multiple Implicit Parameter can be passed simply as.


~~~Scala
def make(a:Int, b:Int)(Implicit c:Int, d:Double, e:String):Unit = {
    println(a,b,c,d)
}
~~~
***
5. Implicit function in scala is useful. Suppose some conversion is not feasible, and compiler is finding it hard to process. Then in such cases it will look for any possible implicit function available before throwing any error. If compiler will found something, it will use it, otherwise throw an error.
***

6. Defining a typed function in Scala.

~~~Scala
def func[T](name:T):T = {
  T
}
~~~
***

7. Taking variable arguments in scala `varargs :_ *`.
~~~Scala

def printPiper(st:String*){
  println(st.mkString(" "))
}

~~~
***
8. creating an partial function from an existing function.
~~~Scala
def namedPara(a:Int)(b:String)(c:Double):Double = {
  c
}

val partialFun1 = namedPara(3)_ // creation of partial Function

partialFun1("Don")(2.0F) //calling
~~~
***
9. Defining a higher order function which takes an function as a parameter.
~~~Scala
def fun1(a:Int)(b:String)(f:Double => Double){

val d:Double = b match{
  case "car" => 2.0F
  case "dog" => 2.5F
  case _ => 0.0F
}

val no:Double = a * d
f(no)
}

func(2)("car")(x => x*2.05)

~~~
***
10. Call By Name. `exchangeRate: => Double` used to denote call by name.
~~~Scala

def placeOrderWithByNameParameter(orders: List[(String, Int, Double)])
(exchangeRate: => Double):Double = {
  var totalCost: Double = 0.0
  orders.foreach {order =>
    val costOfItem = order._2 * order._3 * exchangeRate
    println(s"Cost of ${order._2} ${order._1} = £$costOfItem")
    totalCost += costOfItem
  }
  totalCost
}

val randomExchangeRate = new Random(10)
def usdToGbp: Double = {
  val rate = randomExchangeRate.nextDouble()
  println(s"Fetching USD to GBP exchange rate = $rate")
  rate
}

placeOrderWithByNameParameter(listOrders)(usdToGbp)

~~~
***
11. Lambda Expression function.
There will be parenthesis followed by `=>`. So, It can be assumed that the default signature will be `()=>`. On left of it you will have variable name, And on right You will have expression. `val x = () => 24`.
~~~Scala
val fun = (str:String, name:String) => {
  str + name
}
~~~
***
12. andThen. It is function1 type means it can work with one function type.
~~~Scala
val a = (t:Int) => t + 2
val b = (t:Int) => t + 4

val c = a.andThen(b)
c(4)
~~~
***
13. Defining a tail recursive function.
~~~Scala
@annotation.tailrec
def search(donutName: String, donuts: Array[String], index: Int): Option[Boolean] = {
  if(donuts.length == index) {
    None
  } else if(donuts(index) == donutName) {
    Some(true)
  } else {
    val nextIndex = index + 1
    search(donutName, donuts, nextIndex)
  }
}
~~~
You can read more about recursive functions at [tail rec annotation](http://allaboutscala.com/tutorials/chapter-3-beginner-tutorial-using-functions-scala/scala-tutorial-learn-create-tail-recursive-function-tailrec-annotation/), [scala.util.control.TailCalls._](http://allaboutscala.com/tutorials/chapter-3-beginner-tutorial-using-functions-scala/scala-tutorial-learn-create-tail-recursive-function-scala-control-util-tailcalls/) and also [this](http://allaboutscala.com/tutorials/chapter-3-beginner-tutorial-using-functions-scala/scala-tutorial-learn-create-trampoline-tail-recursive-function/).
***

14. Classes in scala.
```
class Person(name:String, rollNo:Long){


}

val p1 = new Person("Shubham", 3043L)
println(p1.name, p1.rollNo)

```
***
15. Class with Companion Object.
In turn, you will use the companion object to create instances for that particular class without having to make use of the new keyword.

Suppose class is.
```
class Donut(name: String, productCode: Long){

  def print = println(s"Donut name = $name, productCode = $productCode")

}
```
For this class companion object will be defined as follows.
A Companion Object is defined using the object keyword and the name of the object should be identical to the class name.

In addition, the companion object should define an apply() method which will be responsible for instantiating an instance of the given class.

***
16. Use Companion Objects' Apply Method As A Factory : Class Hierarchy Via Inheritance.

~~~scala

class Donut(name:String, productCode:Option[Long]=None){
  def print = println(s"Donut name = $name, productCode = ${productCode.getOrElse(0)}")
}

class GlazedDonut(name:String) extends Donut(name)
class VanillaDonut(name:String) extends Donut(name)

object Donut{
  def apply(name:String):Donut = {
    name match {
      case "Glazed Donut" => new GlazedDonut(name)
      case "Vanilla Donut" => new VanillaDonut(name)
      case _ => new Donut(name)
    }
  }
}

val glazedDonut = Donut("Glazed Donut")
val vanillaDonut = Donut("Vanilla Donut")

~~~

***
17. A variable defined in the object as private can be accessed through the class.
~~~scala
class Donut(name:String, productCode:Option[Long] = None){
  def print = println(s"Donut name = $name, productCode = ${productCode.getOrElse(0)}, uuid = ${Donut.uuid}")
}

object Donut {

 private val uuid = 1

 def apply(name: String, productCode: Option[Long]): Donut = {
  new Donut(name, productCode)
 }

 def apply(name: String): Donut = {
  new Donut(name)
 }
}


~~~
***
18. <b>Case Class</b> :
A case class is similar to any other classes except that it also creates the Companion Object. In addition, a case class will automatically create the `apply()`,  `toString()`, `hashCode` and `equals()` methods for you.

~~~Scala
case class Donut(name: String, price: Double, productCode: Option[Long] = None)
~~~~
***

19. Type Aliasing.
Type aliasing can be useful to help you provide more meaningful names which represent your business or domain objects without having to create unnecessary types.
Instead of using a Tuple2 type to represent a Donut with its corresponding quantity being bought by a customer, you can make use of type aliasing to alias the Tuple2 type.



As shown below, we are aliasing the Tuple2 type and giving it a more meaningful name of CartItem which is essentially a pair of Donut item with the quantity being bought.

~~~Scala
type CartItem[Donut,Int] = Tuple2[Donut, Int]

// create new object.
val cartItem = new CartItem(vanillaDonut, 4)

println(cartItem._1, cartItem._2)

~~~

20. Modify the functionality of the object using the implicit class.
~~~Scala
case class Donut(name:String, id:Long, productCode:Option[String])

val obj1 = Donut("RahMal",12, "TheInd123")

object DonutImplicit{

implicit class AugumentedDonut(donut:Donut){
  def uuid:String = s"${donut.name}_23454_${donut.productCode}"
}

import AugumentedDonut._

}

~~~

***

21. Package Object : To store and encapsulate common object and also implicit conversion.
Objects and Implicit conversions declared inside Package Objects, as the name implies, will be accessible to any classes or traits in the given package. In addition, Package Objects make it easy to inject these Objects and Implicit conversions into other scopes within your application.

~~~Scala
package object four {
  case class Donut(name: String, price: Double, productCode: Option[Long] = None)

  implicit class AugmentedDonut(donut: Donut) {
   def uuid: String = s"${donut.name} - ${donut.productCode.getOrElse(12345)}"
  }

  type DateTime = org.joda.time.DateTime // Storing type aliases inside Package Objects
                                        //  is typically a good idea because the type alias
                                        // will be available package wide.



}
~~~
***

22. Abstract class.
~~~Scala
abstract class Donut(name:String) {
  def printName: Unit
}
~~~
Any class that will inherit Donut has to provide definition to printName.

~~~Scala
class VanialDonut(name:String) extends Donut(name) {
  override def printName:Unit = println(name)
}

object VanialDonut{
  def apply(name:String):Donut = {
    new VanialDonut
  }
}

~~~
****

23. Abstract class extension using case class. One advantage, in case of `case class` we don't have to define the companion object.

~~~Scala
abstract class Donut(name:String){
  def printName:Unit
}

case class VanillaDonut(name:String) extends Donut(name){
  override def printName:Unit = println(name)
}

~~~

***
24. Creation of the typed class in Scala.
~~~Scala
abstract class Donut(name:String){
  def printName:Unit
}

case class VanillaDonut(name:String) extends Donut(name){
  overide def printName:Unit = println(name)
}

case class GlazzedDonut(name:String) extends Donut(name){
  overide def printName:Unit = println(name)
}

val vanillaDonut = VanillaDonut("Vanilla Donut")
val glazzedDonut = GlazzedDonut("Glazzed Donut")

// defining a class which expect donut type as input.
class ShoppingCart[D <: Donut](donuts: Seq[D]){
  def printCartItems:Unit = donut.foreach(_.println)
}

val shoppingCart: ShoppingCart[Donut] = new ShoppingCart(Seq[Donut](vanillaDonut, glazedDonut))
shoppingCart.printCartItems

~~~

25. Convert AnyVal to Long.

~~~Scala
val lVal = toBeNo.asInstanceOf[Number].longValue
~~~

26.  Paste the code in repl mode of scala.

~~~Scala
:paste
// followed by your code paste here

// To end pasting mode. Enter ctrl + D
~~~

27. enforcing type hierarchies through the use of variance - in particular covariance.

~~~Scala
abstract class Donut(name:String){

  def printName:Unit

}

case class VanillaDonut(name: String) extends Donut(name) {

   override def printName: Unit = println(name)

 }

case class GlazedDonut(name: String) extends Donut(name) {

   override def printName: Unit = println(name)

 }

 val vanillaDonut: VanillaDonut = VanillaDonut("Vanilla Donut")
vanillaDonut.printName

val glazedDonut: Donut = GlazedDonut("Glazed Donut")
glazedDonut.printName

class ShoppingCart[D <: Donut](donuts: Seq[D]) {

  def printCartItems: Unit = donuts.foreach(_.printName)

}
~~~
`ShoppingCart` will only take object of type `Donut` and will throw error if we pass `vanillaDonut` and `glazedDonut`. To avoid this we will need to use the covariance. `+D` in following example represents that sub-classes of Donut are also acceptable.
~~~Scala
class ShoppingCart2[+D <: Donut](donuts: Seq[D]) {

 def printCartItems: Unit = donuts.foreach(_.printName)

 }

 val shoppingCart2: ShoppingCart2[Donut] = new ShoppingCart2[VanillaDonut](Seq(vanillaDonut))
 shoppingCart2.printCartItems
~~~

In case if you want to pass super class in type class of child class you can use `-D`. For eg.
~~~Scala
val shoppingCart: ShoppingCart[VanillaDonut] = new ShoppingCart[Donut](Seq(glazedDonut))

class ShoppingCart2[-D <: Donut](donuts: Seq[D]) {

  def printCartItems: Unit = donuts.foreach(_.printName)

}
 
val shoppingCart2: ShoppingCart2[VanillaDonut] = new ShoppingCart2[Donut](Seq(glazedDonut))
shoppingCart2.printCartItems

~~~