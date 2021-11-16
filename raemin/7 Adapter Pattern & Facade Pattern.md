# 7. Adapter Pattern & Facade Pattern

# 📚 Adapter / Facade Pattern

## 📖 Adapt new library problem - Adapter Pattern

- 기존의 시스템이 있고, 외주업체에서 추가적인 코드를 받는 상황을 가정
- 이때, 기존의 시스템에서 사용하고 있는 인터페이스와, 외주업체에서 준 코드의 인터페이스가 다른 경우가 있을 수 있음
- 기존의 시스템과, 외주업체의 코드를 수정할 수 없는 상황일 때 기존 시스템의 인터페이스와, 외주업체 코드의 인터페이스를 이어주는 어댑터를 만들 수 있음
- 이 어댑터를 만들어 인터페이스끼리 호환되도록 연결해주는 방식이 어댑터 패턴
    
    ```java
    interface Duck {
    		public void quack();
    		public void fly();
    }
    ```
    
    ```java
    interface Turkey {
    		public void gobble(); // 골골 거리기
    		public void fly();
    }
    ```
    
    ```java
    class TurkeyAdapter implements Duck {
    		Turkey turkey;
    
    		TurkeyAdapter(Turkey turkey) {
    				this.turkey = turkey;
    		}
    
    		void quack() {
    				turkey.gobble();
    		}
    
    		void fly() {
    				for (int i = 0; i < 5; i++) {
    						turkey.fly();
    				}
    		}
    }
    ```
    
    ```java
    Duck turkeyAdapter = new TurkeyAdapter(turkey);
    ```
    
    - 위와 같이, Turkey ( 외주업체 코드 ) 를 TurkeyAdapter 를 통해 Duck에 맞추어 줌
    - 그렇다면, turkey를 Duck으로 변환하여 동일하게 사용 할 수 있음
    - 이때, 어댑터를 통해 변경되는 인터페이스 ( Turkey )를 `어댑티 인터페이스` 라고 하고, 변경하고자 하는 결과 인터페이스 ( Duck )를 `타겟 인터페이스` 라고 함
    - 기존에 Duck을 기준으로 짜여져 있는 시스템을 `클라이언트` 라고 함

## 📖 Home theater problem - Facade Pattern

- Facade 는 외관, 겉모양을 의미하는 단어
- 만약 홈씨어터를 통해 영화를 본다면, 팝콘 기계를 작동시키고 → 방안의 불을 조절하고 → 스크린을 내리고 ... 등등의 작업을 진행 해야 함
- 영화를 볼 때 이런 기능들을 일일이 조작하기는것은 너무나도 귀찮고, 합리적이지 못함
- 따라서 이런 기능들을 미리 구현해놓은 Facade 클래스를 만들어 두고, Facade 클래스를 통해 영화를 키거나, 영화를 끄도록 구현
- 그러면 유저는 `영화 켜기` 라는 버튼만 누르면, 팝콘 기계를 작동시키고 → 방안의 불을 조절하고 등 이런 작업들을 한번에 진행 할 수 있도록 하는것
    
    ```java
    class HomeTheaterFacade {
    		PopconPopper popper;
    		Tuner tuner;
    		...
    
    		HomeTheaterFacade(PopconPopper popper, Tuner tuner ...) {
    				this.popper = popper;
    				this.tuner = tuner;
    		}
    
    		void watchMovie(String movie) {
    				popper.on();
    				popper.pop();
    				...
    		}
    }
    ```
    
    ```java
    HomeTheaterFacade homeTheaterFacade = new HomeTheaterFacade(...);
    homeTheaterFacade.watchMovie();
    ```
    
- 퍼사드 패턴을 적용함으로써 클라이언트 ( HomeTheaterFacade 객체를 사용하는 곳 )과, 서브시스템 ( HomeTheaterFacade 클래스 )를 분리 할 수 있음
- 즉, 홈시어터에 기능이 추가되더라도 클라이언트를 수정하는 것이 아니라 HomeTheaterFacade 만 수정하면 됨

## 📖 소개된 디자인 패턴 원칙 정리

### 어댑터 패턴

<aside>
💡 인터페이스를 클라이언트에서 사용하고자 하는 인터페이스 형태로 변환해주는 패턴

</aside>

- 어댑터를 활용하면, 인터페이스 호환성 문제 때문에 같이 쓸 수 없는 클래스들을 연결해서 사용 할 수 있음
- 위의 Turkey, Duck 예시를 봤을떄 Turkey 즉, 어댑티의 객체 구성을 가지고 어댑터 클래스를 만듬
- 즉, Turkey를 구현하고 있는 / 상속하고 있는 형식이라면 모두 어댑터를 통해 변환 할 수 있음을 의미
- 만약 `타겟 인터페이스` 에는 기능이 존재하지만, `어댑터 인터페이스` 에는 존재하지 않는 기능이라면 exception 처리를 잘 해주어야 함
    - 책에서, Enumeration → Iterator 코드의 예시를 제시
    - Iterator 인터페이스에는 `remove` 기능이 있지만, Enumeration에는 `remove` 기능이 없음을 이야기 함
    - 이떄엔  remove method를 호출 할 때엔 `UnsupportedOperationException` 을 던져 주도록 구현

### 퍼사드 패턴

<aside>
💡 서브시스템들이 조합된 통합된 인터페이스를 제공 해 줌

</aside>

- 따라서 클라이언트가 서브시스템들을 일일이 알 필요가 없이 Facade만 알면 서브시스템의 기능들을 묶어서 활용 할 수 있음
- 즉, 클라이언트에서 서브시스템의 기능들을 단순하게 사용할 수 있음

### 최소 지식 원칙

<aside>
💡 객체와 상호작용 하는 ( 의존되어 있는 ) 객체의 수가 작으면 작을수록 좋음

</aside>

- 예를들어, 객체 안에 객체를 가져와 호출을 하는 경우를 상정
    
    ```java
    float getTemp() {
    		Thermometer thermometer = station.getThermometer();
    		return thermometer.getTemperature();
    }
    ```
    
    ```java
    float getTemp() {
    			return station.getTemperature();
    }
    ```
    
    - 아래의 예시에서는 station 클래스 내부에서 `Thermometer` 객체를 호출함으로써, getTemp가 구현되어 있는 클래스에선 `Thermometer` 에 의존하지 않도록 구현 하였음
- 이 원칙을 통해 객체들 사이에서 의존성을 줄일 수 있음