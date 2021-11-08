# Command Pattern

# Command Pattern이란?

실행될 기능을 캡슐화함으로써 주어진 여러 기능을 실행할 수 있는 재사용성이 높은 클래스를 설계하는 패턴 이다.

커맨드 패턴을 이용하면 요구 사항을 객체로 캡슐화 할 수 있고, 매개변수를 써서 여러 가지 다른 요구사항을 집어넣을수 있다. 또한 요청을 큐에 저장하여 로그로 기록하거나 작업 취소 기능도 지원 가능 하다. 작업을 요청한 쪽과 그 작업을 처리하는 쪽을 분리 시킬수 있는게 커맨드 패턴의 핵심이다.

![http://pds4.egloos.com/pds/200705/18/13/d0014013_06051297.gif](http://pds4.egloos.com/pds/200705/18/13/d0014013_06051297.gif)

- `Client`는 커맨드 객체 ConcreteCommand를 생성하고 Receiver를 설정한다.
- `ConcreteCommand`는 **command 객체**로, 행동을 캡슐화하고 리시버에 있는 특정 행동을 처리하기 위한 execute() 메소드 하나만 제공하며 특정 action과 receiver사이를 연결해 준다. Invoker에서 execute() 호출을 통해 요청을 하면 ConcreteCommand 객체에서 receiver에 있는 메소드를 호출함으로써 그 작업을 처리한다.
- `Invoker`에는 명령이 들어있으며, execute() 메소드를 호출함으로써 **command 객체**에게 특정 작업을 수행해 달라는 요구를 하게 된다.
- `Command`는 모든 커맨드 객체에서 구현해야하는 인터페이스이다. 모든 명령은 execute() 메소드 호출을 통해 수행되며, 이 메소드에서는 Receiver에 특정 작업을 처리하라는 지시를 전달한다.
- `ConcreteCommand`는 **command 객체**로, 특정 action과 receiver사이를 연결해 준다. Invoker에서 execute() 호출을 통해 요청을 하면 ConcreteCommand 객체에서 receiver에 있는 메소드를 호출함으로써 그 작업을 처리한다.

## 버튼이 하나인 리모컨

예를 들어, 전등 장치인 Light가 있다고 가정하자. Light는 **on()**과 **off()**메소드만 가지고 있다.

전등을 키고 싶을 땐, 전등에게 리모컨을 통하여 불을 키라는 명령만 전달하면 될 것이다. 전등이 어떤 방법으로 불을 켜는지는 관심이 없다.

```java
public interface Command {    
    public void execute();
}
```

```java
// 전등 불 키는 커맨드 객체
public class LightOnCommand implements Command {  
    Light light;

  public LightOnCommand(Light light){
    this.light = light;
  }

  // 리시버 객체(여기서는 전등)의 on()메소드를 호출한다.
  public void execute(){
    light.on();
  }
}
```

```java
public class SimpleRemoteControl {    // 버튼이 하나밖에 없는 리모컨일 때
    Command slot;

    public SimpleRemoteControl() {}

  public void setCommand(Command command) {
    slot = command;
  }

  public void buttonWasPressed() {
    slot.execute();
  }
}
```

## 버튼이 여러 개인 리모컨

제어 할수 있는 장치의 개수가 7개고 각 장치 를 on/off 할수 있는 버튼이 2개가 있다고 가정 하자

```java
// 리모콘 
public class RemoteControl {
    Command[] onCommands;
    Command[] offCommands;
    Command undoCommand;

    public RemoteControl() {
        onCommands = new Command[7];
        offCommands = new Command[7];

        Command noCommand = new NoCommand();
        for(int i=0;i<7;i++) {
            onCommands[i] = noCommand;
            offCommands[i] = noCommand;
        }
        undoCommand = noCommand;
    }

  // 슬롯 번호와 그 슬롯에 저장할 on/off 명령을 인자로 받는다.
  // 각 커맨드 객체를 나중에 사용할 수 있도록 배열에 저장한다.
    public void setCommand(int slot, Command onCommand, Command offCommand) {
        onCommands[slot] = onCommand;
        offCommands[slot] = offCommand;
    }

    public void onButtonWasPushed(int slot) {
        onCommands[slot].execute();
        undoCommand = onCommands[slot];
    }

    public void offButtonWasPushed(int slot) {
        offCommands[slot].execute();
        undoCommand = offCommands[slot];
    }

  public String toString() {
        StringBuilder sb = new StringBuilder();
        sb.append("\n------ Remote Control -------\n");
        for (int i = 0; i < onCommands.length; i++) {
            sb.append("[slot " + i + "] " + onCommands[i].getClass().getName()
                + "    " + offCommands[i].getClass().getName() + "\n");
        }
        return stringBuff.toString();
    }
}
```

```java
//커맨드 클래스 
public class LightOnCommand implements Command {  // 불 키는 커맨드 객체
    Light light;

  public LightOnCommand(Light light){
    this.light = light;
  }

  // 리시버 객체(여기서는 전등)의 on()메소드를 호출한다.
  public void execute(){
    light.on();
  }
}
```

```java
public class LightOffCommand implements Command {    // 불 끄는 커맨드 객체
    Light light;

  public LightOnCommand(Light light){
    this.light = light;
  }

  // 리시버 객체(여기서는 전등)의 off()메소드를 호출한다.
  public void execute(){
    light.off();
  }
}
```

on/off 이후 처리 할 작업 있으면 execute() 안에 작업을 요청하면 된다

## 리모컨 테스트

1. 리시버 생성 ( 전등, 선풍기 팬 , 창고 문 등)
2. 장치별 커맨드 객체 생성 (여기선 LightOnCommand, LightOffCommand)
3. invoker에 command객체 저장 ( 여기서 invoker는 리모컨)
4. receiver에게 명령 전달  (커맨드 객체 넘김)

```java
public class RemoteLoader {

    public static void main(String[] args) {
        RemoteControl remoteControl = new RemoteControl();

    // receiver 생성
        Light livingRoomLight = new Light("Living Room");
        Light kitchenLight = new Light("Kitchen");
        CeilingFan ceilingFan= new CeilingFan("Living Room");
        GarageDoor garageDoor = new GarageDoor("");
        Stereo stereo = new Stereo("Living Room");

    // 각 장치별 커맨드 객체 생성
        LightOnCommand livingRoomLightOn = new LightOnCommand(livingRoomLight);
        LightOffCommand livingRoomLightOff = new LightOffCommand(livingRoomLight);
        LightOnCommand kitchenLightOn = new LightOnCommand(kitchenLight);
        LightOffCommand kitchenLightOff = new LightOffCommand(kitchenLight);

        CeilingFanOnCommand ceilingFanOn = new CeilingFanOnCommand(ceilingFan);
        CeilingFanOffCommand ceilingFanOff = new CeilingFanOffCommand(ceilingFan);

        GarageDoorUpCommand garageDoorUp = new GarageDoorUpCommand(garageDoor);
        GarageDoorDownCommand garageDoorDown = new GarageDoorDownCommand(garageDoor);

        StereoOnWithCDCommand stereoOnWithCD = new StereoOnWithCDCommand(stereo);
        StereoOffCommand  stereoOff = new StereoOffCommand(stereo);

    // invoker에 커맨드 객체를 실행
        remoteControl.setCommand(0, livingRoomLightOn, livingRoomLightOff);
        remoteControl.setCommand(1, kitchenLightOn, kitchenLightOff);
        remoteControl.setCommand(2, ceilingFanOn, ceilingFanOff);
        remoteControl.setCommand(3, stereoOnWithCD, stereoOff);

        System.out.println(remoteControl);

    // invoker를 통해 receiver 에게 커맨드 객체를 전달
        remoteControl.onButtonWasPushed(0);
        remoteControl.offButtonWasPushed(0);
        remoteControl.onButtonWasPushed(1);
        remoteControl.offButtonWasPushed(1);
        remoteControl.onButtonWasPushed(2);
        remoteControl.offButtonWasPushed(2);
        remoteControl.onButtonWasPushed(3);
        remoteControl.offButtonWasPushed(3);
    }
}

/** 
    ------ Remote Control -------
    [slot 0] headfirst.designpatterns.command.remote.LightOnCommand    headfirst.designpatterns.command.remote.LightOffCommand
    [slot 1] headfirst.designpatterns.command.remote.LightOnCommand    headfirst.designpatterns.command.remote.LightOffCommand
    [slot 2] headfirst.designpatterns.command.remote.CeilingFanOnCommand    headfirst.designpatterns.command.remote.CeilingFanOffCommand
    [slot 3] headfirst.designpatterns.command.remote.StereoOnWithCDCommand    headfirst.designpatterns.command.remote.StereoOffCommand
    [slot 4] headfirst.designpatterns.command.remote.NoCommand    headfirst.designpatterns.command.remote.NoCommand
    [slot 5] headfirst.designpatterns.command.remote.NoCommand    headfirst.designpatterns.command.remote.NoCommand
    [slot 6] headfirst.designpatterns.command.remote.NoCommand    headfirst.designpatterns.command.remote.NoCommand

    Living Room light is on
    Living Room light is off
    Kitchen light is on
    Kitchen light is off
    Living Room ceiling fan is on high
    Living Room ceiling fan is off
    Living Room stereo is on
    Living Room stereo is set for CD input
    Living Room Stereo volume set to 11
    Living Room stereo is off
*/
```

### NoCommand

위에 slot 4~6 까지 나온 NoCommand 객체는 일종의 널 객체(Null Object)이다. 리턴할 객체는 없지만 클라이언트 쪽에서 null을 처리하지 않도록 하고 싶을 때 널 객체를 사용하면 좋다.

특정 슬롯을 사용하려고 할 때 사용 중인 슬롯인지를 체크하는 것 보다, 빈 슬롯임을 나타낼 수 있는 NoCommand 와 같은 객체로 초기화 해두면 된다.

### receiver들에게 모두 적용 되는 기능 추가

Command 인터페이스에 적용될 메소드를 추가하여, 모든 커맨드 객체가 이를 구현하도록 만들면 된다.

예를 들어, 지금은 execute() 메소드 밖에 없지만 직전 작업을 취소하는 undo() 메소드 등을 추가할 수도 있다.

```java
public interface Command {
    public void execute();
    public void undo(); // undo 추가
}
```

```java
public class LightOnCommand implements Command {
    Light light;
    
    public LightOnCommand(Light light) {
        this.light = light;
    }    
    
    public void execute() {
        light.on();
    }
    
    // execute 불키고 undo 에서 불끄고
    public void undo() {
        light.off();
    }
}
```

```java
public class LightOffCommand implements Command {
    
    Light light;
    
    public LightOffCommand(Light light) {
        this.light = light;
    }    
    
    public void execute() {
        light.off();
    }

    public void undo() {
        light.on(); 
    }
}
```

```java
public class RemoteControl {
    Command[] onCommands;
    Command[] offCommands;
    Command undoCommand; // 마지막으로 작동한 상태 값 저장하기 위한 undo 객체를 생성
    
    public RemoteControl() {
        onCommands = new Command[7];
        offCommands = new Command[7];
        
        Command noCommand = new NoCommand();
        for(int i = 0; i<7; i++) {
            onCommands[i] = noCommand;
            offCommands[i] = noCommand;
        }
        
        undoCommand = noCommand; // undoCommand 를 초기화
    }
    
    public void setCommand(int slot, Command onCommand, Command offCommand) {
        onCommand[slot] = onCommand;
        offCommand[slot] = offCommand;
    }
    
    public void onButtonWasPushed(int slot) {
        onCommands[slot].execute();
        undoCommand = onCommands[slot]; // 상태 저장
    } 
    
    public void offButtonWasPushed(int slot) {
        offCommands[slot].execute();
        undoCommand = offCommands[slot]; // 상태 저장
    }
    
    public void undoButtonWasPushed() {
        undoCommand.undo();
    }
}
```

## 매크로 커맨드

매크로 커맨드는 여러 개의 커맨드를 한 곳에 묶어 놓은 커맨드이다

위에서 여러 작업을 순서대로 진행하려면 execute() 안에 코드를 추가해야 했다.

하지만 매크로 커맨드를 사용하는게 편리할 것이다.

```java
public class MacroCommand implements Command {
    Command[] commands;

    public MacroCommand(Command[] commands) {
        this.commands = commands;
    }

    public void execute() {
        for (int i = 0; i < commands.length; i++) {
            commands[i].execute();
        }
    }

    public void undo() {
        for (int i = commands.length -1; i >= 0; i--) {
            commands[i].undo();
        }
    }
}
```

위에서 보는 것처럼 커맨드 배열을 받아 순서대로 작업을 수행하는 것을 볼 수 있다.

```java
public class RemoteLoader {

    public static void main(String[] args) {

    // invoker 생성
        RemoteControl remoteControl = new RemoteControl();

    // receiver들 생성
        Light light = new Light("Living Room");
        TV tv = new TV("Living Room");
        Stereo stereo = new Stereo("Living Room");
        Hottub hottub = new Hottub();

    // 커맨드 객체들을 생성
        LightOnCommand lightOn = new LightOnCommand(light);
        StereoOnCommand stereoOn = new StereoOnCommand(stereo);
        TVOnCommand tvOn = new TVOnCommand(tv);
        HottubOnCommand hottubOn = new HottubOnCommand(hottub);
        LightOffCommand lightOff = new LightOffCommand(light);
        StereoOffCommand stereoOff = new StereoOffCommand(stereo);
        TVOffCommand tvOff = new TVOffCommand(tv);
        HottubOffCommand hottubOff = new HottubOffCommand(hottub);

    // 커맨드 객체들을 작업 순서대로 하나의 커맨드 배열로 묶음
        Command[] partyOn = { lightOn, stereoOn, tvOn, hottubOn};
        Command[] partyOff = { lightOff, stereoOff, tvOff, hottubOff};

    // 커맨드 배열을 매크로 커맨드로 생성
        MacroCommand partyOnMacro = new MacroCommand(partyOn);
        MacroCommand partyOffMacro = new MacroCommand(partyOff);

    // 인보커에 매크로 커맨드를 로드
        remoteControl.setCommand(0, partyOnMacro, partyOffMacro);

        System.out.println(remoteControl);
        System.out.println("--- Pushing Macro On---");
        remoteControl.onButtonWasPushed(0);
        System.out.println("--- Pushing Macro Off---");
        remoteControl.offButtonWasPushed(0);
    }
}

/** 
            ------ Remote Control -------
      [slot 0] headfirst.designpatterns.command.party.MacroCommand    headfirst.designpatterns.command.party.MacroCommand
      [slot 1] headfirst.designpatterns.command.party.NoCommand    headfirst.designpatterns.command.party.NoCommand
      [slot 2] headfirst.designpatterns.command.party.NoCommand    headfirst.designpatterns.command.party.NoCommand
      [slot 3] headfirst.designpatterns.command.party.NoCommand    headfirst.designpatterns.command.party.NoCommand
      [slot 4] headfirst.designpatterns.command.party.NoCommand    headfirst.designpatterns.command.party.NoCommand
      [slot 5] headfirst.designpatterns.command.party.NoCommand    headfirst.designpatterns.command.party.NoCommand
      [slot 6] headfirst.designpatterns.command.party.NoCommand    headfirst.designpatterns.command.party.NoCommand
      [undo] headfirst.designpatterns.command.party.NoCommand

      --- Pushing Macro On---
      Light is on
      Living Room stereo is on
      Living Room TV is on
      Living Room TV channel is set for DVD
      Hottub is heating to a steaming 104 degrees
      Hottub is bubbling!
      --- Pushing Macro Off---
      Light is off
      Living Room stereo is off
      Living Room TV is off
      Hottub is cooling to 98 degrees
*/
```

### Command 패턴의 **장점과 단점**

- 장점 :
    - 커맨드 패턴을 활용하게 요청부와 동작부를 분리시켜주기 때문에 시스템의 결합도를 낮출 수 있으며, 각 객체들이 수정되어도 다른 객체가 영향을 받지않는다.
    - 클라이언트와 INVOKER 클래스 간의 의존성이 제거 된다.
- 단점 : 리시버 및 리시버의 동작이 추가된다면 그 동작에 대한 클래스를 만들어야 하기 때문에, 다소 많은 잡다한 클래스들이 추가된다

출처: [https://blog.hexabrain.net/352](https://blog.hexabrain.net/352)