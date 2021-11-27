# Iterator pattern

## Iterator Pattern 이란?

> 이터레이터 패턴(Iterator Pattern: 반복자 패턴)은 컬렉션 구현 방법을 노출시키지 않으면서도 그 집합체 안에 들어있는 모든 항목에 접근할 수 있게 해주는 방법을 제공해준다.
> 
> 
> 이 패턴을 이용하면 집합체 내에서 어떤 식으로 일이 처리되는지에 대해서 전혀 모르는 상태에서 그 안에 들어있는 모든 항목들에 대해 반복작업을 수행할 수 있다.
> 
> 또한, 이터레이터 패턴을 사용하면 모든 항목에 일일이 접근하는 작업을 컬렉션 객체가 아닌 반복자 객체에서 맡게된다는 것이 중요한 점인데, 이는 집합체의 인터페이스 및 구현이 간단해질 뿐 아니라, 집합체에서는 반복작업에서 손을 떼고 원래 자신이 할 일(객체 컬렉션 관리)에만 전념할 수 있게 한다.
> 

![https://blog.kakaocdn.net/dn/ARh75/btqAy2u00m3/Lum3YjmKrA5iJy4VIcwPZ0/img.png](https://blog.kakaocdn.net/dn/ARh75/btqAy2u00m3/Lum3YjmKrA5iJy4VIcwPZ0/img.png)

- ConcreteAggregate에는 객체 컬렉션이 들어있으며, 그 안에 들어있는 컬렉션에 대한 Iterator를 리턴하는 메소드를 구현한다.
- 모든 ConcreteAggregate는 그 안에 있는 객체 컬렉션에 대해 돌아가면서 반복 작업을 처리할 수 있게 해주는 ConcreteIterator의 인스턴스를 만들 수 있어야 한다.
- ConcreteIterator에서는 반복작업 중에 현재 위치를 관리한다.
- Iterator인터페이스에서는 모든 반복자에서 구현해야 하는 인터페이스를 제공하며, 컬렉션에 들어있는 원소들에 돌아가면서 접근할 수 있게 해주는 메소드들을 제공한다.

## 팬케이크 가게와 식당의 합병

```java
public class PancakeMenu{    // 팬케이크 가게의 메뉴 구현 방식
    // ...
  public ArrayList getMenuItems(){
    return menuItems;
  }
  // ...
}
```

```java
public class DinerMenu{    // 식당의 메뉴 구현 방식
    // ...
  public MenuItem[] getMenuItems(){
    return menuItems;
  }
  // ...
}
```

```java
public class Waitress {   
    // ...
  for (int i = 0; i < breakfastItems.size(); i++){
    MenuItem menuItem = (MenuItem) breakfastItems.get(i);
    //...
  }

  for (int i = 0; i < lunchItems.length; i++){
    MenuItem menuItem = lunchItem[i];
    // ...
  }
  // ...
}
```

> 두 가게가 합쳐지면서 메뉴 구현 방식 통합이 필요해졌다.
> 
> 
> 팬케이크 가게에서는 ArrayList를, 식당에서는 배열을 사용하여 메뉴를 구현하고 있다.
> 
> 메뉴 항목이 서로 다른 식으로 구현되어 있기에, 두 개의 서로 다른 순환문을 만들어야 한다.
> 

## 해결 방법

![https://blog.kakaocdn.net/dn/dNlPfL/btq21CXI9YF/Bg9rRAHTg1TO3Yidpy12Gk/img.png](https://blog.kakaocdn.net/dn/dNlPfL/btq21CXI9YF/Bg9rRAHTg1TO3Yidpy12Gk/img.png)

```java
public interface Iterator {
    boolean hasNext();
    MenuItem next();
}
```

```java
public class DinerMenuIterator implements Iterator {
    MenuItem[] items;
    int position = 0;

    public DinerMenuIterator(MenuItem[] items) {
        this.items = items;
    }

    public MenuItem next() {
        MenuItem menuItem = items[position];
        position = position + 1;
        return menuItem;
    }

    public boolean hasNext() {
        if (position >= items.length || items[position] == null) {
            return false;
        } else {
            return true;
        }
    }
}
```

```java
public class DinerMenu{
  //생성자 , 메소드 호출

  // getmenuItems는 내부 구조를 다 드러내는 단점이 있어서 필요 없음
  //public MenuItem[] getMenuItems(){
  //  return menuItems;
  //}

  public Iterator createIterator(){
    return new DinerMenuIterator(menuItems);
  }
}
```

```java
public class Waitress{
  // ...
	public void printMenu() {
		Iterator pancakeIterator = pancakeHouseMenu.createIterator();
		Iterator dinerIterator = dinerMenu.createIterator();
		//...
		printMenu(pancakeIteraotr);
		printMenu(dinerIterator);
	}

  private void printMenu(Iterator iterator){
    while (iterator.hasNext()){
        MenuItem menuItem = (MenuItem) iterator.next();
    }
  }
  // ...
}
```

```java
public class MenuTestDrive {
	public static void main(string args[]) {
		PancakeHouseMenu pancakeHouseMenu = new PancakeHouseMenu();
		DinerMenu dinerMenu = new DinerMenu();
		
		Waitress waitress = new Waitress(pancakeHouseMenu, dinerMenu);

		waitress.printMenu();
	}
}
```

- createIterator() 메소드는 menuItems 배열을 가지고 DinerMenuIterator를 생성한 다음 클라이언트한테 리턴한다.
- PancakeMenu와 DinerMenu에서는 createIterator() 메소드를 구현한다. 이 메소드에서는 각 메뉴 항목에 대한 반복자를 만들어주는 역할을 한다.
- PancakeMenuIterator와 DinerMenuIterator는 Iterator인터페이스를 구현한다.
- Iterator덕분에 Waitress 클래스가 실제로 구현된 구상 클래스로부터 분리될 수 있다. Iterator를 받아서 컬렉션에 들어있는 모든 객체를 사용하기 때문에 Menu가 무엇으로 구성되었는지는 알 필요가 없다.
- 반복자를 이용하면, 컬렉션 입장에서는 그 안에 들어있는 모든 항목에 접근할 수 있게 하기 위해서 여러 메소드를 외부에 노출시키지 않으면서도, 컬렉션에 들어있는 모든 객체에 접근할 수 있다. 이는 반복자를 구현한 코드를 컬렉션 밖으로 끄집어낼 수 있다는 장점이 있다.
- PancakeMenu와 DinerMenu 두 메뉴에서는 똑같은 메소드들을 제공하지만 아직 같은 인터페이스를 구현하고 있진 않는다.

## 인터페이스를 개선하기

> 위에서는 자바 내장 인터페이스 Iterator 를 사용한 것이 아니다.
> 
> 
> 반복자를 어떤 식으로 만드는지 알았으니 이번엔 자바에 있는 Iterator 인터페이스를 사용해보자.
> 
> 자바의 Iterator 인터페이스는 hasNext(), next(), remove() 메소드를 가지고 있으므로 이들을 모두 구현해주어야 한다.
> 

![Iterator](https://user-images.githubusercontent.com/46774431/142768358-cc98edae-247c-4215-b07d-c9b83a54b512.PNG)

ArrayList 를 사용하는 PancakeMenu 는 별도의 Iterator가 필요하지 않다.

ArrayList 에 반복자를 리턴해주는 iterator()라는 메소드가 있기 때문이다.

하지만 배열을 사용하는 DinerMenu 는 자바의 Iterator 인터페이스를 사용한다 할지라도 별도의 Iterator가 필요하다.

```java
public class PancakeMenu {
    // ...

  // ArrayList의 iterator() 를 활용, 더 이상 별도의 Iterator 클래스가 필요없다.
  public Iterator createIterator(){
    return menuItems.iterator();
  }
  // ...
}
```

```java
public class DinerMenuIterator implements Iterator {
    // ...
    public MenuItem next() {
        // ...
    }
    public boolean hasNext() {
        // ...
    }
  public boolean remove(){
		// Iterator 인터페이스의 remove를 오버라이드 해줘야한다. 고정된 배열을 사용하기 때문에 
		// remove가 호출 되면 모든 항목을 한칸씩 왼쪽으로 밀어줘야 하기 때문
		if(position <= 0)
			throw new IllegalStateException
		if( list[position - 1 ] != null) {
				for(int i = position - 1; i < (list.length -1); i++) {
						list[i] = list[i+1];
				}
				list[list.length-1] = null;
		}
  }
}
```

## 메뉴 추가는 주방장이, 웨이트리스는 그냥 메뉴만

> Waitress 클래스에서 각 메뉴 객체를 참조할 때는 구상 클래스 대신 인터페이스를 이용하도록 하면 특정 구현이 아닌 인터페이스에 맞춰서 프로그래밍한다는 원칙을 따르게 되므로 Waitress와 구상 클래스 사이의 의존성을 줄일 수 있다.
> 

```java
import java.util.Iterator;

public interface Menu {
    public Iterator<MenuItem> createIterator();
}
```

![https://blog.kakaocdn.net/dn/tqnVN/btq26ET2xDY/EkuEUdbwyyIKG1dxhPc9g1/img.png](https://blog.kakaocdn.net/dn/tqnVN/btq26ET2xDY/EkuEUdbwyyIKG1dxhPc9g1/img.png)

- Waitress 클래스는 Menu와 Iterator만 신경쓰면 된다.
- DinerMenu와 PancakeMenu는 Menu인터페이스를 구현하면 되고, 각 이터레이터 클래스들은 메뉴 항목에 대한 반복자를 생성하기 위해 Iterator인터페이스를 구현하면 된다.
- 여기서는 java.util 에서 제공하는 ArrayList.iterator() 반복자를 사용하므로 PancakeMenuIterator클래스는 더이상 필요가 없다.
- 하지만 배열에 대한 반복자는 자바에서 제공하지 않기 때문에, DinerMenuIterator클래스는 여전히 필요하다.

# 단일 역할 원칙

클래스를 바꾸는 이유는 단 한 가지 뿐이어야 한다는 원칙이다.

컬렉션의 역할은 집합체를 관리하는 역할이다. 그런데 이 안에 반복자를 처리하는 다른 역할을 부여하게 되면

추후에 이 클래스를 바꾸어야 하는 이유가 두 가지가 된다.

첫 번째는 컬렉션이 어떤 이유로 인해 바뀌었을 때, 두 번째는 반복자 관련 기능이 바뀌었을 때이다.

이터레이터 패턴을 사용하면 컬렉션으로부터 반복자 작업을 캡슐화시킬 수 있다.

이러한 이유로 인해 "변경"과 관련된 단일 역할 원칙이 이터레이터 패턴에 등장한 것 같다.

## **응집도(cohesion)**

응집도란 클래스 또는 모듈이 특정 목적 또는 역할을 얼마나 일관되게 지원하는지를 나타내는 척도다.

어떤 모듈 또는 클래스의 응집도가 높다는 것은 일련의 서로 연관된 기능이 묶여있다는 것을,

응집도가 낮다는 것은 서로 상관 없는 기능들이 묶여있다는 것을 의미한다.

※ 단일 역할 원칙을 잘 따르는 클래스는 두 개 이상의 역할을 맡고 있는 클래스에 비해 응집도가 높고, 관리하기에 더 용이한 편이다.
