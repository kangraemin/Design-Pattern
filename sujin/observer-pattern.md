# 옵저버 패턴

객체들에게 연락망을 돌립시다.

### 기상 모니터링 애플리케이션 개요

시스템은 기상 스테이션과 WeatherData 객체, 그리고 사용자에게 현재 기상 조건을 보여주는 디스플레이 이렇게 세요소로 이루어집니다.

### WeatherDate 클래스

getTemperature, getHumidity, measurementsChanged가 있습니다.

### 지금 알고 있는 것들

Weather-O-Rama에서 준 규격은 그리 분명하지가 않습니다. 하지만 우엇을 할지는 확실하게 알고 넘어가야합니다. 지금 우리가 알고 있는 것들을 한번 정리해보죠.

- 새로운 기상 데이터가 나올 때마다 measurementsChanged 매소드가 호출됩니다.
- 기상데이터를 사용하는 세개의 디스플레이 항목을 구현해야 합니다. 하나는 현재 조건을 표시하는 것이고, 다른 하나는 기상 통계를 표시하는 것이고 나머지 하나는 기상 예볼르 표시하는 것이죠. WeatherData에서 새로운 측정값이 들어올 때마다 디스플레이를 갱신해야합니다.
- 시스템이 확장 가능해야합니다.

### 기상 스테이션용 코드를 대강 다음과 같이 만들어보았습니다.

```jsx
public class WeatherData {
	public void measurementsChanged() {
		float temp = getTemperature();
		float humidity = getHumidity();
		...
		currentConditionDisplay.update(temp, humidity, pressure);
		statisticsDisplay.update(temp, humidity, pressure);
	}
}
```

### 아까 구현한 코드에 무슨 문제가?

배웠던 원칙을 살펴보죠.

```jsx
		currentConditionDisplay.update(temp, humidity, pressure);
		statisticsDisplay.update(temp, humidity, pressure);
```

이 부분에서 구체적인 구현에 맞춰서 코딩했기 때문에 프로그램을 고치지 않고는 다른 디스플레이 항목을 추가/제거 하지 않고는 다른 디스플레이 항목을 추가/제거 할수 없습니다.

### 옵저버 패턴을 만나봅시다.

신문사 이야기를 예시로 들면 구독하고 싶을 때 구독하거나, 구독을 끊고 싶을 때 신청을하죠. 그것과 비슷한게 옶저버 패턴입니다.

### 출판사 + 구독자 = 옵저버 패턴

신문 구독 매커니즘만 제대로 이해할 수 있다면 옵저버 패턴을 쉽게 이해할수 있습니다. 출판사를 주제(subject) 구독자를 옵저버로 불러보죠.

### 옵저버 패턴의 정의

옵저버 패턴에선 한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체들한테 자동으로 연락이 가고 자동으로 내용이 갱신되는 방식으로 일때다 의존성을 정의합니다.

### 느슨한 결합의 위력

두 객체가 느슨하게 결합되어 있다는 것은 그 둘이 상호작용을 하긴 하지만 서로에 대해 서로 잘 모른다는 것을 의미합니다.

옵저버 패턴에서는 주제와 옵저버가 느슨하게 결합되어 있는 객체 디자인을 제공합니다.

- 주제가 옵저버에 대해서 아는 것은 특정 인터페이스를 구현한다는 것 뿐입니다.
- 옵저버는 언제든지 새로 추가할수 있습니다.
- 새로운 형식의 옵저버를 추가하려고 할 때도 주제를 전혀 변경할 필요가 없습니다.
- 주제와 옵저버는 서로 독립ㅂ적으로 재사용할 수 있습니다.
- 주제나 옵저버가 바뀌더라도 서로한테 영향을 미치지는 않습니다.

### 기상 스테이션 설계

```jsx
public interface Subject {
	void registerObserver(Observer o);
	void removeObserver(Observer o);
	void notifyObservers(); // 상태가 변경된 후, 옵저버들에게 알리기 위해 호출하는 메서드
}

public interface Observer {
    void update(float temp, float humidity, float pressure); // 파라미터는 기상 정보 변경시 옵저버들에게 전달되는 상태
}

public interface DisplayElement {
    void display(); // 디스플레이 항목을 화면에 표시해야 하는 경우, 매서드 호출
}
```

### WeatherData에서 Subject 인터페이스 구현하기

```jsx
/**
* WeatherData 는 Subject 를 구현. 안에 Observer 리스트를 가지고 있으면서,
  데이터의 변동이 생기면 observer에게 알린다.
*/
public class WeatherData implements Subject {
    private List<Observer> observers;
    private float temperature;
    private float humidity;
    private float pressure;
    
    public WeatherData() {
        observers = new ArrayList<>();
    }
    
    public void registerObserver(Observer o) {
        observers.add(o);
    }
    
    public void removeObserver(Observer o) {
        observers.remove(o);
    }
    
    public void notifyObservers() { // 모든 옵저버에게 알리기.
        for(Observer each : observers) {
            each.update(temperature, humidity, pressure);
        }
    }
    
    public void measurementsChanged() {
        notifyObservers();
    }
    
    public void setMeasurements(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        measurementsChanged();
    }
}
```

### 디스플레이 항목을 만들어봅시다.

```jsx
public class CurrentConditionsDisplay implements Observer, DisplayElemnt {
    private float temperature;
    private float humidity;
    private Subject weatherData; // 이 객체는 당장 사용할 일이 없지만 저장해둠. 
    
    // 생성자에 subject 객체를 전달하고, 그 객체를 사용하여 디스플레이를 옵저버로 등록한다.
    public CurrentConditionsDisplay(Subject weatherData) { 
        this.weatherData = weatherData;
        weatherData.registerObserver(this);
    }
    
    public void update(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        dispaly();
    }
    
    public void display() {
        // 최근에 얻은 기온과 습도를 출력한다.
        System.out.println("Current conditions : " + temperature + "F degrees and " + humidity + "% humidity");
    }
}
```

### 기상스테이션을 돌려봅시다.

테스트용 클래스

```jsx
public class WeatherStation {
    public static void main(String [] args) {
        WeatherData weatherData = new weatherData(); // observer 생성
        
        CurrentConditionsDisplay currentDisplay = new CurrentConditionsDisplay(weatherData);
        
        // 새로운 기상 측정값이 들어온것처럼 만듭니다.
        weatherData.setMeasurements(80, 65, 30.4f);
        weatherData.setMeasurements(82, 70, 29.2f);
        weatherData.setMeasurements(78, 90, 29.2f);
    }
}
```

### 자바 내장 옵저버 패턴

지금까지는 우리가 직접 만든 코드로 옵저버 패턴을 구현해보았습니다. 하지만, 자바에서 몇 가지 API 를 통하여 자체적으로 옵저버 패턴을 지원하기도 합니다.

가장 일반적으로 java.util 패키지에 들어있는 Observer 인터페이스와 Observable 클래스를 들 수 있습니다. 그리고 이걸 사용하면, 푸시 방식으로도 갱신할 수 있고 풀 방식으로도 갱신이 가능하다. 즉, Observable 클래스는 위의 Subject 와 같은 역할을 하고, Observer 인터페이스는 위의 옵저버 인터페이스와 같은 역할을 한다.
