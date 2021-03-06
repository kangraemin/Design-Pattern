# 6. Command Pattern

# ๐ Command Pattern

## ๐ Remote control problem - Comamand pattern

- on / off ๋ฒํผ๋ง ์๋ ๋ฆฌ๋ชจ์ฝ์ด ์กด์ฌ ํ๋ค๊ณ  ๊ฐ์ 
- ์ด ๋ฆฌ๋ชจ์ฝ์๋ค๊ฐ, ๊ฑฐ์ค์ ํ๊ด๋ฑ์ ํค๊ฑฐ๋ / ๊ฑฐ์ค์ ํ๊ด๋ฑ์ ๋๊ณ , ์ ํ๊ธฐ๋ฅผ ์ผ๊ฑฐ๋ ๋๋๋ฑ์ ์์์ ํ๋ค๊ณ  ๊ฐ์ 
- ์ด๋ฅผ ๊ตฌํํ๊ธฐ ์ํด, ๋ฆฌ๋ชจ์ปจ ๋ฒํผ์ on / off ๋ฅผ ๋๋ ์ ๋, ๋์ํ๋ ๋์์ ๊ตฌ์ฒด์ ์ธ ๋ด์ฉ์ ๋ฆฌ๋ชจ์ปจ์ ๋ด์ผ๋ฉด, ์ ์ง๋ณด์๊ฐ ๋งค์ฐ ์ด๋ ค์ ์ง
- ๋ฐ๋ผ์, ๋ฆฌ๋ชจ์ปจ ๋ฒํผ์ on / off ๋ฒํผ์๋ค๊ฐ ๊ฐ ๋ฒํผ์ ๊ตฌ์ฒด์ ์ธ ๋ด์ฉ์ ์คํํ  ์ ์๋๋ก ๊ตฌํ ํด๋์ `์ปค๋งจ๋ ๊ฐ์ฒด` ๋ฅผ ์ฌ์ด๋๊ณ , ๋ฒํผ์ ๋๋ ์ ๋ ์ปค๋งจํธ ๊ฐ์ฒด์ ๋ด๊ธด ๋ด์ฉ์ ์คํํ๋๋ก ๊ตฌํ
- ์ข ๋ ํ์ด์ค๋ชํ๋ฉด, `์ปค๋งจ๋ ๊ฐ์ฒด`๋ Command ์ธํฐํ์ด์ค์ ๊ตฌํ์ฒด
    
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
    
    - ์ด๋ Light ๋ ์ฃผ๋ฐฉ ํ๊ด๋ฑ์ด ๋ ์๋, ํ์ฅ์ค ํ๊ด๋ฑ์ด ๋  ์๋ ์๊ธฐ ๋๋ฌธ์ Light interface์ ์์กดํ๋๊ฒ์ด ์ข์
