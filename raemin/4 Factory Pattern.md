# 4. Factory Pattern

# π Factory Pattern

## π κ°μ²΄ μμ±μ λνμ¬

- μλ°μμλ `new` ν€μλλ₯Ό νμ©νμ¬ ν΄λμ€μ κ°μ²΄λ₯Ό μμ± ν¨
- κ°μ²΄λ₯Ό μμ±νλ μμ μμ²΄κ°, κ°μ²΄λ₯Ό μμ±νλ κ³³κ³Ό μμ±λλ ν΄λμ€κ° μμ‘΄μ μμ λνλ
- μ‘°κ±΄μ λ°λΌ κ΅¬μ²΄ ν΄λμ€λ₯Ό κ³¨λΌ κ°μ²΄λ₯Ό μμ± ν  λμ, κ°μ²΄λ₯Ό μμ±νλ κ³³μμ  κ΅¬μ²΄ ν΄λμ€ λͺ¨λμ μμ‘΄νκ² λ¨
    
    ```java
    Duck duck;
    if (someCondtion) {
    		duck = RubberDuck();
    } else {
    		duck = DecoyDuck();
    }
    ...
    ```
    
- μμ κ°μ μ½λκ° λ§μ μ§λ€λ©΄, RubberDuck / DecoyDuck μ μμ±μλ₯Ό μμ  νμ λ μμ  ν΄μΌ ν  κ³΅κ°μ΄ λ§€μ° λ§μμ§λ©°, μ΄λ€ λ‘μ§μ ν΅ν΄ κ°μ²΄ μμ±μ νκ³  μλμ§ μΆμ νλκ²μ΄ μ λ§ νλ€μ΄μ§
- λ°λΌμ, κ°μ²΄λ₯Ό μμ±νλ λΆλΆμ μΊ‘μν ν΄μ€ νμκ° μμ

## π νΌμμ§ λ¬Έμ 

- μμ μ½λ
    
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
    
    - μ μ½λμμλ pizzaλ₯Ό μμ±νλ λΆλΆμ΄ ifλ¬ΈμΌλ‘ μ²λ¦¬λμ΄ μμ
    - μ΄λ, νΌμ μ’λ₯λ₯Ό μΆκ° / μ­μ  νκ² λλ€λ©΄ μΆκ°λλ νΌμ μλ§νΌ μ½λλ₯Ό μμ  ν΄ μ£Όμ΄μΌ ν¨
- κ°μ²΄ μμ± λΆλΆμ `Factory` λΌλ κ²μ ν΅ν΄ μΊ‘μνλ₯Ό μ§ν
    - κ°μ²΄ μμ±μ μ²λ¦¬νλ ν΄λμ€λ₯Ό FactoryλΌ μΉ­ν¨
    - μμ μ½λ
        
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
        
    - SimplePizzaFactoryλ₯Ό μ¬μ©νλ κ³³μ΄ λ§μΌλ©΄ λ§μμλ‘ μ μ½λκ° μ§μ  μμ±νλκ²λ³΄λ€ μ μ§λ³΄μμ μμ΄ ν¨μ¬ μ λ¦¬ ν¨
    - νΌμλ₯Ό μμ±νλ μμμ ν ν΄λμ€μ μΊ‘μνλ₯Ό μμΌ λμ κ²
    - νΌμ μ’λ₯λ₯Ό μΆκ° / μ κ±° ν΄μΌ νλ κ²½μ°, ν©ν λ¦¬ ν΄λμ€λ§ κ³ μΉλ©΄ λ¨

## π νΌμμ§ νλμ°¨μ΄μ¦ νμ₯μ λν λ¬Έμ 

- νΌμμ§ ( PizzaStore )λ₯Ό λ΄μ, μμΉ΄κ³ μλ λ°μΉ­νκ³ μ ν¨
- λ°λΌμ, PizzaStoreλ₯Ό μμνλ λ€λ₯Έ νΌμμ§μ λ§λ€μ΄μΌ ν¨
- μμ²λΌ simple Factoryλ₯Ό νμ© ν  λμ λ΄μ νΌμ κ°κ²λ₯Ό λ§λ€ λμλ simplePizzaFactoryμ λ΄μ Factoryκ° μ ννκ² μμΌλ§ λμμ μ ν¨
- νμ§λ§, κ°μ μ±μ΄ λΆμ‘±νλ―λ‘ μ€μκ° μμ μ μμ
- λ°λΌμ pizzaλ₯Ό λ§λλ methodλ₯Ό μμ ν΄λμ€μμ κ΅¬ννλλ‘ κ΅¬ν
    
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
    
- Pizza ν΄λμ€ κ΅¬ν
    
    ```java
    abstract class Pizza {
    		String name;
    		Stirng dough;
    		...
    		void prepare() {
    				... // νΌμ μ¬λ£ μ€λΉ 
    		}
    		...
    }
    ```
    
    ```java
    class NYStyleCheesePizza extends Pizza {
    		public NYStyleCheesePizza() {
    				name = "λ΄μ μ€νμΌ μΉμ¦ νΌμ"
    				dough = "μμ λμ°"
    				...
    		}
    }
    ```
    

## π μμ¬λ£μ λν λ¬Έμ 

- νΌμμ λ€μ΄κ°λ μμ¬λ£λ€μ Factoryλ‘ κ΄λ¦¬νκ³ μ ν¨ ( μΆμ ν©ν λ¦¬ ν¨ν΄ νμ© )
    
    ```java
    interface PizzaIngredientFactory {
    		Dough createDough();
    		Sauce createSauce();
    		...
    }
    ```
    
    ```java
    class NYPizzaIngredientFactory extends PizzaIngredientFactory {
    		// μ€μ  κ΅¬ν ...
    }
    ```
    
