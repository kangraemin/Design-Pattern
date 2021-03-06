# Composite pattern

# **컴포지트 패턴의 정의**

컴포지트 패턴을 이용하면, 객체들을 트리 구조로 구성해서 부분과 전체를 나타내는 계층 구조를 만들 수 있다.

클라이언트에서 개별 객체와 다른 객체들로 구성된 복합 객체(compoiste)를 똑같은 방법으로 다룰 수 있다.

## **컴포지트 패턴의 클래스 다이어그램**

![https://user-images.githubusercontent.com/37353837/70488796-28d19400-1b3d-11ea-8f7d-b1be63897803.png](https://user-images.githubusercontent.com/37353837/70488796-28d19400-1b3d-11ea-8f7d-b1be63897803.png)

컴포지트 패턴을 이용하면, 복합 객체와 개별 객체를 구분할 필요가 없어진다. 복합 구조에 들어있는 것을 **구성요소**라고 부른다. 구성요소에는 **복합 객체**와 **잎(leaf) 노드**가 있다 .**잎(leaf) 노드**든 아니든 똑같이 구현할 수 있게 되는 것이다.

**Component**

- Component는 Composite와 Leaf의 부모가 되고 해당 두 객체가 가질 수 있는 동작들을 가지고 있다.

**Composite**

- Composite는 Component를 컬렉션으로 가지고 있다.
- 즉 자기 자신인 Composite와 Leaf를 가질 수 있는 것이다.
- 컬렉션으로 가지고 있는 Component들을 추가 제거할 수 있고 opertion() 또한 가능하다.

**Leaf**

- opertion()만 할 수 있는 객체이다.

컴포지트 패턴은 단일 책임 원칙을 깨면서 투명성을 확보하기 위한 패턴이다. 여기서 투명성은 component 인터페이스에 자식들을 관리하기 위한 기능과 리프 노드로써의 기능을 전부 집어넣음으로써 클라이언트에서 복합 객체와 리프 노드를 똑같은 방식으로 처리할 수 있도록 한다. 어떤 원소가 복합 객체인지 리프 노드인지가 클라이언트 입장에서는 투명하게 느껴지는 것이다.

Component 클래스에는 두 종류의 기능이 모두 들어가 있으므로 안정성은 조금 떨어진다. 사용하지 말아야 할 기능을 호출할 수도 있기 때문이다. 이러한 문제는 설계 결정 사항에 포함된다. 안정성 측면을 고려한다면 여러 역할로 분리해서 다른 인터페이스로 구현할 수도 있다. 하지만 이렇게 설계한다면 각 인스턴스를 확인하기 위해 조건문 또는 "instanceof" 와 같은 코드가 추가될 것이다. 혹은 예외를 던질 수도 있다.

### **컴포지트 패턴의 구현 문제**

### **장점**

- **클라이언트를 단순화**시킬 수 있다.(복합 객체를 사용하고 있는지 잎(left) 객체를 사용하고 있는지에 대해 신경쓰지 않아도 되기 때문이다.)
- 메서드 하나만 호출하면, 전체 구조에 대해서 반복해서 작업을 처리할 수 있는 경우도 자주 있다.

### **1. 컴포지트 패턴에서 아무 의미가 없는 메서드가 생기는 경우**

아무일도 하지 않거나 널(Null) 또는 false를 상황에 맞게 리턴하는 방법이 있다.또는 예외를 던질 수 있다.

### **2. 복합 구조를 탐색하는데 너무 복잡할 경우**

복합 노드를 **캐싱**해두는 게 효과적이다.

### **결론**

> 컴포지트 패턴을 적용할 때는 여러 가지 장단점을 고려해야 한다.상황에 따라 투명성과 안전성 사이에서 적절한 평형점을 찾아야 한다.
>