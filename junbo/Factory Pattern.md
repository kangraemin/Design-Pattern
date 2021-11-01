# Factory Pattern

## 문제점

```jsx
class DuckWorld {
    Duck duck;
    
    public void initDuck(DuckType type) {
        if (type == DuckType.PICNIC) {
            duck = new MallardDuck();
        } else if(type == DuckType.HUNTING) {
            duck = new DecoyDuck();
        } else if (type == DuckType.PICNIC.INBATHTUB) {
            duck = new RubberDuck();
        }
    }
}
```

위 코드를 보면 뭔가 변경하거나 확장해야 할 때 코드를 확인 하고 추가 제거 해야한다. 그 뜻은 관리 및 갱신이 힘들고 오류가 발생할 문제가 많아진다.

위의 코드는 구상 클래스를 사용하기 때문에, 새로운 타입이 추가될 때마다 구상 클래스가 추가되어야 한다. 즉 **변화에 대해 닫혀 있는** 코드가 되는 것이다.

위의 문제를 해결하려면 먼저 지속적으로 바뀌는 부분을 찾아야 한다. 객체지향에 가장 중요한 원칙 중 하나는 **바뀔 수 있는 부분을 찾아내서 바뀌지 않는 부분과 분리시켜야 한다.**

재료의 타입에 맞게 피자를 만드는 객체지향 나라의 피자집이 있다고 생각해 보자. 피자를 만드는 코드는 다음과 같다.

```java
Pizza orderPizza(String type) {
	Pizza pizza;
	
	if(type.equals("cheese")) {
		pizza = new CheesePizza();
	} else if(type.equals("greek")) {
		pizza = new GreekPizza();
	} else if(type.equals("pepperoni") {
		pizza = new PepperoniPizza();
	}

	pizza.prepare();
	pizza.bake();
	pizza.cut();
	pizza.box();
	
	return pizza;
}
```

이제 새로운 피자가 추가 되고 그리스식 피자는 별로 안 팔려서 메뉴에서 사라졌다고 하자

```java
Pizza orderPizza(String type) {
	Pizza pizza;
	
	if(type.equals("cheese")) {
		pizza = new CheesePizza();
	} else if(type.equals("pepperoni") {
		pizza = new PepperoniPizza();
	} else if (type.equals("clam")) {
		pizza = new ClamPizza();
	} else if (type.equals("veggie")) {
		pizza = new eggiePizza();
	}

	pizza.prepare();
	pizza.bake();
	pizza.cut();
	pizza.box();
	
	return pizza;
}
```

아까 DuckWorld에서 보면 알 수 있듯이, 구상 클래스를 선택하는 부분은 피자가 추가되고 삭제 될때마다 지속적으로 변경이 발생할 수 밖에 없는 구조이다.

```java
if(type.equals("cheese")) {
    pizza = new CheesePizza();
} else if(type.equals("greek")) {
    pizza = new GreekPizza();
} else if(type.equals("pepperoni")) {
    pizza = new PepperoniPizza();
}
```

변경 되는 부분만 때어내서 객체를 생성하는 역할만 맡는 다른 객체에 집어넣도록 하자. 새로 만드는 객체는 펙토리(Factory)라고 부르겠다. 이 객체를 SimplePizzaFactory 라고 하면, orderPizza() 메소드는 새로 만든 객체의 클라이언트가 된다. 즉, 새로 만든 객체를 호출한다.

그러면 orderPizza() 메소드에서는 어떤 피자를 만드는지 알 필요가 없다. orderPizza() 메소드에서는 새로 만들어 진 피자를 받아서, 그 인터페이스에서 정의한 prepare(), bake(), cut(), box() 메소드를 호출하면 된다.

코드로 표현하면 다음과 같다.

```java
public class SimplePizzaFactory {
	public Pizza createPizza(String type) {
		Pizza pizza = null;
	
		if(type.equals("cheese")) {
			pizza = new CheesePizza();
		} else if(type.equals("greek")) {
			pizza = new GreekPizza();
		} else if(type.equals("pepperoni")) {
			pizza = new PepperoniPizza();
		}
		
	    return pizza;
	}
}
```

그다음 클라이언트 쪽에서는 

```java
public class PizzaStore {
	SimplePizzaFactory factory;

	public PizzaStore(SimplePizzaFactory factory) {
		this.factory = factory;
	}

	Pizza orderPizza(String type) {
		Pizza pizza;
		
		pizza = factory.createPizza(type);

		pizza.prepare();
		pizza.bake();
		pizza.cut();
		pizza.box();
		
		return pizza;
    }
}
```

