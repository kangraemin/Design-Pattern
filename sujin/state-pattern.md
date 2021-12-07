# 10. 스테이트 패턴

어떤 행위를 수행할 때 상태에 행위를 수행하도록 위임하는 것이 스테이트 패턴이다. 각 상태를 클래스로 분리해서 표현하며, 수행하는 행위들을 메서드로 구현한다. 외부로부터 캡슐화하기 위해 인터페이스를 생성하여 시스템의 각 상태를 나타나는 클래스로 실체화한다는 의미이다.

사탕 뽑기 기계 프로그램을 구현한다고 가정하면 사탕뽑기 기계는 '동전없음', '동전있음', '상품배출', '상품품절' 상태를 가질 것입니다.

그리고, 사탕뽑기의 각 상태는 아래와 같은 행위를 통해서 다른 상태로 변이하게 됩니다.

```jsx
public class GumBallMachine {

    final static int SOLD_OUT = 0;
    final static int NO_QUARTER = 1;
    final static int HAS_QUARTER = 2;
    final static int SOLD = 3;

    private int numberOfGumBall = 0;
    public int state = SOLD_OUT;

    public GumBallMachine(int numberOfGum) {
        if(numberOfGum > 0) {
            state = NO_QUARTER;
        }
    }

    public int getNumberOfGumBall() {
        return numberOfGumBall;
    }

    public void insertQuarter() {
		// 동전삽입 시 동작 구현
    }

    public void returnQuarter() {
		// 동전반환 시 동작 구현
    }

    public void turnCrank() {
		// 뽑기 손잡이를 돌릴 때 동작 구현
    }

    public void dispense() {
		// 상품 배출 시 동작 구현
    }

}
```

아래와 같은 분기코드 반복되는 것을 느낄수 있습니다.

if(상태 == 동전있음) {

} else if(상태 == 동전없음) {

} else if(상태 == 상품배출) {

} else if(상태 == 상품품절) {

}

이런 반복문을 각 상태에 따른 클래스에 캡슐화 하여 각자 자기의 역할을 구현하게 하는 것이 바로 스테이트(State) 패턴입니다.

스테이트 패턴의 다이어그램은 아래와 같습니다.

```jsx
public interface State {

    public void insertQuarter();

    public void returnQuarter();

    public void turnCrank() ;

    public void dispense() ;

}

public class SoldState implements State {

    private GumBallMachine gumBallMachine;

    public SoldState(GumBallMachine gumBallMachine) {

        this.gumBallMachine = gumBallMachine;

    }

    @Override

    public void insertQuarter() {

        System.out.print("잠깐만 기다려주세요. 상품이 나오고 있습니다.");

    }

    @Override

    public void returnQuarter() {

        System.out.print("삽입된 동전이 없습니다.");

    }

    @Override

    public void turnCrank() {

        System.out.print("상품이 나오고 있습니다. 손잡이는 이미 돌리셨습니다.");

    }

    @Override

    public void dispense() {

        gumBallMachine.releaseGumBall();

        System.out.print("상품을 꺼냈습니다.");

        if(gumBallMachine.getNumberOfGumBall() > 0 ) {

            gumBallMachine.setState(gumBallMachine.getNoQuarterState());

        }

        else {

            System.out.print("상품이 모두 팔렸습니다.");

            gumBallMachine.setState(gumBallMachine.getSoldOutState());

        }

    }

}
```

이렇게 하면 상태가 추가, 수정되더라도 수정이 용이한 코드로 탈바꿈하게 됩니다.

### **스트래티지 패턴 VS 스테이트 패턴**

일반적으로 스트래티지 패턴은 서브클래스를 만드는 방법을 대신하여 유연성을 극대화하기 위한 용도로 쓰입니다.

상속을 이용해서 클래스의 행동을 정의하다 보면 행동을 변경해야할 때 마음대로 변경하기가 힘들죠.

하지만 스트래티지 패턴을 사용하면 구성을 통해 행동을 정의하는 객체를 유연하게 변경할 수 있습니다.

스테이트 패턴은 컨텍스트 객체에 수많은 조건문을 집어 넣는 대신에 사용할 수 있는 패턴이라고 보면 됩니다.

행동을 상태 객체 내에 캡슐화 시키면 컨텍스트 내의 상태 객체를 바꾸는 것만으로도 컨텍스트 객체의 행동을 바꿀 수 있습니다.

---

출처

[https://developside.tistory.com/78](https://developside.tistory.com/78)
