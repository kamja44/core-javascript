# 클래스와 인스턴스

## 클래스

- 객체를 만들기 위한 청사진
  - 동일한 구조와 동작을 가지는 여러 객체를 쉽게 생성하기 위해 사용한다.
- 어떤 사물의 공통 속성을 모아 정의한 추상적인 개념

## 인스턴스

- 어떤 클래스의 속성을 지니는 실존하는 개체
  - 클래스의 구체적인 예시
- 클래스의 속성을 지니는 구체적인 사례

### 프로그래밍 언어에서의 클래스

- 공통 요소를 지니는 집단을 분류하기 위한 개념
  - 현실 세계의 클래스는 `인스턴스로부터 공통점을 발견하여 클래스를 정의`
  - 프로그래밍 언어에서의 클래스는 `클래스가 먼저 정의되어야만, 그로부터 공통적인 요소를 지니는 개체들을 생성할 수 있다.`
    - 즉, 프로그래밍 언어에서의 클래스는 사용하기에 따라 추상적인 대상일 수도 있고 구체적인 개체가 될 수도 있다.

# JS의 클래스

- JS는 프로토타입 기반 언어이기에 클래스의 개념이 존재하지 않음
  - 프로토타입을 일반적인 의미에서의 클래스 관점에서 접근해보면, 비슷하게 해석 가능
    - 생성자 함수 Array를 new 연산자와 함께 호출하면 인스턴스가 생성됨.
    - 이때 Array를 일종의 클래스라 하면, Array의 prototype 객체 내부 요소들이 인스턴스에 상속된다고 볼 수 있다.
      - 엄밀히 말하면 상속이 아닌 프로토타입 체이닝에 의한 참조(하지만 결과는 동일)
      - Array 내부 프로퍼티 중 prototype 프로퍼티를 제외한 나머지는 인스턴스에 상속되지 않는다.

## 스태틱 맴버와 인스턴스 맴버

- 인스턴스에 상속되는지(인스턴스가 참조하는지) 여부에 따라 스태틱 맴버와 인스턴스 맴버를 구분한다.
  - 인스턴스에서 직접 메서드를 정의할 수 있기에 인스턴스 메서드라는 명칭보다 `프로토타입 메서드`로 명명하는게 더 알맞음

```js
var Rectangle = function (width, height) {
  this.width = width;
  this.height = height;
}; // 생성자 부분
Rectangle.prototype.getArea = function () {
  return this.width * this.height;
}; // (프로토타입) 메서드
Regtangle.isRectangle = function (instance) {
  return (
    instance instanceof Rectangle && instance.width > 0 && instance.height > 0
  );
}; // 스태틱 메서드

var rect1 = new Rectangle(3, 4);
console.log(rect1.getArea()); // 12
console.log(rect1.isRectangle(rect1)); // Error
console.log(Rectangle.isRectangle(rect1)); // true
```

- 12번째 줄에서 Rectangle 함수를 new 연산자와 함꼐 호출해서 생성된 인스턴스를 rect1에 할당
  - width, height에 각각 3, 4가 할당됨
- 프로토타입 객체에 할당한 메서드는 인스턴스가 마치 자신의 것처럼 호출할 수 있다.
  - 13번째 줄에서 호출한 getArea는 `__proto__`를 생략하여 호출함 => this가 rect1인 채로 실행
    - 이처럼 `인스턴스에서 직접 호출할 수 있는 메서드가 프로토타입 메서드이다.`
- 14번째 줄은 rect1 인스턴스에서 isRectangle 메서드에 접근 => rect1에서 메서드 검색 => `rect1.__proto__`에서 메서드 검색 => rect1.`__proto__.__proto__`(Obejct.prototype)에도 없음 -> undefined 출력 => 함수가 아니므로 Error 발생
  - `인스턴스에서 직접 접근할 수 없는 메서드를 스태틱 메서드`라고 한다.
    - 스태틱 메서드는 15번째 줄처럼 생성자 함수를 this로 해야만 호출할 수 있다.

클래스 자체를 this로 해서 직접 접근해야만 하는 스태틱 메서드를 호출할 때의 클래스는 그 자체가 하나의 개체로서 취급된다.

# ES6의 클래스 및 클래스 상속

- JS ES6에서는 클래스가 도입되었다.

## ES5와 ES6의 클래스 문법 비교

```js
var ES5 = function (name) {
  this.name = name;
};
ES5.staticMethod = function () {
  return this.name + " staticMethod";
};
ES5.prototype.method = function () {
  return this.name + " method";
};
var es5Instance = new ES5("es5");
console.log(ES5.staticMethod()); // es5 staticMethod
console.log(es5Instance.method()); // es5 method

var ES6 = class {
  constructor(name) {
    this.name = name;
  }
  static staticMethod() {
    return this.name + " staticMethod";
  }
  method() {
    return this.name + " method";
  }
};
var es6Instance = new ES6("es6");
console.log(ES6.staticMethod()); // es6 staticMethod
console.log(es6Instance.method()); // es6 method
```

- 13번째 줄 class라는 명렁어 뒤 바로 {}가 등장한다.
  - 이 중괄호 묶음 내부가 클래스 본문 영역이다.
- 14번째 줄
  - constructor라는 이름 뒤 바로 () {가 등장한다.
  - 클래스 본문에서는 function 키워드를 생략하더라도 모두 메서드로 인식한다.
  - constructor라는 이름에서 알 수 있듯이 ES5의 생성자 함수와 동일한 역할을 수행한다.
- 16번째 줄
  - 메서드와 다음 메서드 사이에는 콤마로 구분하지 않는다.
- 17번째 줄
  - static이라는 키워드 뒤에 staticMethod라는 이름이 등장했고 뒤이어 (){가 등장한다.
  - static 키워드는 해당 메서드가 static 메서드임을 알리는 내용으로, ES5 체계에서 생성자 함수에 바로 할당하는 메서드와 동일하게 생성자 함수(클래스) 자신만이 호출할 수 있다.
- 20번째 줄
  - method라는 이름이 등장한다.
  - 이는 자동으로 prototype 객체 내부에 할당되는 메서드이다.
  - ES5.prototype.method와 동일하게, 인스턴스 프로토타입 체이닝을 통하여 마치 자신의 것처럼 호출할 수 있는 메서드이다.

## ES6의 클래스 상속

```js
var Rectangle = class {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }
  getArea() {
    return this.width * this.height;
  }
};
var Square = class extends Rectangle {
  constructor(width) {
    super(width, width);
  }
  getArea() {
    console.log(`size is : ${super.getArea()}`);
  }
};
```

- 10번째 줄
  - Square를 Rectangle 클래스를 상속받는 subClass로 만들기 위해 class 명령어 뒤에 `extends Rectangle`이라는 내용을 추가했다.
    - 이 설정으로 상속 관계 설정을 진행한다.(이게 끝)
- 12번째 줄
  - constructor 내부에서는 super라는 키워드를 함수처럼 사용할 수 있다.
    - 이 함수는 superClass의 constructor를 실행한다.
- 15번째 줄
  - constructor 메서드를 제외한 다른 메서드에서는 super 키워드를 마치 객체처럼 사용할 수 있다.
    - 이때 객체는 superClass.prototype을 바라보는데, 호출한 메서드의 this는 super가 아닌 원래의 this를 그대로 따른다.
