# 커맨드 패턴

<aside>
💡 실행될 기능을 캡슐화함으로써 주어진 여러 기능을 실행할 수 있는 재사용성이 높은 클래스를 설계하는 패턴

</aside>

즉, 어떤 객체(A)에서 다른 객체(B)의 메서드를 실행하려면 그 객체(B)를 참조하고 있어야 하는 의존성이 발생합니다.

그러나 커맨드 패턴을 적용하면 의존성을 제거할 수 있습니다.

또한 기능이 수정되거나 변경이 일어날 때 A 클래스 코드를 수정없이 기능에 대한 클래스를 정의하면 되므로 시스템이 확장성이 있으면서 유연해집니다.

구글홈이라고 "OK Google 히터 틀어줘" 라고 하면, 히터를 틀어주는 실제 구글 서비스가 있습니다.

사용자를 Client 클래스 구글홈을 OKGoogle 클래스, 히터를 Heater 클래스로 정의하도록 하겠습니다.

그러면 OKGoogle은 히터를 켜기 위해서 Heater 객체를 참조해야 합니다.

이를 코드로 표현하면 다음과 같습니다.

```jsx
public class Heater {
    public void powerOn(){
        System.out.println("Heater on");
    }
}
```

```jsx
public class OKGoogle {
    private Heater heater;

    public OKGoogle(Heater heater){
        this.heater = heater;
    }

    public void talk(){
        heater.powerOn();
    }
}
```

```jsx
public class Client {
    public static void main(String args[]){
        Heater heater = new Heater();
        OKGoogle okGoogle = new OKGoogle(heater);
        okGoogle.talk();
    }
}
```

그런데 OKGoogle에서 히터를 켜는 기능 말고, 램프를 켜는 기능을 추가하고 싶다면 어떻게 해야 할까요?

위와 같이 Lamp 클래스를 정의하고, OKGoogle 클래스에서 Lamp 객체를 참조하도록 해야 합니다.

물론 기존의 Heater 기능도 있어야 하구요.

이를 적용하여 코드로 표현하면 다음과 같습니다.

```jsx
public class Heater {
    public void powerOn(){
        System.out.println("Heater on");
    }
}
```

```jsx
public class Lamp {
    public void turnOn(){
        System.out.println("Lamp on");
    }
}
```

```jsx
public class OKGoogle {
    private static String[] modes = {"heater", "lamp"};

    private Heater heater;
    private Lamp lamp;
    private String mode;

    OKGoogle(Heater heater, Lamp lamp){
        this.heater = heater;
        this.lamp = lamp;
    }

    public void setMode(int idx){
        this.mode = modes[idx];
    }

    public void talk(){
        switch(this.mode){
            case "heater":
                this.heater.powerOn();
                break;
            case "lamp":
                this.lamp.turnOn();
                break;
        }

    }
}
```

```jsx
public class Client {
    public static void main(String args[]){
        Heater heater = new Heater();
        Lamp lamp = new Lamp();
        OKGoogle okGoogle = new OKGoogle(heater, lamp);

        // 램프 켜짐
        okGoogle.setMode(0);
        okGoogle.talk();

        // 알람 울림
        okGoogle.setMode(1);
        okGoogle.talk();
    }
}
```

OKGoogle에게 mode 설정을 통해, 모드가 0이면 히터를 틀고, 1이면 램프를 켜도록 가정했습니다.

OKGoogle은 히터를 틀고, 램프를 켜기 위해서 Heater, Lamp 객체를 참조해야 하기 때문에, OKGoogle의 기능이 많아질수록 객체 프로퍼티는 더욱 늘어날 것이고, 기존의 talk() 메서드에서 분기가 늘어날 것입니다.

OCP에도 위배되죠.

### **커맨드 패턴 적용**

문제점을 해결하기 위해 커맨드 패턴을 적용해보겠습니다.

먼저 OKGoogle이 할 수 있는 기능들(Heater를 튼다, Lamp를 킨다.) 을 클래스로 만들어서( HeaterOnCommand, LampOnCommand ) 각 기능들을 캡슐화 합니다.

그리고 OKGoogle 클래스의 talk() 메서드에서 heater.powerOn() , lamp.turnOn()과 같이 기능들을 직접 호출하지 않고,

캡슐화한 Command 인터페이스의 메서드를 호출하도록 합니다.


1. 먼저 인터페이스를 정의합니다.

```jsx
public interface Command {
    public void run();
}
```

1. Heater를 켜는 명령을 클래스화 하여, HeaterOnCommand 클래스를 정의하고, Heater 클래스는 그대로 히터를 켜는 powerOn() 메서드를 정의합니다.

```jsx
public class HeaterOnCommand implements Command{
    private Heater heater;

    public HeaterOnCommand(Heater heater){
        this.heater = heater;
    }

    public void run(){
        heater.powerOn();
    }
}
```

```jsx
public class Heater {
    public void powerOn(){
        System.out.println("Heater on");
    }
}
```

1. 마찬가지로 Lamp를 켜는 명령을 클래스화 하여, LampOnCommand 클래스를 정의하고 Lamp 클래스는 그대로 램프를 켜는 turnOn() 메서드를 정의합니다.

```jsx
public class LampOnCommand implements Command{
    private Lamp lamp;

    public LampOnCommand(Lamp lamp){
        this.lamp = lamp;
    }

    public void run(){
        lamp.turnOn();
    }
}
```

```jsx
public class Lamp {
    public void turnOn(){
        System.out.println("Lamp on");
    }
}
```

1. OKGoogle 클래스의 talk() 메서드에서는 Command 인터페이스의 run() 메서드를 하여 명령을 실행합니다

```jsx
public class OKGoogle {
    private Command command;

    public void setCommand(Command command){
        this.command = command;
    }

    public void talk(){
        command.run();
    }
}
```

1. 마지막으로 OKGoogle을 사용하는 Client 클래스를 정의합니다.

```jsx
public class Client {
    public static void main(String args[]){
        Heater heater = new Heater();
        Lamp lamp = new Lamp();

        Command heaterOnCommand = new HeaterOnCommand(heater);
        Command lampOnCommand = new LampOnCommand(lamp);
        OKGoogle okGoogle = new OKGoogle();

        // 히터를 켠다
        okGoogle.setCommand(heaterOnCommand);
        okGoogle.talk();

        // 램프를 켠다
        okGoogle.setCommand(lampOnCommand);
        okGoogle.talk();

    }
}
```

만약 OKGoogle에 TV를 틀어줘 기능이 추가된다면,

TVOnCommand 클래스를 추가하면 되므로, OCP에 위배되지 않으면서 기능을 추가할 수 있습니다.

---

참고 문서

[https://victorydntmd.tistory.com/295](https://victorydntmd.tistory.com/295)