- νΌμ ν΄λμ€ λ³κ²½
    
    ```java
    abstract class Pizza {
    		String name;
    		Stirng dough;
    		...
    		abstract void prepare() // μ΄ λΆλΆμ Factoryλ₯Ό νμ©νμ¬ κ°μ Έμ΄
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
    				name = "λ΄μ μ€νμΌ μΉμ¦ νΌμ"
    				dough = pizzaIngredientFactory.createDough(); // Factory νμ©νμ¬ μ¬λ£ κ°μ Έμ΄
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
    

## π μκ°λ λμμΈ ν¨ν΄ μμΉ μ λ¦¬

### Simple Factory Pattern

κ°μ²΄ μμ±μ ν΄μ£Όλ Factory classλ₯Ό λ§λ€κ³ , μ΄ Factory classλ₯Ό νμ©νμ¬ κ°μ²΄ μμ±μ ν΄μ£Όλ ν¨ν΄ 

- μ μ½λμ SimplePizzaFactory μ²λΌ, κ°μ²΄ μμ±μ ν΄μ£Όλ Factoryλ₯Ό λ°λ‘ λΉΌλλ κ²
- μ΄ ν¨ν΄κ³Ό ν©ν λ¦¬ ν¨ν΄κ³Όλ λ€λ¦
- μ λ§ λ§μ΄ μ¬μ©λλ ν¨ν΄ μ€ νλ

### ν©ν λ¦¬ λ©μλ ν¨ν΄

μλΈν΄λμ€μμ κ°μ²΄ μμ±μ νλλ‘ μμ νλ ν¨ν΄ 

- μ΄λ₯Ό ν΅ν΄ κ°μ²΄ μμ±μ λν΄μ μΊ‘μν ν  μ μμ
- μμ μμμμ, Pizzaλ μ ν ν΄λμ€, PizzaStoreλ μμ°μ ( Creator ) ν΄λμ€μ ν΄λΉ λ¨
- μ νμ λν μΆμ ν΄λμ€μ, μμ°μμ λν μΆμ ν΄λμ€λ₯Ό κ°μ§κ³  κ΅¬ν ν΄λμ€λ€μ λ§λ€μ΄ μ¬μ©
- μ½λμμ μ€λ³΅λλ λ΄μ©μ μ κ±°ν  μ μκ³ , μ μ§λ³΄μκ° λ§€μ° νΈν΄μ§

### μμ‘΄μ± μ­μ  μμΉ

μΆμνλ κ²μ μμ‘΄νλλ‘ λ§λ€μ΄ κ³ μμ€ κ΅¬μ±μμμ μμ‘΄νλλ‘ κ°λ°νλ μμΉ

- νΉμ  κ΅¬νμ λ§μΆμ΄μ κ°λ°νλ κ²μ΄ μλλΌ, μΈν°νμ΄μ€μ λ§μΆμ΄ κ°λ° νλκ²
- κ³ μμ€ κ΅¬μ±μμκ° μ μμ€ κ΅¬μ±μμμ μμ‘΄νλ©΄ μλ¨
- μ¦, νΌμκ°κ² λΌλ μΆμν΄λμ€κ° κ΅¬μ²΄μ μΈ νΌμ μ’λ₯μ μμ‘΄ν΄μλ μλ¨
- μμ‘΄μ±μ΄ μμμ μλλ‘ λ΄λ €κ°λ κ²λ§ μλ κ²μ, μμ‘΄μ± λ°©ν₯μ μλ‘ μ­μ  μν€λ κ²
- λ³μμ κ΅¬μ²΄ ν΄λμ€μ λν νμμ μ§μ νμ§ μλ μ΅κ΄, μ΄λ―Έ κ΅¬νλμ΄ μλ κ΅¬μ²΄ ν¨μλ₯Ό μ¬μ μ νμ¬ μ¬μ©νλ κ²μ μ€μ΄λ κ²μ΄ μ’μ

### μΆμ ν©ν λ¦¬ ν¨ν΄

μ°κ΄μ± μλ, μμ‘΄νκ³  μλ κ°μ²΄λ₯Ό μμ°νλ μΈν°νμ΄μ€λ₯Ό λ§λ€μ΄ νμ©νλ ν¨ν΄

- νμ¬ Aμμ μ»΄ν¨ν°λ₯Ό λ§λ€ λ, νμ¬ Aμμ μμ°νλ μ νλ€λ‘λ§ μ»΄ν¨ν°λ₯Ό μμ°νλ€κ³  κ°μ  νμ λ, μ νμ μ¬λ£λ₯Ό κ°μ Έμ€λ / μμ°νλ μμμ interfaceλ‘ λ§λ€μ΄λκ³  κ΅¬μ²΄μ μΈ μ ν μμ°μ μ΄μ μμ‘΄νλλ‘ ν¨
- Pizza μμμμ, NYStyleCheesePizzaλ₯Ό λ§λ€ λ νμν μ¬λ£λ€μ μμ°νλ κ³³μ νκ³³ ( PizzaIngredientFactory )μμ κ΄λ¦¬νκ³ μ ν  λ νμ©νμ
- μ¦ μ°κ΄μ± μλ μΈμλ€μ νκ³³μμ κ΄λ¦¬νκ³ μ ν  λ μ£Όλ‘ μ¬μ© λ¨