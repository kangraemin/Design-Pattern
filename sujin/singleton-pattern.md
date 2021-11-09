# 싱글턴 패턴

싱글톤 패턴(singleton)은 전체 시스템에서 클래스의 인스턴스를 하나만 존재하도록 하는 패턴이다. 보통 객체를 만들 때 하나의 생성자로 여러 개의 서로 다른 객체를 만들 수 있다. 그러나 싱글톤에서는 단 하나의 객체만 존재하는 것이 보장된다.

가장 간단한 싱글톤 예제는 아래와 같이 객체 리터럴을 이용하는 것이다.

```jsx
const plus = {a: 1, b: 2};
const minus = {a: 1, b: 2};
```

동일한 키와 값을 똑같이 지정했더라도 참조하는 주소값이 다르기 때문에

plus와 minus는 각각 유일하게 존재하는 서로 다른 객체이다.

이렇게 객체 리터럴로 사용하지 않고 비공개된 프로퍼티나 함수를 정의하고 싶다면? 비공개 프로퍼티와 함수를 정의하려면 클로저(closure)를 사용해야 한다. 아래처럼 IIFE(즉시 실행 함수)로 비공개 변수를 가질 수 있게 만들어주면 된다.

<aside>
💡 **클로저**(**closure**)란, 외부 함수에 접근할 수 있는 내부 함수 혹은 이러한 원리를 일컫는 용어인데 스코프에 따라서 내부함수의 범위에서는 외부 함수 범위에 있는 변수에 접근이 가능하지만 그 반대는 실현이 불가능하다는 개념이다.

</aside>

```jsx
const SingletonClass = (function() {
  let instance;

  function init(){ // 싱글톤 객체를 리턴할 비공개 함수
    return {
      publictMethod: function() {
        return 'public method';
      },
      publicProp: 'public variable',
    };
  }

  return {
    getInstance: function() {
      if (instance) {
        return instance; // 있으면 그냥 반환
      }
      instance = init();
      return instance; // 없으면 객체 생성 후 반환 (이해를 위해 명시적으로 나눔)
    }
  };
})();

const a = SingletonClass.getInstance();
console.log(a.publicProp, 'a'); // 'public variable'

const b = SingletonClass.getInstance();
console.log(a === b) // true
```