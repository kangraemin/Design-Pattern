# Template method pattern

## 템플릿 메소드 패턴(Template Method Pattern)의 정의

**템플릿 메소드 패턴(Template Method Pattern)**에서는 알고리즘의 골격을 정의한다. 알고리즘의 여러 단계 중 일부는 서브클래스에서 구현할 수 있다. 템플릿 메소드를 이용하면 알고리즘의 구조는 그대로 유지하면서 서브클래스에서 특정 단계를 재정의할 수 있다.

```java
public abstract class CaffeineBeverage {

    final void prepareRecipe() {
        boilWater();
        brew();
        pourInCup();
        addCondiments();
    }

    abstract void brew();

    abstract void addCondiments();

    void boilWater() {
        System.out.println("물 끓는 중");
    }

    void pourInCup() {
        System.out.println("컵에 따르는 중");
    }
}
```

```java
public class Coffee extends CaffeineBeverage {

    public void brew() {
        System.out.println("필터로 커피 우려내는 중");
    }

    public void addCondiments() {
        System.out.println("우유와 설탕을 추가하는 중");
    }

}
```

```java
public class Tea extends CaffeineBeverage{
  public void brew(){
    System.out.println("차를 우려내는 중");
  }
  public void addCondiments(){
    System.out.println("레몬을 추가하는 중");
  }
}
```

[https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FccSzez%2Fbtq2Tcw9NKX%2F7EBKXKud8oWa16PRD80WU0%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FccSzez%2Fbtq2Tcw9NKX%2F7EBKXKud8oWa16PRD80WU0%2Fimg.png)

caffeineBeverage 클래스에서 해당 알고리즘을 독점(prepareRecipe())하는 것을 알수 있다. 그리고 이 알고리즘의 일부 구현만 서브 클래스에 의존한다. 그래서 알고리즘은 한 군데에 있기 때문에 그 부분만 고치면 된다

템플릿 메소드 패턴의 기본 구조는 아래와 같다.

- 알고리즘은 final 로 지정하여 서브클래스에서 수정할 수 없도록 하였습니다.
- 알고리즘의 일부 구현은 서브클래스에서 담당하도록 합니다.
- 후크는 다양한 용도로 사용할 수 있습니다.

```java
public abstract class AbstractClass {

    final void templateMethod() {
        primitiveOperation1();
        primitiveOperation2();
        concreteOperation();
        hook();
    }

    abstract void primitiveOperation1();

    abstract void primitiveOperation2();

    final void concreteOperation() {}

    void hook() {}
}
```

## 후크(Hook)

후크(hook)는 추상클래스에서 선언되는 메소드긴 하지만 기본적인 내용만 구현되어 있거나 아무 코드도 들어있지 않은 메소드다. 이렇게 하면 서브클래스 입장에서는 다양한 위치에서 알고리즘에 끼어들수 있다.

# 헐리우드 원칙

헐리우드 원칙: 먼저 연락하지 마세요. 저희가 연락 드리겠습니다 .

헐리우드 원칙을 활용하면"의존성 부패(dependency rot)를 방지할 수 있다. 의존성이 복잡하게 꼬여있는 것을 의존성 부패라고 부릅니다. 의존성이 부패되면 시스템이 어떤 식으로 디자인된 것인지 거의 아무도 알아볼 수 없게 됩니다.

템플릿 메소드 패턴은 헐리우드 원칙을 적용한 패턴이라고 할 수 있습니다.