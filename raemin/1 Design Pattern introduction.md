# 1. Design Pattern introduction

# 📚 디자인패턴 소개

## 📖 상속의 문제점 - 오리 문제

- 오리를 상속을 이용하여 구현하였을 경우
    
    ```java
    class Duck() {
    		quack(); // 오리 울음소리를 내는 method
    		swim(); // 오리가 헤엄을 치는 method
    		display(); // 오리의 모양을 서술해놓은 method
    }
    ```
    
    ```java
    class MalladDuck extends Duck
    class ReadHeadDuck extends Duck
    ```
    
- 위와 같이 구현되어 있는 경우에 대한 상황 분석
    1. 오리들이 날아다닐 수 있도록 `fly()` 메소드 추가
        - 간단히 Duck 클래스에 추가하면 됨
    2. RubberDuck 이라는 고무오리 클래스를 Duck 부모 클래스를 활용 할 때
        - 고무오리에 대해 swim, quack은 오버라이드를 통해 구현 할 수 있지만, `fly()` 메소드는 고무오리에 알맞지 않음
        - DecoyDuck ( 나무로된 모형오리 )를 추가 했을 때, `quack` 도 못하고, 날지도 못하는 동일한 문제가 발생 함
- 인터페이스를 활용하여 구현
    
    ```java
    interface Flyable {
    		fly();
    }
    
    interface Quackable {
    		quack();
    }
    
    class Duck {
    		swim();
    		display();
    }
    
    class MalladDuck extends Duck implements Quackable, Flyable
    class ReadHeadDuck extends Duck implements Quackable, Flyable
    class RubberDuck extends Duck implements Quackable
    class DecoyDuck extends Duck
    ```
    
    - 위와 같이 구현 했을 때, `fly()` 구현체를 재활용 할 수 없다는 문제점과, 인터페이스가 다양해짐에 따라 결론적으로 유연하지 않은 구조가 탄생 할 수 있음
- 변하는 부분과, 변하지 않는 부분 분리하고 위임을 통해 통합하기
    - Duck 클래스는 fly, quack 부분만 제외하고는 잘 동작한다고 가정
    - fly, quack 행동을 하는 구체적인 부분은 `flyBehavior`, `quackBehavior` 라는 인터페이스를 두고, 이 인터페이스를 구현하는 구체 클래스에 구현 해 둠
    - 그리고 오리 객체를 생성 할 때 이 인터페이스들의 구현체들을 인자로 받거나, 생성 해 줌
    
    ```java
    interface FlyBehavior {
    		void fly();
    }
    
    class FlyWithWings() implements FlyBehavior
    class FlyNoWay() implements FlyBehavior
    ```
    
    ```java
    interface QuackBehavior {
    		void quack();
    }
    
    class Quack implements QuackBehavior // 꽥 
    class Squeak implements QuackBehavior // 고무오리 소리 
    class MuteQuack implements QuackBehavior // 소리 안냄
    ```
    
    ```java
    class Duck {
    		FlyBehavior flyBehavior;
    		QuackBehavior quackBehavior;
    
    		void performQuack() {
    				quackBehavior.quack();
    		}
    
    		void performFly() {
    				flyBehavior.fly();
    		}
    }
    ```
    
    ```java
    class MalladDuck extends Duck {
    		MalladDuck() {
    				quackBehavior = new Quack();
    				flyBehavior = new FlyWithWings();
    		}
    }
    ```
    
    - 위와 같이 개발을 진행하면, RubberDuck 객체를 생성 할 때엔 그에 맞는 behavior 객체를 넣어 줌으로써 구현 할 수 있음
    - behavior 객체들은 Duck 클래스에 setter 함수를 넣어주면 실행시간 도중에도 쉽게 변경 할 수 있음
    - fly, quack에 대한 행동을 인터페이스를 통해 Duck 클래스로부터 캡슐화를 진행 해 준 것

## 📖 Strategy 패턴

알고리즘 군을 정의하고, 각각을 캡슐화하여 교환해서 사용할 수 있도록 개발하는 방식 

- 오리 문제에서, fly, quack에 대한 행동들을 behavior 인터페이스를 만들고, 그에 대한 객체를 생성하여 대입해주는 식으로 문제를 해결한 방식
- Strategy 패턴을 활용하여 상속을 구성으로 변경 할 수 있음
- behavior 객체는 인터페이스에 의존해 있기 때문에, setter를 통해 구현 객체를 언제든지 바꿀 수 있음

## 📖 소개된 디자인 패턴 원칙 정리

### 캡슐화

서비스에서 달라지는 부분을 찾아내고, 달라지는 부분은 달라지지 않는 부분으로 부터 분리 ( 캡슐화 ) 시켜야 함

- 이렇게 한다면 코드를 변경하는 과정에서 의도하지 않은 일이 일어나는 것을 방지하며 시스템의 유연성은 높일 수 있음
- 예를들어, 오리 문제에서 달라지는 부분은 `fly` , `quack` 과 같은 부분인데 이 부분을 변하지 않는 부분 ( `swim`, `display` )과 분리 시켜야 한다는 뜻
- 즉 새로운 요구사항이 있을 때 마다 바뀌는 부분이 있다면, 그 부분은 다른 부분 ( 바뀌지 않는 부분 ) 으로 부터 분리 되어야 함
- 시스템의 일부분을 다른 부분과 독립적으로 변화 시킬 수 있는 방법을 제공하기 위함

### 인터페이스에 의존하기

구현체가 아닌 인터페이스에 의존하도록 개발 함 

- 특정 구현체에 의존하도록 개발하는 것이 아니라, 인터페이스에 의존하도록 개발한다면 코드를 유연하게 유지 할 수 있음
- 예를들어 오리 문제에서, `fly` , `quack` 부분에 대한 구현체를 부모 클래스나 자식 클래스에서 직접 구현하여 사용한다면, `fly` / `quack` 에 대한 구현체를 프로그램 실행 도중엔 변경 할 수 없음
- 구체적인 구현에 의존하는 것이 아니라, `fly` / `quack` 행동을 하도록 하는 interface를 두고 interface를 구현한 객체를 인자로 받는다면 setter 등을 통해서 구체적인 행동 방안도 정할 수 있음
- 변수를 선언할 때엔 추상 클래스나, 인터페이스에 의존하도록 구현 해놓아야 다형성을 쉽게 활용할 수 있어 코드를 유연하게 유지 할 수 있음
- 즉, 사용하는 곳에서는 실제 코드의 구현체를 모르는 채로 두어야 함

### 상속보다는 구성 ( Composition )을 활용하기

상속 보다는 구성을 활용하는것이 더 나을떄가 있음

- A는 B이다 보다는, A에는 B가 있다 라는 식으로 개발을 하면 A와 B의 의존성을 떨어트릴 수 있음
- 즉, 행동을 상속받는것이 아니라 행동 객체들로 클래스를 구성하여 행동을 부여하는 것
- setter를 통해 행동 객체를 쉽게 변경 할 수 있음