- ๋ฆฌ๋ชจ์ปจ์ ๋ฒํผ์๋ command๋ฅผ ์ค์ 
    
    ```java
    class RemoteControl {
    		Command[] onCommands;
    		Command[] offCommands;
    
    		public RemoteControl() {
    				onCommands = new Command[7]; // ๋ฒํผ์ด 7๊ฐ
    				offCommands = new Command[7]; // ๋ฒํผ์ด 7๊ฐ
    
    				Command noCommand = new NoCommand(); // Default command, null์ ์ญํ 
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
    
- Undo ๊ธฐ๋ฅ ์ถ๊ฐ
    
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
    				onCommands = new Command[7]; // ๋ฒํผ์ด 7๊ฐ
    				offCommands = new Command[7]; // ๋ฒํผ์ด 7๊ฐ
    
    				Command noCommand = new NoCommand(); // Default command, null์ ์ญํ 
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
    
- ๋ฆฌ๋ชจ์ปจ์ Custom command ์๋ ฅ ๊ฐ๋ฅ ( Party Mode )
    
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
    
- ์ปค๋งจ๋ ํจํด์ ํ์ฉํ์ฌ, Work Queue / ์คํ ํ์คํ ๋ฆฌ ์ ์ฅ ๋ฑ์ ๊ตฌํ ํ  ์ ์์

## ๐ ์๊ฐ๋ ๋์์ธ ํจํด ์์น ์ ๋ฆฌ

### ์ปค๋งจ๋ ํจํด

<aside>
๐ก ์คํ๋  ๊ธฐ๋ฅ์ ์บก์ํ ( Command ์ธํฐํ์ด์ค์ ๊ฐ์ฒดํ ) ํ์ฌ, ์ฌ๋ฌ๊ฐ์ ์คํ๋  ๊ธฐ๋ฅ ( ์ฌ๋ฌ Command ๊ฐ์ฒด๋ค )์ ๋ด์๋๊ณ  ์ฌ์ฉํ  ์ ์๋ ํด๋์ค ( invoker )๋ฅผ ๋ง๋ค์ด ์ฌ์ฉํ๋ ํจํด

</aside>

- ์ฉ์ด ์ ๋ฆฌ
    - Receiver ( ์์ ์ ) : ์ค์  ๊ธฐ๋ฅ์ ์ํํ๋ ํ์๋ฅผ ๋ด์๋์ interface ( ์์ ์์์์ Light interface )
        - ๊ตฌ์ฒด ํด๋์ค์ ์ค์  ๊ธฐ๋ฅ์ ๊ตฌํ ํด ๋์
    - Command ( ๋ช๋ น ) : ๋ช๋ น์ด ๋ชจ์ ์ธํฐํ์ด์ค ( ์์ ์์์์ command interface )๋ก์, ์ด๋ค ํ์๋ฅผ ํ ์ง ๋ด์๋์ interface
        - ๊ตฌ์ฒด ํด๋์ค์ receiver ( ์์ ์ ๊ฐ์ฒด )๋ฅผ ๋ด์๋๊ณ , ๋ช๋ น์ด๊ฐ ์คํ ๋์์ ๋ ์์ ์์ ํจ์๋ฅผ ํธ์ถ ํด์ค
    - Invoker ( ๋ฐ๋์ ) : Invoker ํด๋์ค์ Command๋ฅผ ๋ด์ ๋๊ณ , ํ์ํ ๊ฒฝ์ฐ Command ๊ฐ์ฒด์ ๋ช๋ น์ ๋ด๋ ค์ฃผ๋ class ( ์์ ์์์์ RemoteControl )
        - Command ๋ฅผ ์ฌ๋ฌ๊ฐ ๋ด์์๋, ํ๋๋ง ๋ด์์๋ ์์
    - Client ( ํด๋ผ์ด์ธํธ ) : invoker ( ๋ฐ๋์ )๋ฅผ ์ด๊ธฐํ ํ๊ณ , ๋ฐ๋์์ ์ปค๋งจ๋ ๊ฐ์ฒด๋ฅผ ๋๊ฒจ์ฃผ๋ ์ญํ 
        - ์ค์ ๋ก ์ปค๋งจ๋ ๊ฐ์ฒด๋ฅผ ์์ฑํ์ฌ invoker๋ฅผ ์ด๊ธฐํ ์์ผ์ฃผ๊ณ , ์ํํด์ฃผ๋ฉฐ invoker ๊ฐ์ฒด๋ฅผ ์ฌ์ฉํ  ์ ์๋๋ก ๋ง๋ค์ด ์ฃผ๋ ๊ณณ
- Command ๊ฐ์ฒด๋ฅผ invoker์ ์ด๋ป๊ฒ ๋ด์ ๋๋๋, ์ด๋ค ํ์์ผ๋ก ์คํํ๋๋์ ๋ฐ๋ผ ์ฌ๋ฌ๊ฐ์ง ๊ธฐ๋ฅ์ ์ฝ๊ฒ ๊ตฌํ ํ  ์ ์์
    - ์์ ์์์์ Undo ๊ธฐ๋ฅ ์ถ๊ฐ, party mode ์ถ๊ฐ ๋ฑ๋ฑ์ ํด๋น๋จ