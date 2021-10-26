# 2. Observer Pattern

# 📚 Observer Pattern

## 📖 기상 관측 서비스 문제

- 기상 관측 장비로 부터, 기상 관측 정보가 도달하면 관련 데이터를 디스플레이에 보여주는 서비스 구성 문제
- 새로운 기상 관측 정보가 발생하면 `measurementsChanged()` 라는 함수가 호출 되는것이 약속되어 있음
- 온도, 습도, 압력 데이터를 수집하고, 그 데이터를 기반으로 보여주는 화면 3개가 있음
- 예시 코드
    
    ```java
    class WeatherData {
    		void measurementsChanged() {
    				float temp = getTemperature()
    				float humidity = getHumidity()
    				float pressure = getPressure()
    
    				currentConditionDisplay.update(temp, humidity, pressure)
    				statisticsDisplay.update(temp, humidity, pressure)
    				forecastDisplay.update(temp, humidity, pressure)
    		}
    }
    ```
    
- 위 코드의 구조에선, 새로운 디스플레이 항목이 추가될 때 마다 코드를 변경해야 함

## 📖 옵저버 패턴

한 객체의 상태가 변경 되었을 때, 다른 객체 ( Observer )들에게 update 신호를 보내고, 옵저버가 신호를 받았을 때 그에 맞춰 내용이 갱신되는 방식

- Subject와 Observer의 관계는 1대 다수의 관계
- Subject와 Observer로 분리 함으로써, Observer들끼리의 관계를 끊어버릴 수 있음
- Subject의 역할로써 Java에는 Observable 클래스를, Observer의 역할로서 Observer 인터페이스를 제공 해 줌
- Observable 클래스는 Subject의 역할을 하며, Observer를 등록 할 수 있음
- Observable 클래스에는 `addObserver` , `deleteObserver` 등의 함수가 이미 구현되어 있음

## 📖 소개된 디자인 패턴 원칙 정리

### 느슨한 결합

서로 상호작용을 하는 객체 사이에서는 가능하면 느슨하게 결합하는 디자인을 사용해야 함

- Subject가 Observer에 대해서 아는것은 observer interface를 구현하고 있다는 것이 유일
- 즉, 데이터가 변경 되었을 때 Observer가 어떤 역할을 하는지 등에 대한 구체적인 역할을 Subject는 전혀 몰라도 됨
- 또 Observer들끼리 서로 결합이 이루어 질 필요성이 없음

> Observer Pattern 참조 : [https://pinnate-boursin-6d8.notion.site/1-Observer-Pattern-9c51d75e592f4510a9ddc8df434b62d7](https://www.notion.so/1-Observer-Pattern-9c51d75e592f4510a9ddc8df434b62d7)
>