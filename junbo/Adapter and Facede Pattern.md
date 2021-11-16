# Adapter and Facede Pattern

# 어댑터 패턴

한 클래스의 인터페이스를 클라이언트에서 사용하고자 하는 다른 인터페이스로 변환한다.

어댑터를 이용하면, 인터페이스 호환성 문제 때문에 같이 쓸 수 없는 클래스들을

연결해서 쓸 수 있다.

```java
// Duck Turkey 인터페이스 생성
public interface Duck{
    public void quack();
    public void fly();
}

public interface Turkey{
    public void gobble();
    public void fly();
}

public class MallardDuck implements Duck{
    public void quack(){
        System.out.println("Quack");
    }
    public void fly(){
        System.out.println("I'm flying");
    }
}

public class WildTurkey implements Turkey{
    public void gobble(){
        System.out.println("Gobble gobble");
    }
    public void fly(){
        System.out.println("I'm flying a short distance");
    }
}

//변환할(적응시킬) 인터페이스를 구현
public class TurkeyAdaptor implements Duck{
    Turkey turkey;

    public TurkeyAdaptor(Turkey turkey) {
        this.turkey = turkey;
    }

    public void quack() {
        turkey.gobble();
    }
    public void fly() {
        for (int i = 0; i < 5; i++) {
            turkey.fly();
        }
    }
}

public class DuckTestDrive{
    public static void main(String[] args) {
        MallardDuck duck = new MallardDuck();

				// 위처럼 어댑터를 만들면 Duck 객체에 Turkey 객체를 넣을 수 있음
        WildTurkey turkey = new WildTurkey();
        Duck turkeyAdaptor = new TurkeyAdaptor(turkey);

        System.out.println("turkey says");
        turkey.gobble();
        turkey.fly();

        System.out.println("duck says");
        duck.quack();
        duck.fly();

        System.out.println("turkeyAdaptor says");
        duck.quack();
        duck.fly();
    }
}
```

## 객체 어댑터

![https://user-images.githubusercontent.com/37353837/69004512-b7486080-0957-11ea-8b9b-a3c209c7172d.png](https://user-images.githubusercontent.com/37353837/69004512-b7486080-0957-11ea-8b9b-a3c209c7172d.png)

- 클라이언트는 타겟 인터페이스에 맞게 구현이 되어있고, 그걸 통해 메소드 호출
- 어댑터는 타겟 인터페이스를 구현하며 *어댑티 객체를 가지고 있음
- 어댑터는 어댑티 객체를 사용해 클라이언트의 요청을 응답
- 클라이언트는 결과를 받지만 중간에 어댑터가 껴있는지 알지 못함

* 어댑티 : 어댑터를 가운데 두고 클라이언트(타겟 인터페이스)와 반대의 위치에 있는 것 ex) Turkey

## 클래스 어댑터

- 클래스 어댑터 형식으로 구현하려면 다중 상속이 필요하기 때문에 자바에선 불가능
- 클래스 어댑터에선 타겟/어댑티 클래스 둘 모두를 상속 받는 서브 클래스를 만들어 요청 처리

![https://user-images.githubusercontent.com/37353837/69004513-b8798d80-0957-11ea-940a-7bbd9a800c6b.png](https://user-images.githubusercontent.com/37353837/69004513-b8798d80-0957-11ea-940a-7bbd9a800c6b.png)

# 퍼사드 패턴

퍼사드는 클래스 라이브러리 같은 어떤 소프트웨어의 다른 커다란 코드 부분에 대한 간략화된 인터페이스를 제공하는 객체이다**.** 퍼사드에서 고수준 인터페이스를 정의 하기 때문에 서브 시스템을 더 쉽게 사용할 수 있다.

### 데코레이터 패턴 vs 어댑터 패턴 vs 퍼사드 패턴

- 데코레이터 : 객체의 행동/책임을 추가
- 어댑터 : 인터페이스를 변환
- 퍼사드 : 일련의 행동을 감싸서 단순화

```java
// 홈시어터 사용 할려고 하는데 해야 할일이 많다
popper.on();
popper.pop();

lights.dim(10);

screen.down();

projector.on();
projector.setInput(dvd);
projector.wideScreenMode();

amp.on();
amp.setDvd(dvd);
amp.setSurroundSound();
amp.setVolume(5);

dvd.on();
dvd.play(movie);
```

```java
// 하지만 퍼사드 패턴을 사용하면 간단한 인터페이스로 사용할수 있다

public class HomeTheaterFacade {
    Amplifier amp;
    Tuner tuner;
    DvdPlayer dvd;
    CDPlayer cd;
    Projector projector;
    TheaterLights lights;
    Screen screen;
    PopcornPopper popper;

    public HomeTheaterFacade(...){
        ...
    }

    public void watchMovie(String movie) {
        popper.on();
        popper.pop();
        ...
        dvd.on();
        dvd.play(movie);
    }

    public void endMovie() {
        popper.off();
        dvd.stop();
        dvd.ejct();
        dvd.off();
    }
}
```

퍼사드 패턴은 다른 패턴과 달리 추상화도 사용하지 않고 단순하다. 그렇다고 별 볼 일 없는 패턴이라고는 할 수 없다.

모든 패턴을 사용하고, 객체를 만듦에 있어 **최소 지식 원칙**을 따르는 것을 지향해야 한다.

## 최소 지식 원칙

어떤 메서드에서든지 다음 네 종류의 객체의 메서드만을 호출하면 된다.

- 객체 자체
- 메서드에 매개변수로 전달된 객체
- 그 메서드에서 생성하거나 인스턴스를 만든 객체
- 그 객체에 속하는 구성요소