그러나 위 팩토리는 Simple Factory라고 하는데 사실 디자인 패턴이라고 하기 어렵다 그냥 프로그래밍을 하는데 있어서 자주 쓰이는 관용구에 가깝다고 할수 있다 하지만 자주 사용해서 이러한 간단한 것들도 팩토리 패턴이라고 하는 사람도 있다.

---

이제 피자가게를 여러 분점을 만들었다. 

그래서 PizzaStore를 생성할 때 팩토리 객체에서 리턴한 팩토리 객체를 파라미터로 넘기고, 주문을 하도록 코드를 변경하려고 한다.

```java
NYPizzaFactory nyFactory = new NYPizzaFactory();
PizzaStore nyStore = new PizzaStore(nyFactory);
nyStore.order("Veggie");
```

이런 식으로 말이다 그런데 각 분점 마다 독자적인 방법을 사용하여 각자의 스타일을 만들기 시작 했다.

---

일단, 피자를 만드는 활동 자체는 다시 PizzaStore 클래스에 다시 집어넣어 보자. 그러나 이번에는 이 메서드를 추상 메서드로 선언하고, 각 지역마다 고유의 스타일에 맞게 PizzaStore 의 서브클래스를 만들게 할 것이다.

```java
public abstract class PizzaStore {
    
	public Pizza orderPizza(String type) {
		Pizza pizza;
		
		pizza = createPizza(type);
		pizza.prepare();
		pizza.bake();
		pizza.cut();
		pizza.box();
		
		return pizza;
	}
	
	abstract createPizza(String type);
}
```

이제 각 분점에 대한 서브클래스를 구현해야 한다. 피자의 스타일은 각 서브클래스에서 결정하게 된다. 즉, 어떤 피자를 만들지는 각기 분점에서 결정하게 된다.

---

# 서브클래스에서 결정되는 것

PizzaStore 의 orderPizza() 메서드 내에서 주문 시스템은 잘 갖춰 있다. 이 주문 시스템 자체는 모든 분점에서 동일하게 실행되어야 한다.

즉 피자를 준비하고, 굽고, 자르고 등 모든 과정은 변경할 수 없다. 각 분점에서 결정할 수 있는 것은 피자의 스타일 뿐이다. 이렇게 달라지는 점들은 createPizza() 메소드에 집어 넣고 그 메소드에서 해당 스타일의 피자를 만들도록 할 것이다

```java
public class NYPizzaStore extends PizzaStore {
	
    Pizza createPizza(String item) {
		if (item.equals("cheese")) {
			return new NYStyleCheesePizza();
		} else if (item.equals("veggie")) {
			return new NYStyleVeggiePizza();
		} else if (item.equals("clam")) {
			return new NYStyleClamPizza();
		} else if (item.equals("pepperoni")) {
			return new NYStylePepperoniPizza(); 
		}
	}
}
```

orderPizza() 메소드에서는 Pizza 객체를 가지고 여러 가지 작업을 하지만, Pizza 는 추상 클래스이기 때문에 orderPizza() 에서는 실제로 어디 구상클래스에서 작업을 처리하는지 알 수 없다.

다시 말하면 PizzaStore랑 Pizza는 **객체는 완전히 서로 분리되어 있다**

이렇게 하면 팩토리 메서드는 **객체 생성을 처리하고, 객체를 생성하는 과정을 서브클래스에 캡슐화**시킬 수 있다. 즉, 변하지 않는 부분은 남기고 변하는 부분은 추상 클래스를 상속받아서 만들 수 있게 된다.

## 피자 클래스

이제 피자 클래스를 만들어 보자.

```java
public abstract class Pizza {
	String name;
	String dough;
	String sauce;
	ArrayList toppings = new ArrayList();
	
	void prepare() {
		System.out.println("Preparing " + name);
		System.out.println("Tossing dough...");
		System.out.println("Adding sauce...");
		System.out.println("Adding toppings : ";
		for (int i = 0; i<toppings.size(); i++) {
			System.out.println("  " + toppings.get(i));
		}
	}
	
	void bake() {
		System.out.println("baking");
	}
	
	void cut() {
		System.out.println("cutting");
	}
	
	void box() {
		System.out.println("boxing");
	}
	
	public String getName() {
		return name;
	}
}
```

이하는 구성 서브클래스이다. 각각 피자들은 추상클래스 Pizza 를 상속받게 된다.

