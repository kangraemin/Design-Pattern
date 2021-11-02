# 4. Factory Pattern

# 📚 Factory Pattern

## 📖 객체 생성에 대하여

- 자바에서는 `new` 키워드를 활용하여 클래스의 객체를 생성 함
- 객체를 생성하는 작업 자체가, 객체를 생성하는 곳과 생성되는 클래스가 의존적임을 나타냄
- 조건에 따라 구체 클래스를 골라 객체를 생성 할 때엔, 객체를 생성하는 곳에선 구체 클래스 모두에 의존하게 됨
    
    ```java
    Duck duck;
    if (someCondtion) {
    		duck = RubberDuck();
    } else {
    		duck = DecoyDuck();
    }
    ...
    ```
    
- 위와 같은 코드가 많아 진다면, RubberDuck / DecoyDuck 의 생성자를 수정 했을 때 수정 해야 할 공간이 매우 많아지며, 어떤 로직을 통해 객체 생성을 하고 있는지 추적하는것이 정말 힘들어짐
- 따라서, 객체를 생성하는 부분을 캡슐화 해줄 필요가 있음

## 📖 피자집 문제

- 예시 코드
    
    ```java
    class Pizza
    
    Pizza orderPizza(String type) {
    		Pizza pizza;
    		if (type.eqauls("cheese")) {
    				pizza = CheesePizza();
    		} else if (type.eqauls("greek") {
    				pizza = GreekPizza();
    		}
    		...
    		pizza.prepare();
    		pizza.bake();
    		pizza.cut();
    		pizza.box();
    		return pizza;
    }
    ```
    
    - 위 코드에서는 pizza를 생성하는 부분이 if문으로 처리되어 있음
    - 이떄, 피자 종류를 추가 / 삭제 하게 된다면 추가되는 피자 수만큼 코드를 수정 해 주어야 함
- 객체 생성 부분을 `Factory` 라는 것을 통해 캡슐화를 진행
    - 객체 생성을 처리하는 클래스를 Factory라 칭함
    - 예시 코드
        
        ```java
        class SimplePizzaFactory {
        		public Pizza orderPizza(String type) {
        				if (type.eqauls("cheese")) {
        						pizza = CheesePizza();
        				} else if (type.eqauls("greek") {
        						pizza = GreekPizza();
        				}
        		}
        }
        ```
        
        ```java
        class Pizza
        
        class PizzaStore {
        
        		SimplePizzaFactory simplePizzaFactory;
        		
        		public PizzaStore(SimplePizzaFactory simplePizzaFactory) {
        				this.simplePizzaFactory = simplePizzaFactory;
        		}
        
        		Pizza orderPizza(String type) {
        				Pizza pizza = simplePizzaFactory.orderPizza(type);
        				pizza.prepare();
        				pizza.bake();
        				pizza.cut();
        				pizza.box();
        				return pizza;
        		}
        
        }
        ```
        
    - SimplePizzaFactory를 사용하는 곳이 많으면 많을수록 위 코드가 직접 생성하는것보다 유지보수에 있어 훨씬 유리 함
    - 피자를 생성하는 작업을 한 클래스에 캡슐화를 시켜 놓은 것
    - 피자 종류를 추가 / 제거 해야 하는 경우, 팩토리 클래스만 고치면 됨

## 📖 피자집 프랜차이즈 확장에 대한 문제

- 피자집 ( PizzaStore )를 뉴욕, 시카고에도 런칭하고자 함
- 따라서, PizzaStore를 상속하는 다른 피자집을 만들어야 함
- 위처럼 simple Factory를 활용 할 때엔 뉴욕 피자 가게를 만들 때에는 simplePizzaFactory에 뉴욕 Factory가 정확하게 와야만 동작을 잘 함
- 하지만, 강제성이 부족하므로 실수가 있을 수 있음
- 따라서 pizza를 만드는 method를 자식 클래스에서 구현하도록 구현
    
    ```java
    abstract class PiazzaStore {
    		public Pizza orderPizza(String type) {
    				Pizza pizza = createPizza(type);
    				pizza.prepare();
    				pizza.bake();
    				pizza.cut();
    				pizza.box();
    				return pizza;
    		}
    
    		protected abstract Pizza createPizza(String type)
    }
    ```
    
