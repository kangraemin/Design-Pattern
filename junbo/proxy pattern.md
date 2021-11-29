# proxy pattern

**프록시 패턴(Proxy Pattern)** 은 어떤 객체에 대한 접근을 제어하기 위한 용도로, 대리인이나 대변인에 해당하는 객체를 제공하는 패턴이다.

프록시 패턴을 사용하면 원격 객체라든가 생성하기 힘든 객체, 보안이 중요한 객체와 같은 다른 객체에 대한 접근을 제어하는 대변자 객체를 만들 수 있다.

# 원격 프록시(Remote Proxy)

### 원격 프록시의 역할

- 원격 프록시: 원격 객체에 대한 로컬 대변자 역할을 한다. 프록시의 메소드를 호출하면 그 호출이 네트워크를 통하여 전달되어 결국 원격 객체의 메소드가 호출된다. 그리고 그 결과는 다시 프록시를 거쳐 클라이언트에게 전달된다.
- 원격 객체(remote object): 다른 JVM의 Heap에서 살고 있는 객체(일반적으로 얘기하자면 다른 주소 공간에서 돌아가고 있는 원격 객체)이다.
- 로컬 대변자(local representative): 로컬 대변자의 어떤 메소드를 호출하면, 다른 원격 객체한테 그 메소드 호출을 전달해주는 역할을 맡고 있는 객체이다.

