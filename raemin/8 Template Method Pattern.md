# 8. Template Method Pattern

# 📚 Template Method Pattern

## 📖 Make Tea / Coffee Problem - Template method

- 커피와 홍차를 만드는 과정을 상정
- 커피와 홍차를 만들 때 과정이 매우 비슷함
    - 커피 : 물을 끓임 → 커피를 우려냄 → 컵에 따름 → 설탕 / 우유 추가
    - 홍차 : 물을 끓임 → 차를 우려냄 → 컵에 따름 → 레몬 추가
- 이때, 물을 끓인다거나 어떤 물체를 우려낸다는 것이 매우 유사함
- 따라서, 이를 공통으로 뽑아 내어 부모 컴포넌트로 만들 수 있음
    - 또, 커피와 홍차 둘다 카페인이 존재하기 때문에, 카페인 음료로 컴포넌트 명을 정함
    
    ```java
    abstract class CaffeineBeverage {
    		final void prepareRecipe() {
    				boilWater();
    				brew();
    				pourInCup();
    				addCondiments();
    		}
    
    		abstract void brew();
    		abstract void addCondiments();
    
    		void boilWater() {
    				// 구체 구현
    		}
    
    		void pourInCup() {
    				// 구체 구현
    		}
    }
    ```
    
    ```java
    class Tea extends CaffeineBeverage {
    		void brew() {
    				// 구체 구현
    		}
    
    		void addCondiments() {
    				// 구체 구현
    		}
    }
    ...
    ```
    
    - 위와 같이 구현하면, `prepareRecipe` 호출 시 각 구현체 ( Tea / Coffee )에 맞도록 동작
    - 또, `prepareReceipe` 를 final로 선언 해 놓음으로써 서브클래스에서 override 하여 잘못 사용되는일을 방지
    - 여기서 `prepareRecipe` 를 템플릿 메소드라고 명명
- 템플릿 메소드란, 알고리즘의 각 단계를 정의하며 그중 한개 이상의 단계가 서브클래스에 의해 구현 될 수 있음
- 템플릿 메소드를 적용했을때의 장점
    - 음료를 만드는 알고리즘이 고정되어 실수가 줄어들고, 수정시 수정할 곳이 명확해짐
    - 코드 재사용성이 높아짐
    - 다른 카페인 음료도 쉽게 추가할 수 있음

## 📖 Hook method in Template Method

- Template method 안에 Hook 함수를 두어 Template method의 알고리즘을 서브클래스에서 수정하도록 할 수 있음
    
    ```java
    abstract class CaffeineBeverage {
    		final void prepareRecipe() {
    				boilWater();
    				brew();
    				pourInCup();
    				if (hookBoolean()) {
    						addCondiments();
    				}
    				hook();
    		}
    
    		abstract void brew();
    		abstract void addCondiments();
    
    		void boilWater() {
    				// 구체 구현
    		}
    
    		void pourInCup() {
    				// 구체 구현
    		}
    
    		void hook() {}
    
    		boolean hookBoolean() {
    				return true;
    		}
    }
    ```
    
    - hookBoolean 또는 hook 함수를 서브클래스에서 override 하여 템플릿 메소드 내부의 알고리즘 흐름을 변경 할 수 있음

## 📖 소개된 디자인 패턴 원칙 정리

### 템플릿 메소드 패턴

<aside>
💡 템플릿 메소드에서 알고리즘의 골격을 정의하고, 템플릿 메소드 내의 알고리즘 단계 중 일부는 서브클래스에서 구현하도록 위임하는 것

</aside>

- 알고리즘의 틀을 만들어 재사용성과 유지보수성을 높임
- 서브클래스에서 일부분을 구현하게 놔두지만, 알고리즘의 구조는 바뀌지 않을 수 있음
- override 가능한 hook 함수를 두어 템플릿 메소드 내의 기능을 서브클래스에서 변경하기도 함

### 헐리우드 원칙

<aside>
💡 저수준 컴포넌트 에서는 고수준 컴포넌트를 호출 할 수 없게 하고, 고수준 컴포넌트에서는 저수준 컴포넌트가 필요 할 때 호출하도록 하는 것

</aside>

- 위의 Caffeine 드링크 예시에서, 저수준 컴포넌트가 `boilWater` / `pourInCup` 을 구현하지만, 이를 사용하는곳은 고수준 컴포넌트
- 즉, 저수준 컴포넌트에 구현은 되어 있지만, 실제 사용은 고수준 컴포넌트에서만 진행하는 것