```java
public class NYStyleCheesePizza extends Pizza {
    public NYStyleCheesePizza() {
		name = "NY Style Pizza";
		dough = "Thin";
		sauce = "Marinara Sauce";
		toppings.add("toppings");
	}

}
```

이제 피자를 만들어 보자

```java
public class PizzaTestDrive {
    public static void main(String args[]) {
        
        PizzaStore nyStore = new NYStyleCheesePizza();
        PizzaStore chicagoStore = new ChicagoPizzaStore();
        
        Pizza pizza = nyStore.orderPizza("cheese");
        System.out.println("Ethan ordered a " + pizza.getName() + "\n");
        
        pizza = chicagoStore.orderPizza("cheese");
        System.out.println("Joel ordered a " + pizza.getName() + "\n");
    }
}
```

이를 팩토리 메서드 패턴이라고 한다.

# 팩토리 메서드 패턴

팩토리 메서드 패턴에서는 서브클래스에서 어떤 클래스를 만들 것인지 결정하게 함으로써, 객체 생성을 캡슐화한다.

팩토리 메서드 패턴 - 팩토리 매서드 패턴에서는 객체를 생성하기 위한 인터페이스를 정의하는데, 어떤 클래스의 인스턴스를 만들지는 서브클래스에서 결정하게 만듭니다. 팩토리 메서드 패턴을 사용하면, 클래스의 인스턴스를 만드는 일은 서브 클래스에게 맡기는 것이죠.

이 패턴에서는 두 종류의 클래스가 등장하게 된다.

1. 생산자(Creator) 클래스
    - 위의 PizzaStore 가 생산자 클래스로, 나중에 서브클래스에서 제품(객체) 를 생산하기 위해 구현할 팩토리 메소드를 정의한다.
2. 제품(Product) 클래스
    - 팩토리에서는 제품을 생산한다. PizzaStore 에서는 Pizza 를 생산한다.

팩토리 메소드 패턴은 간단한 팩토리와 비슷해 보일 수 있다. 하지만 간단한 팩토리는 일회용 처방에 불과한 반면, 팩토리 메소드 패턴을 이용하면 어떤 구현을 사용할지를 서브클래스에서 결정하는 프레임워크를 만들 수 있다.

예를 들면, 팩토리 메소드에서 orderPizza() 는 피자를 만들기 위한 일반적인 비즈니스 로직을 제공한다. 그 로직에서는 팩토리 메소드를 이용하여, 피자를 만드는 구상 클래스를 만들었다. 즉, PizzaStore 클래스의 서브클래스를 만들 때, 어떤 구상 제품 클래스에서 orderPizza() 에서 리턴할 피자를 만들지 결정하게 된다.

반면에 간단한 팩토리에서는 객체 생성은 캡슐화하지만, 팩토리 메소드 패턴처럼 강력한 유연성을 제공하지는 못한다. 생성하는 제품을 마음대로 변경할 수 없기 때문이다.

# 의존성 뒤집기 원칙이란

팩토리 메서드 패턴을 사용하면 구상 클래스에 대한 의존성을 줄일 수 있다.

```java
public class DependentPizzaStore {
    
    public Pizza createPizza(String style, String type) {
        
        Pizza pizza = null;
       
        if(style.equals("NY")) {
            if(type.equals("cheese")) {
                pizza =	new NYStyleCheesePizza();
            } else if (type.equals("veggie")) {
                pizza = new NYStyleVeggiePizza();
            } else if (type.equals("clam")) {
                pizza = new NYStyleClamPizza();    
            } else if (type.equals("pepperoni")) {
                pizza = new NYStylePepperoniPizza();
            }
        } else if(style.equals("Chicago")) {
            if(type.equals("cheese")) {
                pizza = new ChicagoStyleCheesePizza();            
            } else if (type.equals("veggie")) {
                pizza = new ChicagoStyleVeggiePizza();
            } else if (type.equals("clam")) {
                pizza = new ChicagoStyleClamPizza();    
            } else if (type.equals("pepperoni")) {
                pizza = new ChicagoStylePepperoniPizza();
            }
        } else {
            System.out.println("Error");
            return null;
        }
        
        pizza.prepare();
        pizza.bake();
        pizza.cut();
        pizza.box();
        
        return pizza;
    }
}
```

만약에 팩토리 메서드를 사용하지 않는다면 위 코드 처럼 하나의 생성 객체에서 다수의 구상 클래스에 의존하게 된다.  하나의 생성 메소드에서 여덣 개의 구체적인 피자 클래스에 의존하고 있는데 이는 코드의 확장성을 저해하는 요소가 된다.

