# 3. Decorator Pattern

# 📚 Decorator

## 📖 Starbucks Problem

- 스타벅스 음료에 대한 클래스에 대한 문제
- `Beverage` 라는 추상 클래스를 만들고, 구체 음료 클래스들을 만들었음
    
    ```java
    abstract class Beverage {
    		description // 음료에 대한 설명 값 
    		void getDescription() // 음료에 대한 설명 가져오기 
    		int cost() // 음료의 가격 산정 
    }
    ```
    
    - 위 처럼 구현 했을 시, Beverage를 구현하는 카페라떼, 디카페인 음료 등에서 음료의 가격을 결정해주고 사용하게 됨
    - 또, 카페라뗴에 우유가 추가 되었는지, 모카가 추가 되었는지에 따른 다양한 음료가 나오게 되는데 이를 또 하나하나의 클래스로 만들어 두어야 함
    - 따라서, 음료의 갯수만큼 클래스가 생기게 되고 이는 유지보수를 힘들게 하는 요인이 됨
- 따라서, milk, soy, mocha 등 재료를 두고, cost() 함수 내에서 재료의 가격을 더하는 식으로 가격 산출을 하고자 함
    
    ```java
    abstract class Beverage {
    		description // 음료에 대한 설명 값 
    		void getDescription() // 음료에 대한 설명 가져오기 
    		int cost() // 음료의 가격을 산정하는데, 각 재료의 가격을 더해서 최종값을 리턴 
    
    		// 각 요소가 들어갈지 안들어갈지 정하는 boolean variable
    		boolean soy
    		boolean milk
    		boolean mocha
    		...
    		
    		boolean getSoy()
    		void setSoy()
    		...
    }
    ```
    
    - 이렇게 두게 되면, 주요 음료 종류들 ( 디카페인 커피, 에스프레소 ... ) 에 대해서만 클래스를 만들고 나머지는 override 해서 사용하거나, getter / setter로 cost() 함수를 호출하기 전 가격을 조절 할 수 있음
    - 위 코드의 단점
        - 첨가물 가격이 바뀔 때 마다 기존 코드를 수정해야 함
        - 첨가물의 종류가 많아지면, 새로운 메소드와 프로퍼티를 추가 해 주어야 함
        - 음료에 특정 첨가물이 안들어가더라도 프로퍼티에 남아 있음
        - 손님이 모카를 2번 추가하는 경우에 대해 대응이 이상해짐
- 위코드도 여러 문제를 야기 할 수 있음
- 따라서, 주요 음료 사항들에 대한 클래스를 두고, 그 클래스 객체를 Mocha, Milk 등의 부재료의 클래스로 장식하는 방식인 데코레이터 패턴을 적용
    - 데코레이터 패턴이란
        
        객체에 추가적인 요건을 동적으로 추가하기 위한 디자인 패턴 
        
        - Component ( 부모 클래스 ), ConcreteComponent ( 꾸밈을 받을 클래스 ), Decorator ( 꾸며주는 클래스 ), ConcreteDecorator ( 꾸며주는 클래스의 구현체 )로 이루어 져 있음
        - 데코레이터는 클래스를 장식해주는 클래스
        - 데코레이터 패턴을 적용 할 객체 ( 주요 음료 종류들 ) 의 부모 클래스 ( 음료 )는, 자신을 꾸며주고 있는 객체 ( 데코레이터 클래스의 객체 ( Mocha, Whip ... )) 의 부모 클래스 ( 음료 )와 같음
        - 한 객체를 여러 데코레이터로 감쌀 수 있음
        - 데코레이터는 장식해주는 객체에 추가적인 작업을 할 수 있음
        - 객체는 동적으로 언제든 감쌀 수 있기 때문에, 데코레이터가 필요 할 때 언제든 장식 가능
    - 코드 예시
        
        ```java
        abstract class Beverage {
        		description
        		abstract int cost()
        }
        ```
        
        ```java
        // 꾸밈을 받는 클래스 
        class HouseBlend extends Beverage
        ```
        
        ```java
        abstract class CondimentDecorator extends Beverage {
        		abstract String getDescription()
        }
        ```
        
        ```java
        class Milk extends CondimentDecorator {
        		Beverage beverage;
        
        		public Milk(Beverage beverage) {
        				this.beverage = beverage;
        		}
        
        		int cost() {
        				return beverage.cost() + 300
        		}
        
        		String getDescription() {
        				return beverage.getDescription() + "우유가 첨가된"
        		}
        }
        ```
        
        ```java
        // 주문
        Beverage beverage = new HouseBlend();
        beverage = new Mocha(beverage);
        beverage = new Mocha(beverage);
        beverage = new Milk(beverage);
        beverage // 모카가 두번 첨가되고, 우유가 첨가된 음료가 나옴
        
        ```
        
    - 유의사항
        - 꾸밈을 받는 객체가 어떤 객체인지 알아야 하는, 즉 구체적인 클래스를 알아야 하는 작업을 Docorator의 구현 클래스에서는 진행 할 수 없음
        - 따라서 위와 같은 상황일 경우 데코레이터 패턴을 적용 하는것 자체가 맞는지를 의심 해 보아야 함
        - 데코레이터로 장식하는것을 잊는 경우를 방지하기 위해 캡슐화 등을 활용하는 경우도 잦음
        - 음료 이름이 여러번 출력 되는 등의 문제는 mapper를 따로 만들어 두어 활용

## 📖 소개된 디자인 패턴 원칙 정리

### OCP ( Open - Closed Principle )

클래스는 확장에 대해서는 열려있어야 하지만, 코드 변경에 있어서는 닫혀 있어야 함

- 기존 코드는 건드리지 않은채로, 새로운 행동을 간단하게 추가 할 수 있도록 해야 함
- 새로운 기능을 추가하는데 매우 유연하게 대응 할 수 있음
- 데코레이터 패턴을 통해 이를 이룰 수 있음
- 모든 코드 부분에서 이를 준수하는것은 불가능하고, 바뀔 확률이 높은 컴포넌트에 대해 진행 해 줌