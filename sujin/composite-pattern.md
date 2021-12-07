# 9-2. 컴포지트 패턴

<aside>
💡 객체들을 트리 구조로 구성하여 부분과 전체를 나타내는 계층구조로 만들수 있다. 클라이언트에서 개별 객체와 다른 객체들오 구성된 복합 객체(composite)를 똑같은 방법으로 다룰 수 있다.

</aside>

식당 메뉴를 예로들어 생각해본다면 중첩되어 있는 메뉴 그룹과 메뉴 항목을 똑같은 구조 내에서 처리할수 있게끔 하는 것이다. 메뉴와 메뉴항목을 같은 구조에 집어넣어서 부분-전체 계층구조를 생성할수 있다. 이런 복합구조를 사용하면 복합 객체와 개별 객체에 대해 구분없이 똑같은 작업을 적용할 수 있다.

컴포지트 패턴 클래스 다이어그램

![262FAC4F57839ACC18.png](./assets/composite/262FAC4F57839ACC18.png)

컴포지트 패턴을 메뉴에 적용시켜본다면 우선 구성요소 인터페이스를 만드는 것부터 시작해보자.

이 인터페이스는 메뉴와 메뉴 항목 모두에 적용되는 공통 인터페이스 역할을 하며, 이 인터페이스가 있어야만 그 들을 똑같은 방법으로 처리할 수 있다. 즉 메뉴와 메뉴 항목에 대해서 같은 메소드를 호출 할 수 있게 된다.

![26090D4957839C9D31.png](./assets/composite/26090D4957839C9D31.png)

```jsx
public abstract class Menucomponent {

	public void add(MenuComponent menuComponent) {
	
		throw new UnsupportedOperationException();
		
	}

	public void remove(MenuComponent menuComponent) {
	
		throw new UnsupportedOperationException();
		
	}

	public MenuComponent getChild(int i) {
	
		throw new UnsupportedOperationException();
		
	}
	

	public String getName(){
	
		throw new UnsupportedOperationException();
		
	}

	public String getDescription(){
	
		throw new UnsupportedOperationException();
		
	}
	
	public double getPrice(){
	
		throw new UnsupportedOperationException();
		
	}
	
	public boolean isVegetarian(){
	
		throw new UnsupportedOperationException();
		
	}
	
	public void print(){
	
		throw new UnsupportedOperationException();
		
	}

}
```

```jsx
public class MenuItem extends Menucomponent {

	String name;
	
	String description;
	
	boolean vegetarian;
	
	double price;

public MenuItem(String name, String description, boolean vegetarian, double price) {

	this.name = name;
	
	this.description = description;
	
	this.vegetarian = vegetarian;
	
	this.price = price;

}

public String getName(){

	return name;
	
}

public String getDescription() {

	return description;
	
}

public double getPrice() {

	return price;
	
}

public boolean isVegetarian() {

	return vegetarian;
	
}

public void print() {

	System.out.println(getName());
	
	if(isVegetarian()) System.out.println("(v)");
	
		System.out.println(getPrice());
		
		System.out.println(getDescription());
	
	}

}

```

```jsx
public class Menu extends Menucomponent {

	ArrayList<Menucomponent> menuComponents = new ArrayList();
	
	String name;
	
	String description;
	

	public Menu(String name, String description) {
	
		this.name = name;
		
		this.description = description;
	
	}
	
	
	public void add(MenuComponent menuComponent) {
	
		menuComponents.add(menuComponent);
		
	}

	public void remove(MenuComponent menuComponent){
	
		menuComponents.remove(menuComponent);
		
	}
	

	public MenuComponent getChild(int i){
	
		return menuComponents.get(i);
		
	}

	public String getName(){
	
		return name;
		
	}
	

	public String getDescription() {
	
		return description;
		
	}

	public void print() {
	
		System.out.println(getName());
		
		System.out.println(getDescription());
		
		System.out.println("----------------------------------------------------");
		
		Iterator<MenuComponent> iterator = menuComponents.iterator();    //Menu 정보 뿐아니라 Menu안의 아이템까지 출력
		
		while(iterator.hasNext()){
		
			MenuComponent menuComponent = iterator.next();
			
			menuComponent.print();
		
		}
	
	}

}

```

```jsx
public class Waitress {

	MenuComponent  allMenus;
	
	public Waitress(menuComponent allMenus) {
	
		this.allMenus = allMenus;

	}

	public void printMenu(){
	
		allMenus.print();
		
		}
	
}

```

