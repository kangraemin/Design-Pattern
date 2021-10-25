# 2장

## 옵저버 패턴

옵저버 패턴(Observer Pattern)은 한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체들한테 연락이 가고, 자동으로 내용이 갱신 되는 방식으로 일대다(one-to-many) 의존성을 가집니다

## 느슨한 결합(Loose Coupling)의 위력

두 객체가 느슨하게 결합되어 있다는 것은, 그 둘이 상호작용을 하긴 하지만 서로에 대해 서로 잘 모른다는 것을 의미합니다, 옵저버 패턴에서는 주제와 옵저버가 느슨하게 결합되어 있는 객체 디자인을 제공합니다.

- 주제가 옵저버에 대해서 아는 것은 옵저버가 특정 인터페이스를 구현한다는 것 뿐입니다.
- 옵저버는 언제든지 새로 추가할 수 있습니다
- 새로운 형식의 옵저버를 추가하려고 할 때도 주제를 전혀 변경할 필요가 없습니다.
- 주제와 옵저버는 서로 독립적으로 재사용할 수 있습니다.
- 주제나 옵져버가 바뀌더라도 서로한태 영향을 미치지는 않습니다

## Observer 구현

```kotlin
interface Observer {
    fun update(temp: Float, humidity: Float, pressure: Float)
}
```

기상정보가 변경되었을 때 update() 메소드가 호출되며 상태가 변경되었음을  알립니다.

## Subject 구현

```kotlin
interface Subject {
    fun registerObserver(o: Observer)
    fun removeObserver(o: Observer)
    fun notifyObservers()
}

class WeatherData : Subject {
    private val observers: ArrayList<Observer> = arrayListOf()
    private var temp: Float = 0f
    private var humidity: Float = 0f
    private var pressure: Float = 0f

    override fun registerObserver(o: Observer) {
        observers.add(o)
    }

    override fun removeObserver(o: Observer) {
        observers.remove(o)
    }

    override fun notifyObservers() {
        observers.forEach { observer ->
            observer.update(temp, humidity, pressure)
        }
    }

    fun setMeasurements(temp: Float, humidity: Float, pressure: Float){
        this.temp = temp
        this.humidity = humidity
        this.pressure = pressure
        notifyObservers()
    }
}
```

Subject Interface와 Weather Data라는 구현 클래스를 작성합니다.

- Observer 객체를 저장하기 위해 arrayList를 추가하고 registerObserver와 removeObserver를 구현해줍니다.
- notifyObservers 에서는 모든 데이터 변경되었을때 호출되면 등록된 모든Observers에 데이터가 변경되었음을 알립니다.
- setMeasurements 에서는 측정 데이터를 받아서 속성을 변경하고 / 를 호출하여 데이터가 변경되었음을 알립니다.

## Display 구현

```kotlin

interface DisplayElement {
    fun display()
}

class CurrentConditionDisplay(
    private val subject:WeatherData
): Observer, DisplayElement{
    private var temperature: Float = 0f
    private var humidity: Float = 0f

    fun registerObserver(){
        subject.registerObserver(this)
    }

    fun removeObserver(){
        subject.removeObserver(this)
    }

    override fun update(temp: Float, humidity: Float, pressure: Float) {
        this.temperature = temp
        this.humidity = humidity
        display()
    }

    override fun display() {
        println("온도 : $temperature / 습도 :$humidity")
    }
}
```

```kotlin
fun main() {
    val weatherData = WeatherData()
    val currentConditionDisplay = CurrentConditionDisplay(weatherData)

    currentConditionDisplay.registerObserver()
    weatherData.setMeasurements(1f,1f,1f)

    currentConditionDisplay.removeObserver()
    weatherData.setMeasurements(2f,2f,2f)
}
```