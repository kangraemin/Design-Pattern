# 6. Command Pattern

# 📚 Command Pattern

## 📖 Remote control problem - Comamand pattern

- on / off 버튼만 있는 리모콘이 존재 한다고 가정
- 이 리모콘에다가, 거실의 형광등을 키거나 / 거실의 형광등을 끄고, 선풍기를 켜거나 끄는등의 작업을 한다고 가정
- 이를 구현하기 위해, 리모컨 버튼의 on / off 를 눌렀을 때, 동작하는 동작의 구체적인 내용을 리모컨에 담으면, 유지보수가 매우 어려워 짐
- 따라서, 리모컨 버튼의 on / off 버튼에다가 각 버튼의 구체적인 내용을 실행할 수 있도록 구현 해놓은 `커맨드 객체` 를 심어두고, 버튼을 눌렀을 때 커맨트 객체에 담긴 내용을 실행하도록 구현
- 좀 더 풀어설명하면, `커맨드 객체`는 Command 인터페이스의 구현체
    
    ```java
    interface Command {
    		void execute()
    }
    ```
    
    ```java
    interface Light {
    		void on();
    		void off();
    }
    ```
    
    ```java
    class LightOnCommand implements Command {
    		Light light;
    		
    		LightOnCommand(Light light) {
    				this.light = light;
    		}
    
    		void execute() {
    				light.on();
    		}
    }
    ```
    
    - 이때 Light 는 주방 형광등이 될수도, 화장실 형광등이 될 수도 있기 때문에 Light interface에 의존하는것이 좋음
- 리모컨의 버튼에는 command를 설정
    
    ```java
    class RemoteControl {
    		Command[] onCommands;
    		Command[] offCommands;
    
    		public RemoteControl() {
    				onCommands = new Command[7]; // 버튼이 7개
    				offCommands = new Command[7]; // 버튼이 7개
    
    				Command noCommand = new NoCommand(); // Default command, null의 역할
    				for (int i = 0; i < 7; i++) {
    						onCommands[i] = noCommand;
    						offCommands[i] = noCommand;		
    				}
    		}
    
    		void setCommand(int slot, Command onCommand, Command offCommand) {
    				onCommands[slot] = onCommand;
    				offCommands[slot] = offCommand;
    		}
    
    		void onButtonPushed(int slot) {
    				onCommands[slot].execute();
    		}
    
    		void offButtonPushed(int slot) {
    				offCommands[slot].execute();
    		}
    }
    ```
    
- Undo 기능 추가
    
    ```java
    interface Command {
    		void execute()
    		void undo()
    }
    ```
    
    ```java
    class LightOnCommand implements Command {
    		Light light;
    		
    		LightOnCommand(Light light) {
    				this.light = light;
    		}
    
    		void execute() {
    				light.on();
    		}
    
    		void undo() {
    				light.off();
    		}
    }
    ```
    
    ```java
    class RemoteControl {
    		Command[] onCommands;
    		Command[] offCommands;
    		Command undoCommand;
    
    		public RemoteControl() {
    				onCommands = new Command[7]; // 버튼이 7개
    				offCommands = new Command[7]; // 버튼이 7개
    
    				Command noCommand = new NoCommand(); // Default command, null의 역할
    				for (int i = 0; i < 7; i++) {
    						onCommands[i] = noCommand;
    						offCommands[i] = noCommand;		
    				}
    				undoCommand = noCommand;
    		}
    
    		void setCommand(int slot, Command onCommand, Command offCommand) {
    				onCommands[slot] = onCommand;
    				offCommands[slot] = offCommand;
    		}
    
    		void onButtonPushed(int slot) {
    				onCommands[slot].execute();
    				undoCommand = onCommands[slot];
    		}
    
    		void offButtonPushed(int slot) {
    				offCommands[slot].execute();
    				undoCommand = offCommands[slot];
    		}
    
    		void undoButtonPushed() {
    				undoCommand.undo();
    		}
    }
    ```
    
- 리모컨에 Custom command 입력 가능 ( Party Mode )
    
    ```java
    class MacroCommand implements Command {
    		Command[] commands;
    
    		MacroCommand(Command[] commands) {
    				this.commands = commands;
    		}
    
    		void execute() {
    				for (int i = 0; i < commands.length; i++) {
    						commands[i].execute();
    				}
    		}
    }
    ```
    
    ```java
    Command[] partyOn = { lightOn, stereoOn ... }
    Command[] partyOff = { lightOff, stereoOff ... }
    
    MacroCommand partyOnMacro = new MacroCommand(partyOn);
    MacroCommand partyOffMacro = new MacroCommand(partyOff);
    
    remoteControl.setCommand(0, partyOnMacro, partyOffMacro);
    ```
    
- 커맨드 패턴을 활용하여, Work Queue / 실행 히스토리 저장 등을 구현 할 수 있음

## 📖 소개된 디자인 패턴 원칙 정리

### 커맨드 패턴

<aside>
💡 실행될 기능을 캡슐화 ( Command 인터페이스의 객체화 ) 하여, 여러개의 실행될 기능 ( 여러 Command 객체들 )을 담아놓고 사용할 수 있는 클래스 ( invoker )를 만들어 사용하는 패턴

</aside>

- 용어 정리
    - Receiver ( 수신자 ) : 실제 기능을 수행하는 행위를 담아놓은 interface ( 위의 예시에서 Light interface )
        - 구체 클래스에 실제 기능을 구현 해 놓음
    - Command ( 명령 ) : 명령어 모음 인터페이스 ( 위의 예시에서 command interface )로서, 어떤 행위를 할지 담아놓은 interface
        - 구체 클래스에 receiver ( 수신자 객체 )를 담아놓고, 명령어가 실행 되었을 때 수신자의 함수를 호출 해줌
    - Invoker ( 발동자 ) : Invoker 클래스에 Command를 담아 놓고, 필요한 경우 Command 객체에 명령을 내려주는 class ( 위의 예시에서 RemoteControl )
        - Command 를 여러개 담을수도, 하나만 담을수도 있음
    - Client ( 클라이언트 ) : invoker ( 발동자 )를 초기화 하고, 발동자에 커맨드 객체를 넘겨주는 역할
        - 실제로 커맨드 객체를 생성하여 invoker를 초기화 시켜주고, 셋팅해주며 invoker 객체를 사용할 수 있도록 만들어 주는 곳
- Command 객체를 invoker에 어떻게 담아 놓느냐, 어떤 형식으로 실행하느냐에 따라 여러가지 기능을 쉽게 구현 할 수 있음
    - 위의 예시에서 Undo 기능 추가, party mode 추가 등등에 해당됨