- Pizza 클래스 구현
    
    ```java
    abstract class Pizza {
    		String name;
    		Stirng dough;
    		...
    		void prepare() {
    				... // 피자 재료 준비 
    		}
    		...
    }
    ```
    
    ```java
    class NYStyleCheesePizza extends Pizza {
    		public NYStyleCheesePizza() {
    				name = "뉴욕 스타일 치즈 피자"
    				dough = "얇은 도우"
    				...
    		}
    }
    ```
    

## 📖 원재료에 대한 문제

- 피자에 들어가는 원재료들을 Factory로 관리하고자 함 ( 추상 팩토리 패턴 활용 )
    
    ```java
    interface PizzaIngredientFactory {
    		Dough createDough();
    		Sauce createSauce();
    		...
    }
    ```
    
    ```java
    class NYPizzaIngredientFactory extends PizzaIngredientFactory {
    		// 실제 구현 ...
    }
    ```
    
- 피자 클래스 변경
    
    ```java
    abstract class Pizza {
    		String name;
    		Stirng dough;
    		...
    		abstract void prepare() // 이 부분을 Factory를 활용하여 가져옴
    		...
    }
    ```
    
    ```java
    class NYStyleCheesePizza extends Pizza {
    
    		PizzaIngredientFactory pizzaIngredientFactory;
    
    		public NYStyleCheesePizza(PizzaIngredientFactory pizzaIngredientFactory) {
    				this.pizzaIngredientFactory = pizzaIngredientFactory;
    		}
    
    		public NYStyleCheesePizza() {
    				name = "뉴욕 스타일 치즈 피자"
    				dough = pizzaIngredientFactory.createDough(); // Factory 활용하여 재료 가져옴
    				...
    		}
    }
    ```
    
    ```java
    class NYPizzaStore extends PizzaStore {
    		protected Pizza createPizza(String item) {
    				Pizza pizza;
    				PizzaIngredientFactory ingredeintFactory = new NYPizzaIngredientFactory();
    
    				if (item.eqauls("cheese") {
    						pizza = new NYStyleCheesePizza(ingredeintFactory);
    				} else if ...
    
    				return pizza;
    		}
    }
    ```
    

## 📖 소개된 디자인 패턴 원칙 정리

### Simple Factory Pattern

객체 생성을 해주는 Factory class를 만들고, 이 Factory class를 활용하여 객체 생성을 해주는 패턴 

- 위 코드의 SimplePizzaFactory 처럼, 객체 생성을 해주는 Factory를 따로 빼두는 것
- 이 패턴과 팩토리 패턴과는 다름
- 정말 많이 사용되는 패턴 중 하나

### 팩토리 메서드 패턴

서브클래스에서 객체 생성을 하도록 위임 하는 패턴 

- 이를 통해 객체 생성에 대해서 캡슐화 할 수 있음
- 위의 예시에서, Pizza는 제품 클래스, PizzaStore는 생산자 ( Creator ) 클래스에 해당 됨
- 제품에 대한 추상 클래스와, 생산자에 대한 추상 클래스를 가지고 구현 클래스들을 만들어 사용
- 코드에서 중복되는 내용을 제거할 수 있고, 유지보수가 매우 편해짐

### 의존성 역전 원칙

추상화된 것에 의존하도록 만들어 고수준 구성요소에 의존하도록 개발하는 원칙

- 특정 구현에 맞추어서 개발하는 것이 아니라, 인터페이스에 맞추어 개발 하는것
- 고수준 구성요소가 저수준 구성요소에 의존하면 안됨
- 즉, 피자가게 라는 추상클래스가 구체적인 피자 종류에 의존해서는 안됨
- 의존성이 위에서 아래로 내려가는 것만 있던 것을, 의존성 방향을 위로 역전 시키는 것
- 변수에 구체 클래스에 대한 타입을 지정하지 않는 습관, 이미 구현되어 있는 구체 함수를 재정의 하여 사용하는 것을 줄이는 것이 좋음

### 추상 팩토리 패턴

연관성 있는, 의존하고 있는 객체를 생산하는 인터페이스를 만들어 활용하는 패턴

- 회사 A에서 컴퓨터를 만들 때, 회사 A에서 생산하는 제품들로만 컴퓨터를 생산한다고 가정 했을 때, 제품의 재료를 가져오는 / 생산하는 작업을 interface로 만들어두고 구체적인 제품 생산시 이에 의존하도록 함
- Pizza 예시에서, NYStyleCheesePizza를 만들 때 필요한 재료들을 생산하는 곳을 한곳 ( PizzaIngredientFactory )에서 관리하고자 할 때 활용했음
- 즉 연관성 있는 인자들을 한곳에서 관리하고자 할 때 주로 사용 됨