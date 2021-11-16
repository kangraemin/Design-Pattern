# 5. Singleton Pattern

# 📚 Singleton Pattern

## 📖 Chocolate factory - Singleton Pattern

- 초콜릿을 녹이는 초콜릿 보일러가, 비어있으면 채우는 작업을 하는 클래스가 있다고 가정
- 객체를 처음 만든 직후의 상태는 `empty` 변수 값이 true
- 객체를 만들고 난 이후 `empty` 변수를 수정하는 형식으로 개발을 진행
- 이때, 이 초콜릿 보일러 객체가 두개가 돌아가는 경우, `empty` 변수의 초기화 때문에 공장 전체가 의도치 않게 돌아갈 수 있음
- 따라서 이 초콜릿 보일러 객체는 하나만 존재 해야 함
- 이를 위해 객체를 하나만 만드는 디자인 패턴인 싱글턴 디자인 패턴을 적용
    
    ```java
    class Singleton {
    		private voiatile static Singleton instance;
    
    		private Singleton()
    
    		static Singleton getInstance() {
    				if (instance == null) {
    						synchronized (Singleton.class) {
    								if (instance == null) {
    										instance = Singleton();
    								}
    						}
    				}
    				return instance;
    		}
    }
    ```
    
    - `synchronized` / `volatile` 키워드를 활용하여 멀티쓰레드 환경에서도 객체가 하나만 생성되도록 선언

## 📖 소개된 디자인 패턴 원칙 정리

### 싱글턴 패턴

<aside>
💡 해당 클래스의 객체가 하나만 만들어지고, 어디서든지 그 객체에 접근 할 수 있도록 하는 디자인 패턴

</aside>

- 클래스의 객체 생성을 외부에서 못하도록 해야 함 → 생성자에 private 접근제한자 적용
- 싱글턴 객체를 lazy 하게 생성 할 수 있음 → 객체가 필요 할 때 객체가 생성 되어 있지 않다면 그때 객체 생성
- 다중 쓰레드를 활용하는 경우, 객체를 여러개 생산 하는 문제점이 있을 수 있음
- 따라서 syncronized 키워드를 활용하여 객체 생성하는 부분을 동기화
- 또는 클래스를 선언 할 때, 객체를 생성 해놓는 방법이 있음
- 하지만 클래스를 선언 할 때 객체를 생성해 놓는다면, 그 객체가 사용되지 않는 동안에도 계속해서 GC되지 않고 남아이는 문제점이 있음