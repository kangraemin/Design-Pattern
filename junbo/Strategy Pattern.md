# Strategy Pattern

1. 상속

![strategy1.png](https://user-images.githubusercontent.com/7943694/75551365-675d5780-5a77-11ea-8278-2f0c8dbdd635.png)

Duck에 SuperClass를 만들고 quack(), swim(), fly()의 공통된 메소드를 만든다 다만 오리의 종류에 따라 생김새가 달라 display()는 추상 메소드로 둔다.

하지만 여기서 문제가 생겼는데 SuperClass에 fly() quack() 메소드가 추가 되면서 일부 적합하지 않는 SubClass에도 전부 추가 되어 장난감 오리가 날고 꽥꽥 되는 문제가 발생한 것이다.

2. 인터페이스 

![strategy2.png](http://www.blogjava.net/images/blogjava_net/jedikings/test1.JPG)

그러면 인터페이스는 어떨까?

display()나 swim() 같은 공통적인 메소드 들은 Duck이라는 SuperClass에 상속 받도록 하고 fly(), quack()등 여러가지 선택 할수 있는 행동들은 Interface로 정의 하여 각각의 SubClass들이 필요한 behavior를 implements하여 스스로 각자의 기능을 정의 하는 방법이다.

하지만 이방법은 행위 코드의 재사용이 불가능 해져서 코드의 중복이 발생하고 fly() 또는 quack()에서 변경해야 할 문제가 생긴다면 이를 구현 하고 있는 SUbclass들의 코드를 모두 변경 해줘야 하는 문제가 있다.

3. Strategy Pattern

![https://user-images.githubusercontent.com/7943694/75566420-fa57bb00-5a92-11ea-94f9-cf561a19dc3c.png](https://user-images.githubusercontent.com/7943694/75566420-fa57bb00-5a92-11ea-94f9-cf561a19dc3c.png)

그렇다면 어플리케이션에서 달라지는 부분을 찾아내고, 달라지지 않는 부분을 분리 시키자 바뀌는 부분은 따로 뽑아서 캡슐화(하나의 객체에 특정한 목적을 위해 필요한 것들을 하나로 묶고 외부에서 쉽게 접근하지 못하도록 은닉) 시키자 그렇게 하면 나중에 바뀌지 않는 부분에는 영향을 미치지 않은 채로 그 부분만 고치거나 확장 할수 있다.

```kotlin
abstract class Duck {
    private lateinit var flyBehavior: FlyBehavior
    private lateinit var quackBehavior: QuackBehavior

    fun setFlyBehavior(flyBehavior: FlyBehavior) {
        this.flyBehavior= flyBehavior
    }

    fun setQuackBehavior(quackBehavior: QuackBehavior) {
        this.quackBehavior= quackBehavior
    }

    fun performQuack() {
        quackBehavior.attack()
    }

    fun performFly() {
        flyBehavior.fly()
    }

}

interface FlyBehavior{
    fun fly()
}

class FlyWithWings: FlyBehavior {
    override fun move() {
        println("i can fly")
    }
}

class FlyNoWay: FlyBehavior{
    override fun move() {
        println("i can't fly")
    }
}

interface QuackBehavior{
    fun quack()
}

class MuteQuack: QuackBehavior{
    override fun quack() {
        println(" ")
    }
}

class Squeak: QuackBehavior{
    override fun quack() {
        println("quack quack~")
    }
}

fun client() {
    val realDuck: Duck
    val toyDuck: Duck
    
    realDuck.setFlyBehavior(FlyWithWings())
    realDuck.setQuackBehavior(Squeak())
    
    toyDuck.setFlyBehavior(FlyNoWay())
    toyDuck.setQuackBehavior(MuteQuack())
    
    realDuck.performFly()
    realDuck.performQuack()
    
    toyDuck.performFly()
    toyDuck.performQuack()
}

client()
```

먼저 fly와 quack같은 행위들을 interface로 정의 하고, 이를 implements 하는 구체적인 행위로 정의 한뒤, setter method를 통해 초기화 해준다.

이렇게 하면 기존에 오리 subclass에 있던 fly() 함수 대신 performFly함수를 선언 하고 내부에서 behavior class에 정의된 fly 함수를 호출 한다 그러면 오리 SubClass는 behavior의 디테일을 신경 쓰지 않아도 된다.
