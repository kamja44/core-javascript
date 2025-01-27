# 프로토타입

- 어떤 생성자 함수 constructor를 new 연산자와 함께 호출
- Consturctor에서 정의된 내용을 바탕으로 새로운 인스턴스가 생성된다.
- 이떄 instance에는 proto라는 프로터티가 자동으로 부여된다.
- 이 프로퍼티는 Constructor의 prototype라는 프로퍼티를 참조한다.

`prototype 객체 내부에는 인스턴스가 사용할 메서드를 저장한다.`
`인스턴스에서도 숨겨진 프로퍼티인 proto를 통하여 메서드에 접근할 수 있다.`

- 단, .proto를 이용하여 직접 접근하는 것은 권장되지 않는다.

```js
var Person = function (name) {
  this._name = name;
};
Person.prototype.getName = function () {
  return this._name;
};

var suzi = new Person("Suzi");
suzi.__proto__.getName(); // undefined
```

- Person의 인스턴스는 proto 프로퍼티를 통하여 getName을 호출할 수 있다. - instance의 proto가 Constructor의 prototype 프로퍼티를 참조하므로 결국 둘은 같은 객체를 바라보기 때문
  `Person.prototype === suzi.__proto__ // true`

- 위의 코드에서 suzi의 getName을 호출했을 떄 undefined가 출력된 이유는 this 바인딩 대상이 잘못되었기 때문
  - `suzi.__proto__.getName()`에서 getName 함수 내부에서의 this는 suzi가 아닌 `suzi.__proto__`라는 객체가 된다.
    - 해당 객체에 name 프로퍼티가 정의되어있지 않기 떄문에 Error가 아닌 undefined를 반환한다.

```js
var suzi = new Person("Suzi");
suzi.__proto__.name = "SUZI__proto__";
suzi.__proto__.getName(); // SUZI__proto__
```

- this 바인딩에 따라 값이 달라진다.
  - 해당 이슈를 간단하게 해결하는 방법은 `__proto`를 생략하는 것이다.

```js
var suzi = new Person("Suzi");
suzi.getName(); // Suzi
var iu = new Person("IU");
iu.getName(); // IU
```

- `__proto__` 프로퍼티가 생략 가능한 프로퍼티 이기에 생략이 가능하다.

`new 연산자로 Constructor를 호출하면 instance가 만들어지는데, 이 instance의 생략 가능한 프로퍼티인 __proto__는 Constructor의 prototype을 참조한다.`

- 생성자 함수의 prototype에 어떤 메서드나 프로퍼티가 있따면 인스턴스에서라도 마치 자신의 것처럼 해당 프로퍼티에 접근할 수 있다.

```js
var Constructor = function (name) {
  this.name = name;
};

Constructor.prototype.method1 = function () {};
Constructor.prototype.prototy1 = `Constructor Prototype Property`;

var instance = new Constructor("Instance");
console.dir(Constructor);
console.dir(instance);
```

## constructor 프로퍼티

- 인스턴스로부터 그 원형이 무엇인지를 알 수 있는 수단이다.

```js
var arr = [1, 2];
Array.prototype.constructor === Array; // true
arr.__proto__.constructor === Array; // true
arr.constructor === Array;

var arr2 = new arr.constructor(3, 4);
console.log(arr2); // [3, 4]
```

- 인스턴스의 proto 가 생성자 함수의 prototype 프로퍼티를 참조하며 proto 가 생략 가능하기 때문에 인스턴스에서 직접 constructor에 접근할 수 있는 수단이 생긴 것이다.

  - 즉, 6번쨰 줄과 같은 명령도 오류없이 동작한다.

- constructor를 변경하더라도 참조하는 대상이 변경되는 것 일 뿐 이미 만들어진 인스턴스의 원형이 바뀐다거나 데이터 타입이 변하는 것은 아니다.
  - 어떤 인스턴스의 생성자 정보를 알아내기 위해 constructor 프로퍼티에 의존하는게 항상 안전한 것은 아니다.

### 다양한 constructor 접근 방법

```js
var Person = function (name) {
  this.name = name;
};

var p1 = new Person("사람1");
var p1Proto = Objecgt.getPrototypeOf(p1);
var p2 = new Person.prototype.constructor("사람2");
var p3 = new p1Proto.constructor("사람3");
var p4 = new p1.__proto__.constructor("사람4");
var p5 = new p1.constructor("사람5");

[p1, p2, p3, p4, p5].forEach(function (p) {
  console.log(p, p instanceof Person);
});
```

다음 각 줄은 모두 동일한 대상을 가리킨다
`[Constructor]`
`[instance].__proto__.constructor`
`[instance].constructor`
`Object.getPrototypeOf([instance]).constructor`
`[Constructor].prototype.constructor`

다음 각 줄은 모두 동일한 객체에 접근할 수 있다.
`[Constructor].prototype`
`[instance].__proto__`
`[instance]`
`Object.getPrototypeOf([instance])`

## 프로토타입 체인

- 어떤 데이터의 proto 프로퍼티 내부에서 다시 proto 프로퍼티가 연쇄적으로 이어진 것을 프로토타입 체인이라고 한다.
- 프로토타입 체인을 따라가며 검색하는 것을 프로토타입 체이닝이라고 한다.

### 메서드 오버라이드

```js
var Person = function (name) {
  this.name = name;
};

Person.prototype.getName = function () {
  return this.name;
};

var iu = new Person("지금");
iu.getName = function () {
  return "바로" + this.name;
};

console.log(iu.getName()); // 바로 지금
```

- `iu.__proto__.getName`이 아닌 iu 객체에 있는 getName 메서드가 호출된다.

  - 해당 현상을 메서드 오버라이드라 칭한다.
    - 원본을 제거하고 다른 대상으로 교체하는 것이 아닌 원본이 그대로 있는 상태에서 다른 대상을 그 위에 얹는다.

- JS엔진이 getName이라는 메서드를 찾는 방식은 가장 가까운 대상인 자신의 프로퍼티를 검색하고 없으면, 그 다음으로 가까운 대상인 **proto\_**를 검색하는 순서로 진행된다.
  - 즉, proto 에 있는 메서드는 자신에게 있는 메서드보다 검색 우선순위에서 밀려서 호출되지 않은 것이다.
    - `iu.__proto__.getName()`과 같은 식으로 직접 접근하면 호출 가능하다.
      - 해당 코드를 실행하면 proto 에 name 프로퍼티가 없기에 undefined가 출력된다.
