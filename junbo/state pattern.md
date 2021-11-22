# state pattern

# State Pattern 이란?

스테이트(상태) 패턴(State Pattern)을 이용하면 객체의 내부 상태가 바뀜에 따라서 객체의 행동을 바꿀 수 있다. 마치 객체의 클래스가 바뀌는 것과 같은 결과를 얻을 수 있다.

스테이트 패턴은 컨텍스트 객체에 수많은 조건문을 집어넣는 대신에 사용할 수 있는 패턴이라고 생각할 수 있다.

![https://upload.wikimedia.org/wikipedia/commons/thumb/e/e8/State_Design_Pattern_UML_Class_Diagram.svg/400px-State_Design_Pattern_UML_Class_Diagram.svg.png](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e8/State_Design_Pattern_UML_Class_Diagram.svg/400px-State_Design_Pattern_UML_Class_Diagram.svg.png)

- Context라는 클래스에는 여러가지 내부 상태가 들어있을 수 있다. Context의 request() 메소드가 호출되면 그 작업은 상태 객체에게 맡겨진다.
- State 인터페이스에서는 모든 구상 상태 클래스에 대한 공통 인터페이스를 정의한다. 모든 상태 클래스에서 같은 인터페이스를 구현하기 때문에 바꿔가며 사용할 수 있다.
- ConcreteState에서는 Context로부터 전달된 요청을 처리한다. 각 ConcreteState에서 그 요청을 처리하는 방법을 구현한다. 이렇게 하면 Context에서 상태를 바꾸기만 하면 행동도 바뀌게 된다.
- 구상 상태 클래스는 얼마든지 많이 만들 수 있다.

# **스트래티지 패턴 VS 스테이트 패턴**

일반적으로 스트래티지 패턴은 서브클래스를 만드는 방법을 대신하여 유연성을 극대화하기 위한 용도로 쓰인다.

상속을 이용해서 클래스의 행동을 정의하다 보면 행동을 변경해야할 때 마음대로 변경하기가 힘달다. 하지만 스트래티지 패턴을 사용하면 구성을 통해 행동을 정의하는 객체를 유연하게 변경할 수 있습니다.

스테이트 패턴은 컨텍스트 객체에 수많은 조건문을 집어 넣는 대신에 사용할 수 있는 패턴이라고 보면 된다.

행동을 상태 객체 내에 캡슐화 시키면 컨텍스트 내의 상태 객체를 바꾸는 것만으로도 컨텍스트 객체의 행동을 바꿀 수 있다.