위의 코드를 통해서 구상 클래스에 대한 의존성을 줄이는 것이 좋다는 걸 알 수 있다. 이 원칙은 다음과 같이 일반화 할 수 있다.

추상적인 것에 의존하도록 만들어라. 구상 클래스에 의존하도록 만들지 않도록 한다.

이를 **의존성 뒤집기 원칙(Dependency Inversion Principle**) 이라고 한다.

의존성 뒤집기 원칙에 의하면 구상 클래스처럼 구체적인 것이 아닌 추상 클래스나 인터페이스와 같이 추상적인 것에 의존하는 코드를 만들어야 한다.

그러면 앞의 코드는 어떻게 변경될 수 있을까? 펙토리 메서드 패턴을 적용하면 위의 코드에서 구현 클래스에 의존하는 부분이 모두 Pizza 라는 추상 클래스에 의존하게 된다.

## 의존성 뒤집기 원칙에 위배되지 않는 3가지 원칙

1. 어떤 변수에도 구상 클래스에 대한 레퍼런스를 저장하지 않는다.
2. 구상 클래스에서 유도된 클래스를 만들지 않는다.
3. 베이스 클래스에 이미 구현되어 있던 메서드를 오버라이드 하지 않는다.

---

## 다시 피자가게로

최근에 들어온 정보에 따르면 새로운 프레임워크가 도입된 이후로 분점에서 우리가 정한 절차를 잘 따르긴 하는데, 몇몇 분점에서는 자잘한 재료를 더 싼 재료로 바꿔서 원가를 줄이고 마진을 올린다고 한다.

어떻게 하면 분점에서 좋은 재료를 사용하도록 관리할 수 있을까? 원재료를 생산하는 공장을 만들고, 분점까지 그 재료를 배달하도록 하면 될 것이다.

이 계획에도 약간의 문제점이 있는데, 분점이 서로 멀리 떨어져 있고 뉴욕의 레드 소스와 시카고의 레드 소스가 서로 다르다는 점이다. 그래서 뉴욕과 시카고로 배달되는 재료가 서로 달라진다.

그런데, 이제 캘리포니아에도 분점을 열었다. 캘리포니아는 또 다른 재료를 사용한다. 만약에 서울 분점을 열게 되면 또 다른 재료를 사용해야 할 것이다.

이처럼 서로 다른 종류의 재료들을 제공하기 위해 원재료군(families of ingredients)을 처리할 방법을 생각해 봐야 한다.

# 원재료 공장 만들기

이제 원재료를 생산하기 위한 공장을 만들 예정이다. 이 공장에서는 원재료군에 들어있는 각각의 원재료를 생산한다. 우선 지역마다 달라지는 부분을 처리하는 건 나중에 고려하려고 한다.

인터페이스는 다음과 같다.

```java
public interface PizzaIngredientFactory {
    
    Dough createDough();
    Sauce createSauce();
    Cheese createCheese();
    Veggies[] createVeggies();
    Pepperoni createPepperoni();
    Clams createClams();
}
```

이제 위의 인터페이스를 구현한 뉴욕 원재료 공장을 만들어보려고 한다.

```java
public class NYPizzaIngredientFactory implements PizzaIngredientFactory {
    
    public Dough createDough() {
        return new ThinCrustDough();
    }
    
    public Sauce createSauce() {
        return new MarinaraSauce();
    }
    
    public Cheese createCheese() {
        return new ReggianoCheese();
    }
    
    public Veggies[] createVeggies() {
        Veggies [] veggies = {new Garlic(), new Onion(), new Mushroom(), new RedPepper()};
        return veggies;
    }
    
    public Pepperoni createPepperoni() {
        return new SlicedPepperoni();
    } 
    
    public Clams createClam() {
        return new FreshClams();
    }
}
```

### 클래스 변경

이제 팩토리가 준비되었으니 해당 재료를 가지고 피자를 만들 수 있다. 먼저 위의 팩토리 클래스를 사용하여 Pizza 추상 클래스를 변경해 보자

```java
public abstract class Pizza {
    
    String name;
    Dough dough;
    Sauce sauce;
    Veggies [] veggies;
    Cheese cheese;
    Pepperoni pepperoni;
    Clams clams;
    
    abstract void prepare();
    
    void bake() {
        System.out.println("Bake for 25 min at 350");
    }
    
    void cut() {
        System.out.println("cutting");
    }
    
    void box() {
        System.out.println("boxing");
    }
    
    public String getName() {
        return name;
    }
}
```

