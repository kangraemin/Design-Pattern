# 11장

## 프록시패턴

어떤 객체에 대한 접근을 제어하기 위한 용도로 대리인이나 대변인에 해당하는 객체를 제공하는 패턴

**프록시에서 접근을 제어하는 몇가지 방법은 다음과 같습니다.**

- 원격 프록시를 써서 원격 객체에 대한 접근을 제어합니다.
- 가상 프록시(virtual proxy)를 써서 생성하기 힘든 자원에 대한 접근을 제어할 수 있습니다.
- 보호 프록시(protection proxy)를 써서 접근 권한이 필요한 자원에 대한 접근을 제어할 수 있습니다.

## 클래스 다이어그램

![Untitled](11%E1%84%8C%E1%85%A1%E1%86%BC%204b11e39dfb684151848af7929ea3c36f/Untitled.png)

**<Interface>Subject**

Proxy와 RealSubject가 모두 구현해야 하는 인터페이스.

이를 이용해 어떤 클라이언트에서든 프록시를 주 객체하고 똑같은 식으로 다룰 수 있습니다

**RealSubject**

실제 작업을 대부분 처리하는 객체. Proxy는 이 객체에 대한 접근을 제어하는 객체

**Proxy**

실제 작업을 처리하는 객체에 대한 래퍼런스가 들어있습니다.

이를 이용해 실제 객체가 필요하면 그 래퍼런스를 이용해서 요청을 전달합니다

### 원격 프록시

- 원격 프록시는 다른 JVM에 들어있는 객체의 대변인에 해당하는 로컬 객체입니다.
- 프록시의 메소드를 호출하면 그 호출이 네트워크를 통해서 전달되어 결국 원격 객체의 메소드가 호출 됩니다.
- 호출된 결과는 다시 프록시를 거쳐서 클라이언트한태 전달됩니다.

### 가상 프록시

- 가상 프록시는 생성하는 데 많은 비용이 드는 객체를 대신하는 역할을 맡습니다.
- 실제로 진짜 객체가 필요하게 되기 전까지 객체의 생성을 미루게 해 주는 기능을 제공하기도 합니다.
- 객체 생성 도중에 객체를 대신하기도 하고 객체 생성이 완료되고 나면 그냥 RealSubject에 요청을 직접 전달해 줍니다.

![Untitled](11%E1%84%8C%E1%85%A1%E1%86%BC%204b11e39dfb684151848af7929ea3c36f/Untitled%201.png)

## CD 이미지 뷰어

```java
class ImageProxy(imageUrl: URL) : Icon {

    private var imageIcon: ImageIcon? = null
    private var retrievalThread: Runnable? = null
    private var retrieving = false

    override fun getIconWidth() =
        if (imageIcon != null) {
            imageIcon!!.getIconWidth()
        } else {
            800
        }

    override fun getIconHeight() =
        if (imageIcon != null) {
            imageIcon!!.getIconHeight()
        } else {
            600
        }

    override fun paintIcon(c: Component, g: Graphics, x: Int, y: Int) {
        if(imageIcon != null) {
            imageIcon!!.paintIcon(c,g,x,y)
        } else {
            g.drawString("Loading CD cover plase wait....", x+300, y+190)
            if (!retrieving) {
                retrieving = true
                retrievalThread = Runnable {
                    try {
                        imageIcon = new ImageIcon(imageURL, "CD Cover")
                        c.repaint()
                    } catch (e:Exception) {
                        e.printStackTrace()
                    }
                }
                retrievalThread?.run()
            }
        }
    }
}
```

- paintIcon 호출시 만약 imageIcon이 준비되어 있다면 imageIcon의 메소드를 호출합니다.
- 아직 imageIcon이 준비되지 않았다면 Loading중이라는 메세지를 출력합니다.
- 이미지를 비동기적으로 불러오며 다 불러왔다면 repaint 메소드를 호출하여 화면을 갱신합니다.

## 프록시 VS 데코레이터

### 프록시 패턴

원치 않는 접근으로부터 보호해주거나 커다란 객체를 로딩하는 동안 프로그램이 멈추는 것을 방지해 주거나 Subject 객체가 원격 시스템에서 돌아가고 있다는 사실을 숨겨주는 등의 기능을 제공할 수 있다.

### 데코레이터 패턴

코드를 수정하지 않고도 객체가 할 수 있는 행동을 추가해준다. 구성과 위임을 통해서 실행중에 동적으로 새로운 행동을 추가할 수 있으며 구성요소를 감싸는 데코레이터의 개수에는 제한이 없다.

## 핵심정리

- 프록시 패턴을 이용하면 어떤 객체에 대한 대변인을 내세워서 클라이언트의 접근을 제어 할 수 있습니다. 접근을 관리하는 방법에는 여러가지가 있습니다.
- 원격 프록시는 클라이언트와 원격 객체 사이의 데이터 전달을 관리해 줍니다.
- 가상 프록시는 인스턴스를 만드는 데 많은 비용이 드는 객체에 대한 접근을 제어합니다.
- 보호 프록시는 호출하는 쪽의 권한에 따라서 객체에 있는 메소드에 대한 접근을 제어합니다.
- 그 외에도 **캐싱 프록시**, **동기화 프록시**, **방화벽 프록시**, **지연 복사 프록시**와 같이 다양한 변형된 프록시 패턴이 있습니다.
- 프록시 패턴의 구조는 데코레이터 패턴의 구조하고 비슷하지만 그 용도가 다르다는 차이점이 있습니다.
- 데코레이터 패턴에서는 객체에 행동을 추가하지만 프록시 패턴에서는 접근을 제어합니다.
- 자바에 내장된 프록시 지원 기능을 이용하면 동적 프록시 클래스를 즉석에서 만들어서 원하는 핸들러에서 호출을 처리하도록 할 수 있습니다.
- 다른 래퍼를 쓸 때와 만찬가지로 프록시를 쓰면 디자인에 포함되는 클래스와 객체의 수가 늘어납니다.