```jsx
public class MenuTestDrive {

	public static void main(String args[]){
	
		MenuComponent pancakeHouseMenu = new Menu("펜케이크 하우스 메뉴", "아침 메뉴");
		
		MenuComponent dinerMenu = new Menu("객체마을 식당 메뉴", "점심 메뉴");
		
		MenuComponent cafeMenu = new Menu("카페 메뉴", "저녁 메뉴");
		
		MenuComponent dessertMenu = new Menu("디저트 메뉴", "디저트를 즐겨 보세요");
		
		
		
		MenuComponent allMenus = new Menu("전체 메뉴", "전체 메뉴");
		
		
		
		allMenus.add(pancakeHouseMenu);
		
		allMenus.add(dinerMenu);
		
		allMenus.add(cafeMenu);
		
		
		
		dinerMenu.add(new menuItem("파스타", "마리나라 소스 스파게티.", true, 3.89));
		
		dinerMenu.add(dessertMenu);
		
		dessertMenu.add(new menuItem("애플 파이", "바삭바삭한 크러스트에 바닐라아이스크림이", true, 1.59));
		
		
		
		Waitress waitress = new Waitress(allMenus);
		
		waitress.printMenu();
	
	}
	
}

```

한 클래스에서 한 역할만 맡아야 한다고 했는데 여기서는 계층구조를 관리하는 일하고 메뉴관련 작업을 처리하는 일 두개를 처리해야한다. 컴포지트 패턴에서는 단일 역할 원칙을 깨면서 대신에 투명성을 확보하기 위한 패턴이다.

컴포지트 패턴 내에서 이터레이터 패턴을 활용해보자.

```jsx
public class Menu extends MenuComponent {

	//나머지 코드는 그대로
	
	public Iterator<MenuComponent> createIterator() {
	
		return new CompositeIterator(menucomponents.iterator());
		
	}

}

```

```jsx
public class MenuItem extends MenuComponent {

	//나머지 코드는 그대로
	
	public Iterator<MenuComponent> createIterator() {
	
		return new NullIterator() //널 반복자.
		
	}

}
```

MenuItem에 대해서는 반복작업을 할 대상이없다. 그래서 createIterator()메소드를 구현하기가 애매해진다.

첫번째 방법 : 그냥 null을 리턴한다. 그렇다면 클라이언트에서 리턴값이 널인지 아닌지를 판단하기 위해 조건문을 써야하는 단점이있다.

두번째 방법 : hasNext()가 호출되었을 때 무조건 false 를 리턴하는 반복자를 리턴한다.

두번째 방법은 여전히 반복자를 리턴할수 있기 때문에 클라이언트에서는 리턴된 객체가 널 객체인지에 대해 신경 쓸 필요가 없다.

이렇게 아무일도 하지 않는 반복자를 NullIterator라고 부른다.

```jsx
public class NullIterator implements Iterator<Object>{

	@Override

	public boolean hasNext() {

		return false;

	}

	@Override

	public Object next() {

		return null;

	}

  @Override

   public void remove() {

     throw new UnsupportedOperationException();

  }

}

```

```jsx
public class CompositeIterator implements Iterator {

	Stack<Iterator<MenuComponent>> stack = new Stack();
	
	
	public CompositeIterator(Iterator iterator){
	
		stack.push(iterator);
	
	}
	
	public MenuComponent next() {
	
		Iterator<MenuComponent> iterator = stack.peek();
		
		MenuComponent component = iterator.next(); 
		
		if(component instanceof Menu) stack.push(((Menu)component).iterator());
		
		return component;
	
	}
	
	
	public boolean hasNext() {
	
		if(stack.empty() return false;
		
		Iterator<MenuComponent> iterator = stack.peek();
		
		if(!iterator.hasNext()){
		
			stack.pop();
			
			return hasNext();
		
		} else {
		
			return true;
			
		}
	
	}
	
	
	public void remove() {
	
		throw new UnsupporttedOperationException();
		
	}
	
}
```

```jsx
public class Waitress {

	MenuComponent allMenus;
	
	public Waitress(MenuComponent allMenus) {
	
		this.allMenus = allMenus;
		
	}
	
	
	public void printMenu() {
	
		allMenus.print();
		
	}

	public void printVegetarianMenu(){
	
		Iterator<MenuComponent> iterator = allMenus.createIterator();
		
		while(iterator.hasNext()){
		
		Menucomponent menuComponent = iterator.next();
	
		try{
			
			if(menuComponent.isVegetarian()) menuComponent.print();
			
		} catch(UnsupportedOpoerationException e) {}
		
		}
		
	}

}

출처: https://jusungpark.tistory.com/26?category=630296 [정리정리정리]
```

try{

if(menuComponent.isVegetarian()) menuComponent.print();

}catch(UnsupportedOpoerationException e) {}

위와 같이 try/catch 코드를 쓴이유는 instanceof 를 써서 실행중에 형식을 확인하여 isVegetarian()을 호출할 수도 있지만 그렇게하면 Menu와 MenuItem을 똑같이 다루지 않게 되는 셈이되어 투명성을 잃어버리게 된다. Menu 의 isVegetarian() 메소드에서 무조건 false를 리턴하도록 하는 방법도 있다. 이렇게하면 코드도 간단해지고 투명성도 계속 유지할수 있다. 하지만 위 예제에서는 Menu에서는 그 메소드가 지원되지 않는다는 것을 분명하게 나타내기 위해서 사용되었다.

---

출처

[https://jusungpark.tistory.com/26?category=630296](https://jusungpark.tistory.com/26?category=630296)

[정리정리정리]