이제 위의 재료들을 원 공장에서 가져오게 할 것이다. 앞에 소스를 보면, 피자를 만드는 과정은 동일한걸 알수 있다.  피자마다 클래스를 지역별로 따로 만들 필요는 없다.

이제 지역마다 다른 재료를 사용하는 건 원재료 공장에서 커버해 줄 것이기 때문이다.

```java
public class CheesePizza extends Pizza {
    
    PizzaIngredientFactory ingredientFactory;
    
    public CheesePizza(PizzaIngredientFactory ingredientFactory) {
        this.ingredientFactory = ingredientFactory;
    }
    
    void prepare() {
        System.out.println("Preparing " + name);
        dough = ingredientFactory.createDough();
        sauce = ingredientFactory.createSauce();
        cheese = ingredientFactory.createCheese();
    }
}
```

피자 코드에서는 팩토리를 사용하여 피자에 들어갈 재료를 손질한다. 만들어지는 재료는 어떤 팩토리를 주입하냐에 따라 달라지고, 피자 클래스에서는 신경을 전혀 쓰지 않는다. 이제 피자 클래스와 재료는 완전히 분리되었기 때문에 록키 산맥 지역, 북서부 지역, 서울 등 어떤 지역의 팩토리를 사용하든 피자 클래스는 그대로 재사용할 수 있다.

이제 피자 가게를 구현할 것입니다. 피자 가게에서는 각 분점들을 돌면서, 제대로 된 피자를 만드는지 확인하는 코드가 추가된다.

```java
public class NYPizzaStore extends PizzaStore {
    
    protected Pizza createPizza(String item) {
        
        Pizza pizza = null;
        PizzaIngredientFactory ingredientFactory = new NYPizzaIngredientFactory();
        
        if (item.equals("cheese")) {
			pizza = new CheesePizza(ingredientFactory);
			pizza.setName("New York Style Cheese Pizza");
		} else if (item.equals("veggie")) {
			pizza = new VeggiePizza(ingredientFactory);
			pizza.setName("New York Style Veggie Pizza");
		} else if (item.equals("clam")) {
			pizza = new ClamPizza(ingredientFactory);
			pizza.setName("New York Style Clam Pizza");
		} else if (item.equals("pepperoni")) {
			pizza = new PepperoniPizza(ingredientFactory); 
			pizza.setName("New York Style Pepperoni Pizza");
		}
		
		return pizza;
	}
}
```

# 피자를 주문하는 흐름

위의 과정을 정리해보자. 피자를 주문한다고 가정하자.

1) A는 뉴욕풍 피자를 주문한다 , 2) B는 시카고풍 피자를 주문한다.

다음과 같은 코드로 주문이 가능할 것이다.

```java
class Order {
    public void orderPizza() {
        PizzaStore nyPizzaStore = new NYPizzaStore();
        nyPizzaStore.orderPizza("cheese");
    }
    
}
```

피자를 주문하게 되면, 피자가게를 생성한 후 주문을 시도한다. 그러면 내부에서는 orderPizza 메소드에서 createPizza() 메소드를 호출하게 된다. 

```java
public abstract class PizzaStore {
    
	public Pizza orderPizza(String type) {
		Pizza pizza;
		
		pizza = createPizza(type);
		
		pizza.prepare();
		pizza.bake();
		pizza.cut();
		pizza.box();
		
		return pizza;
	}
	
	abstract createPizza(String type); 
}
```

createPizza() 메서드가 호출되면, 원재료 공장이 동작하기 시작한다.

```java
Pizza pizza = new CheesePizza(nyIngredientFactory);
```

원재료 공장은 각 피자의 생성자를 통해 전달되게 된다. 그 이후에 pizza의 prepare() 메서드가 호출되면, 팩토리에서 원재료를 가져오게 된다.

이후에 orderPizza 메소드에서는 피자를 굽고, 자르고, 포장한다.

# 추상 팩토리

추상 팩토리로 서로 다른 피자에서 필요로 하는 원재료군을 생산하기 위한 방법을 만들었다.

추상 팩토리 패턴에서는 인터페이스를 이용하여 서로 연관된, 또는 의존하는 객체를 구상 클래스를 지정하지 않고도 생성할 수 있다.

추상 팩토리 패턴을 사용하면 클라이언트에서 추상 인터페이스를 통해서 일련의 로직들을 공급받을 수 있다. 실제로 어떤 제품이 생산되는지는 전혀 알 필요도 없다.

그렇기 때문에 클라이언트와 팩토리에서 생산되는 제품의 의존성이 약해진다.