![https://slidesplayer.org/slide/14686464/90/images/9/%EC%9B%90%EA%B2%A9+%EB%A9%94%EC%86%8C%EB%93%9C+%ED%98%B8%EC%B6%9C+%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8+%ED%9E%99+%EC%84%9C%EB%B2%84+%ED%9E%99+%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8+%EA%B0%9D%EC%B2%B4+%EC%84%9C%EB%B9%84%EC%8A%A4+%EA%B0%9D%EC%B2%B4+doBigThing%28%29+doBigThing%28%29.jpg](https://slidesplayer.org/slide/14686464/90/images/9/%EC%9B%90%EA%B2%A9+%EB%A9%94%EC%86%8C%EB%93%9C+%ED%98%B8%EC%B6%9C+%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8+%ED%9E%99+%EC%84%9C%EB%B2%84+%ED%9E%99+%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8+%EA%B0%9D%EC%B2%B4+%EC%84%9C%EB%B9%84%EC%8A%A4+%EA%B0%9D%EC%B2%B4+doBigThing%28%29+doBigThing%28%29.jpg)

- 클라이언트 객체(Client Object)는 진짜 서비스의 메소드를 호출한다고 생각한다. 클라이언트 보조 객체에서 실제로 자신이 원하는 작업을 처리한다고 생각한다.
- 클라이언트 보조 객체(Client Helper)는 진짜 서비스인 척 하지만, 실제로는 진짜 객체에 대한 프록시(proxy)이다.
- 서비스 보조 객체(Service Helper)는 클라이언트 보조 객체로부터 요청을 받아 그 내용을 해석하여 진짜 서비스에 있는 메소드를 호출하낟.
- 서비스 객체(Service Object)가 진짜 서비스로, 실제로 작업을 처리하는 메소드가 들어있다.

## Java RMI(Remote Method Invocation 원격 메소드 호출)

기본적으로, 다른 힙에 위치한 객체에 대한 레퍼런스를 가져올 수는 없다. 어떤 변수가 어떤 객체를 참조하려면, 그 객체는 그 선언문이 들어있는 코드와 같은 힙 공간에 위치해야만 한다.

하지만 RMI를 이용하면 원격 JVM에 있는 객체를 찾아서 그 메소드를 호출할 수 있다.

RMI은 원격 메소드 기초에 나와있는 그림과 같은 로직을 가진다.

하지만 클라이언트 보조 객체를 스텁(Stub), 서비스 보조 객체를 스켈레톤(Skeleton)이라고 부른다.

## 원격 서비스 만들기

원격 서비스 만들기 위한 다섯 가지 단계가 있다

### 원격 인터페이스 만들기

원격 인터페이스에서는 클라이언트에서 원격으로 호출할 수 있는 메소드를 정의한다.

클라이언트에서 이 인터페이스를 서비스의 클래스 형식으로 사용하며, 스텁과 실제 서비스에서는 모두 이 인터페이스를 구현해야 한다.

### 서비스 구현 클래스 만들기

원격 인터페이스에서 정의한 원격 메소드를 실제로 구현한 코득다 들어있는, 실제 작업을 처리하는 클래스이다.

클라이언트에서 이 객체에 있는 메소드를 호출하게 된다.

### rmic를 이용한 스터브와 스켈레톤 만들기

클라이언트 및 서비스 '보조객체'를 생성한다.

### RMI 레지스트리 실행

클라이언트에서 rmiregistry 명령어를 실행함으로 인해, 이 레지스트리로부터 프록시(클라이언트 스텁)를 받아갈 수 있다.

### 원격 서비스 시작

서비스 객체를 가동시킨다

서비스를 구현한 클래스에서 서비스의 인스턴스를 만들고, 그 인스턴스를 RMI 레지스트리에 등록한다.

# 가상 프록시

가상 프록시는 생성하는 데에 많은 비용이 드는 객체를 대신하는 역할을 맡는다.

![https://user-images.githubusercontent.com/37353837/71341839-576c5780-259e-11ea-93e1-e5d10f743ac8.png](https://user-images.githubusercontent.com/37353837/71341839-576c5780-259e-11ea-93e1-e5d10f743ac8.png)

실제로 진짜 객체가 필요하게 되기 전까지 객체의 생성을 미루게 해주는 기능을 제공하기도 하며, 객체 생성 전이나 생성 도중에 객체를 대신하기도 한다.

객체 생성이 완료되면 그냥 RealSubject에 요청을 직접 전달해 준다.

(느낌이 쓰레드와 비슷한듯?)

# 보호 프록시

보호 프록시는 접근권한을 바탕으로 객체에 대한 접근을 제어하는 프록시이다.

자바의 java.lang.reflect 패키지에 들어있는 프록시 기능을 이용하면 즉석에서 한개 이상의 인터페이스를 구현하고 메소드 호출을 지정해 준 클래스에 전달할 수 있는 프록시 클래스를 만들 수 있다.

실제 프록시 클래스는 실행 중에 생성되기 때문에 **동적 프록시(Dynamic Proxy)**라고 부른다.

[https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F75hUm%2Fbtq4o7oVhy4%2FF2tUKh6VjqbTlKRamrTNbK%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F75hUm%2Fbtq4o7oVhy4%2FF2tUKh6VjqbTlKRamrTNbK%2Fimg.png)

- 프록시는 **Proxy**와 **InvocationHandlerClass** 두개의 클래스로 구성된다.
- **Proxy**클래스는 자바에 의해 생성되며 Subject 인터페이스 전체를 구현한다.
- **InvocationHandler** 클래스는 Proxy 객체에 대한 모든 메소드 호출을 전달받는 **InvocationHandler**를 제공해야한다. **InvocationHandler**에서 **RealSubject** 객체에 있는 메소드에 대한 접근을 제어한다.

Proxy 클래스는 자바가 만들어주기 때문에, Proxy 클래스에게 무슨 일을 할 지 알려주기 위한 방법이 필요하다.

Proxy를 직접 구현하지 않기 때문에 필요한 코드를 만들 수 없는데, 이 코드를 InvocationHandlerClass에 집어넣음으로써, Proxy에서 메소드 호출을 받으면 항상 InvocationHandlerClass에 진짜 작업을 부탁하게끔 만든다.

### 그 외의 프록시들

- `방화벽 프록시(Firewall Proxy)`: 일련의 네트워크 자원에 대한 접근을 제어함으로써 주 객체를 "나쁜" 클라이언트들로부터 보호해 준다.
- `스마트 레퍼런스 프록시(Smart Reference Proxy)`: 주 객체가 참조될 때마다 추가 행동을 제공한다.
- `캐싱 프록시(Caching Proxy)`: 비용이 많이 드는 작업의 결과를 임시로 저장해 준다. 여러 클라이언트에서 결과를 공유하게 해 줌으로써 계산 시간 또는 네트워크 지연을 줄여주는 효과도 있다.
- `동기화 프록시(Synchronization Proxy)`: 여러 스레드에서 주 객체에 접근하는 경우에 안전하게 작업을 처리할 수 있게 해준다.
- `복잡도 숨김 프록시(Complexity Hiding Proxy)`: 복잡한 클래스들의 집합에 대한 접근을 제어하고, 그 복잡도를 숨겨준다. `퍼사드 프록시(Facade Proxy)`라고 부르기도 한다.
    - `퍼사드 프록시`에서는 접근을 제어하지만 `퍼사트 패턴`에서는 대체 인터페이스만 제공한다는 점이 차이점이다.
- `지연 복사 프록시(Copy-On-Write Proxy)`: 클라이언트에서 필요로 할 때까지 객체가 복사되는 것을 지연시킴으로써 객체의 복사를 제어한다. (변형된 가상 프록시라고 할 수 있다.)