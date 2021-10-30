# Observer Pattern

## **옵저버 패턴 (Observer Pattern)**

한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체들한테 연락이 가고 자동으로 내용이 갱신되는 방식으로

일대다(one-to-many) 의존성을 정의한다.

옵저버 패턴을 구현하는 방법에는 여러가지가 있지만 대부분 상태를 저장하고있는 주제 인터페이스를 구현한 하나의 **주제객체**와 주제객체에 의존하고있는 옵저버 인터페이스를 구현한 여러개의 **옵저버객체** 가 있는 디자인을 바탕으로 한다.

데이터 전달방식은 2가지가 있다.

**주제객체에서 옵저버로 데이터를 보내는 방식 (푸시 방식)**

**옵저버에서 주제객체의 데이터를 가져가는 방식 (풀 방식)**

## **디자인 원칙.**

서로 상호작용을 하는 객체 사이에서는 가능하면 느슨하게 결합하는 디자인을 사용해야 한다. [(**Loose Coupling)**](https://swk3169.tistory.com/185?category=711222)

옵저버 패턴은 주제와 옵저버가 **느슨하게 결합**되어있는 객체 디자인을 제공.

주제가 옵저버에 대해서 아는 것은 옵저버가 특정 인터페이스(Observer 인터페이스)를 구현 한다는 것 뿐.

옵저버는 언제든지 새로 추가할 수 있음. (주제는 Observer 인터페이스를 구현하는 객체 목록에만 의존하기때문)

새로운 형식의 옵저버를 추가하려해도 주제를 전혀 변경할 필요가 없음. (새로운 클래스에서 Observer 인터페이스만 구현해주면 됨)

주제나 옵저버가 바뀌더라도 서로에게 전혀 영향을 주지않음. 그래서 주제와 옵저버는 서로 독립적으로 재사용할 수 있음.

느슨하게 결합하는 디자인을 사용하면 변경 사항이 생겨도 무난히 처리할 수 있는 유연한 객체지향 시스템을 구축할 수 있다. (객체 사이의 상호 의존성을 최소화 할 수 있기 때문)

### **문제의 시작**

날씨 데이터를 가지고 있는 회사와 데이터를 연동하여 여러 종류의 각각의 디스플레이에 날씨 데이터를 출력해줘야 하는 업무가 생겼다고 가정했을때.

제공받은 객체와 각 메소드의 역할.

getTemperature() : 온도

getHumidity() : 습도

getPressure() : 기압

measurementsChanged() : 새로운 기상 측정 데이터가 나올때마다 자동으로 호출되는 부분.

**대강 구현해 본다면..**

measurementsChanged 메소드 안 Display update 메소드들이 구체적인 구현에 맞춰서 코딩이 되었기 때문에, 프로그램을 고치지 않고는 다른 디스플레이 항목을 추가/제거할수 없다. 향후에 바뀔수있는 부분은 캡슐화해서 분리하여야 한다.

효과적으로 모든 디스플레이들에게 Weather의 상태를 알려줄 수 있는 방법이 필요하다.

```java
public class WeatherData {

public void measurementsChanged() {

		float temp = getTemperature();
		
		float humidity = getHumidity();
		
		float pressure = getPressure();

   currentCondirionsDisplay.update(temp, humidity, pressure); // 디스플레이 갱신

   statisticsDisplay.update(temp, humidity, pressure); // 디스플레이 갱신

   forecastDisplay.update(temp, humidity, pressure); // 디스플레이 갱신

   }

     // 기타 메소드
}
```

**해결해보자.**

**1.옵저버 패턴 구현해보기 (푸시 방식 사용)**

![https://blog.kakaocdn.net/dn/bA9CKW/btqDaVg5Nrg/kmPeUcs7qWjEFkuWvNbHu0/img.jpg](https://blog.kakaocdn.net/dn/bA9CKW/btqDaVg5Nrg/kmPeUcs7qWjEFkuWvNbHu0/img.jpg)

```java
public interface Subject {

	voidregisterobserver(Observer observer);
	
	voidremoveObserver(Observer observer);
	
	voidnotifyObservers();

 }

public interface Observer {

	voidupdate(float temp,float humidity,float pressure);

 }

public interface DisplayElement {

		voiddisplay();

 }
```

```java
public class WeatherData implements Subject {

		private List<Observer> observers;
		
		private float temperature;
		
		private float humidity;
		
		private float pressure;
		
	public WeatherData() {

		this.observers =new ArrayList<>();

	}

	public void measurementsChanged() {
				this.notifyObservers();
	}
	// 값이 세팅된다고 가정.
public void setMeasurementsChanged(float t,float h,float p) {

			this.temperature = t;
			
			this.humidity = h;
			
			this.pressure = p;
			
			this.measurementsChanged();

	}

@Override
public void notifyObservers() {

	for (Observer observer : observers) {

			observer.update(this.temperature,this.humidity,this.pressure);

		}

}

@Override
public void registerobserver(Observer observer) {

			this.observers.add(observer);

}

@Override
public void removeObserver(Observer observer) {
	if (observers.contains(observer))
				observers.remove(observer);
		}
}
```

```java
public class CurrentConditions implements Observer,DisplayElement {

private float temperature;

private float humidity;

private Subject weatherData;

publicCurrentConditions(Subject weatherData) {

		this.weatherData = weatherData;
		
		this.weatherData.registerobserver(this); // 옵저버 등록

}

@Override
public void display() {
		System.out.println("Current conditions : " + temperature + " , " + humidity);
}

@Override
public void update(float temp,float humidity,float pressure) {
		this.temperature = temp;
		
		this.humidity = humidity;
		
		this.display();
	}
}
```

```java
public class WeatherStation {

public static void main(String[] args) {

		WeatherData weatherData =new WeatherData();

		CurrentConditions currentConditions =new CurrentConditions(weatherData);

		StatisticsDisplay statisticsDisplay =new StatisticsDisplay(weatherData);

		ForecastDisplay forecastDisplay =new ForecastDisplay(weatherData);

		weatherData.setMeasurementsChanged(85, 62, 36.7f);

	}

}
```

**2.자바 내장 옵저버 패턴 사용**

java.util.Observer 인터페이스와 java.util.Observable 클래스를 사용할수 있음.

자바 내장 옵저버 패턴은 푸시 방식, 풀 방식 모두 사용가능.

자바 내장 옵저버 패턴 클래스 다이어그램

![https://blog.kakaocdn.net/dn/bzmRrt/btqC9Opgdtj/17BcJ57s4DPs940WHLw1qK/img.jpg](https://blog.kakaocdn.net/dn/bzmRrt/btqC9Opgdtj/17BcJ57s4DPs940WHLw1qK/img.jpg)

이전에 구현 했던 것과 마찬가지로 java.util.Observer 인터페이스를 구현하고 java.util.Observable 객체의 addObserver() 메소드를 호출하면 옵저버 목록에 추가가 되고 deleteObserver()를 호출하면 옵저버 목록에서 탈퇴가 된다.

연락을 돌리는 방법은 java.util.Observable를 상속받는 주제 클래스에서 setChanged() 메소드를 호출해서 객체의 상태가 바뀌었다는 것을 알린후 notifyObservers() 또는 notifyObserver(Object arg) 메소드를 호출하면 된다. (인자값을 넣어주는 메소드는 푸시방식으로 쓰임.)

옵저버 객체가 연락을 받는 방법은 update(Observable o, Object arg) 메소드를 구현하기만 하면된다. Observable o 에는 연락을 보내는 주제 객체가 인자로 전달이 되고 Object arg 에는 notifyObservers(Object arg) 메소드에서 인자로 전달된 데이터 객체가 넘어온다. (데이터 객체가 지정되지 않은경우 null)

```java
public class WeatherData extends Observable {

	private float temperature;
	
	private float humidity;
	
	private float pressure;
	
	public void measurementsChanged() {
	
	this.setChanged(); // 상태가 바뀌었다는 플래그값을 바꿔줌.
	
	this.notifyObservers(); // 풀 방식을 사용해서 알림

	}
	// 값이 세팅된다고 가정
	public void setMeasurementsChanged(float t,float h,float p) {

		this.temperature = t;
		
		this.humidity = h;
		
		this.pressure = p;
		
		this.measurementsChanged();

	}

	public float getTemperature() {
		return temperature;
	}
	
	publicfloatgetHumidity() {
		return humidity;
	}

	publicfloatgetPressure() {

		return pressure;

	}
}
```

```java
public class CurrentConditions implements Observer,DisplayElement {

private Observable observable;

private float temperature;

private float humidity;

public CurrentConditions(Observable observable) {

		this.observable = observable;
		
		this.observable.addObserver(this);

	}

	@Override

public void display() {

		System.out.println("Current conditions : " + temperature + " , " + humidity);

	}

	@Override
	public void update(Observable o, Object arg) {

if (oinstanceof WeatherData) {

			WeatherData weatherData = (WeatherData) o;

			this.temperature = weatherData.getTemperature();
			
			this.humidity = weatherData.getHumidity();
			
			this.display();

		}

	}

}
```

**java.util.Observable 의 단점.**

첫째. Observable 은 클래스이기 때문에 서브클래스를 만들어야 한다. 이미 다른 수퍼클래스를 확장하고 있는 클래스에 Observable의 기능을 추가할 수가 없어서 재사용성에 제약이 생긴다.

둘째. Observable 인터페이스라는 것이 없기 때문에 자바에 내장된 Observer API 하고 잘 맞는 클래스를 직접 구현하는 것이 불가능하다.

java.util.Observable을 확장한 클래스를 쓸 수 있는 상황이면 Observable API를 쓰는 것도 괜찮지만 상황에 따라 직접 구현해야 할 수도 있다.

둘 중 어떤 방법을 쓰든 옵저버 패턴만 제대로 알고 있다면 그 패턴을 활용하는 API는 어떤 것이든 잘 활용할 수 있다.