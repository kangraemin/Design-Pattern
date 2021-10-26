# 1장

오리게임을 만들어보자

Duck 이라는 슈퍼클래스를 만든 뒤 그 클래스를 확장하여 다른 모든 종류의 오리를 만드려고 합니다

아래와 같이 구현해봤습니다.

```kotlin
abstract class Duck {
    abstract fun display()
    fun quack() = println("꽉꽉")
    fun fly() = println("날고있습니다")
}

class RealDuck() : Duck() {
    override fun display() = println("저는 오리입니다")
}

class RubberDuck() : Duck() {
    override fun display() = println("저는 러버덕 입니다")
}

fun main() {
    RealDuck().run {
        display()
        quack()
        fly()
    }

    RubberDuck().run {
        display()
        quack()
        fly()
    }
}
```

```kotlin
[실행결과]
저는 오리입니다
꽉꽉
날고있습니다
저는 러버덕 입니다
꽉꽉
날고있습니다
```

오리의 행동을 abstract class에서 구현하고 서브클래스가 상속 받도록 구현했습니다.

하지만 abstract에서 구현한 fly()와 quack()메소드를 그대로 사용했기 때문에 러버덕이 울고 나는 심각한 문제가 발생했습니다.

구현 클래스별도 다르게 동작해야하는 내용이 있을때 위 방법은 적절하지 않은것같습니다

위 문제점을 개선하여 다시 코드를 작성해봅시다

```kotlin
interface Duck {
    fun display()
    fun quack()
    fun fly()
}

class RealDuck() : Duck {
    override fun display() = println("저는 오리 입니다")
    override fun quack() = println("꽉꽉")
    override fun fly() = println("날고있습니다")
}

class RubberDuck() : Duck {
    override fun display() = println("저는 러버덕 입니다")
    override fun quack() = println("조용...")
    override fun fly() = println("날수없습니다")
}

fun main() {
    RealDuck().run {
        display()
        quack()
        fly()
    }

    RubberDuck().run {
        display()
        quack()
        fly()
    }
}
```

```kotlin
[실행결과]
저는 오리 입니다
꽉꽉
날고있습니다
저는 러버덕 입니다
조용...
날수없습니다
```

Duck interface에서 행위를 정의하고 실제 구현을 Duck interface를 상속받은 RealDuck Class와RubberDuck class가 하도록 코드를 작성하였습니다.

의도한 대로 잘 작동하지만 위 코드는 오리 종류가 늘어날 때마다 모든 행위 fly()와 quack() 메소드를 구현해야 하므로 코드 재사용이 불가능하고 사소한 변경점이 생겨도 수정해야 할 코드가 구현된 Duck Class 수 만큼 늘어날 것 입니다.

## 디자인 원칙

- 바뀌는 부분과 그렇지 않은 부분 분리하기
    - 바뀌는 부분은 따로 뽑아서 캡슐화시킨다 그렇게 하면 나중에 바뀌지 않는 부분에는 영향을 끼치지 않은 채로 그부분만 고치거나 확장 할 수 있다 - fly()와 quack()가 달라지는 부분아이다
- 구현이 아닌 인터페이스에 맞춰 프로그래밍 한다
    - 상위형식에 맞추어 프로그래밍한다
- 상속보다는 구성을 활용한다
    - A는 B이다 보다 A에는 B가 있다 가 나을 수 있습니다.
    - A에는 B가있다 는 오리에는 FlyBehavior 와 QuackBehavior이 있다 라는 말입니다.

## 스트래티지 패턴

알고리즘군을 정의하고 각각을 캡슐화하여 필요할 때 교체할 수 있도록 함으로써 동일한 문제를 다른 알고리즘으로 해결할 수 있게 하는 디자인 패턴, 스트래티지 패턴을 이용하면 알고리즘을 활용하는 클라이언트와 독립적으로 알고리즘을 변경할 수 있습니다.

스트래티지 패턴을 활용하여 Duck 클래스를 다시 작성해봅시다

## Behavio

```kotlin
interface FlyBehavior {
    fun fly()
}

class FlyWithWings : FlyBehavior {
    override fun fly() {
        println("날고있어요")
    }
}

class FlyNoWay : FlyBehavior {
    override fun fly() {
        println("...못날아요")
    }
}
```

오리의 나는 행동을 FlyBehavior를 상속 받아 구현했습니다. FlyBehavior를 사용하는 서브 클래스는 필요에 따라 FlyWithWings와 FlyNoWay를 사용할 수 있습니다

## Duck

```kotlin
abstract class Duck {
    abstract var flyBehavior: FlyBehavior
    abstract var quackBehavior: QuackBehavior
    abstract fun display()

    fun performQuack() {
        quackBehavior.quack()
    }

    fun performFly() {
        flyBehavior.fly()
    }
}

class RealDuck() : Duck() {
    override var flyBehavior: FlyBehavior = FlyWithWings()
    override var quackBehavior: QuackBehavior = Quack()

    override fun display() {
        println("저는 진짜 오리입니다")
    }
}

class RubberDuck() : Duck() {
    override var flyBehavior: FlyBehavior = FlyNoWay()
    override var quackBehavior: QuackBehavior = MuteQuack()

    override fun display() {
        println("저는 러버덕 입니다")
    }
}
```

모든 오리 클래스의 부모 클래스인 Duck 추상 클래스입니다. 자식 클래스는 FlyBehavior와 QuackBehavior에서 어떤 행위를 할지 정합니다 오리의 형태를 정의하는 display()함수를 구현해야합니다.

abstract class Duck을 상속받아 실제 사용하기 위한 RealDuck RubberDuck을 구현합니다.

이때 우는 행위와 나는 행위를 미리 Behavior interface를 상속받아 구현한 객체를 주입합니다.

display() 함수는 직접 구현합니다.

## Main

```kotlin
fun main() {
    RealDuck().run {
        display()
        performQuack()
        performFly()
    }

    RubberDuck().run {
        display()
        performQuack()
        performFly()
    }
}
```

```kotlin
[실행결과]
저는 오리 입니다
꽉꽉
날고있습니다
저는 러버덕 입니다
조용...
날수없습니다
```

## + 오리 소리 호출기

사냥꾼들이 오리 소리를 내기 위해 쓰는 오리 호출기가 있다고 해 봅시다, 어떻게 하면 Duck 클래스를 상속받지 않고 오리 호출기를 구현할 수 있을까요?

- 이미 오리 소리를 내는 코드는 QuackBehavior를 상속받은 Quack()에서 구현되어있다.

```kotlin
abstract class DuckPager {
    abstract var quackBehavior: QuackBehavior
    abstract fun display()
    fun performQuack() {
        quackBehavior.quack()
    }
}

class MyDuckPager:DuckPager(){
    override var quackBehavior: QuackBehavior = Quack()
    override fun display() {
        println("저는 오리 호출기 입니다")
    }
}
```