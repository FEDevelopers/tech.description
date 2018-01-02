> 이 글은 [https://www.sitepoint.com/object-oriented-javascript-deep-dive-es6-classes/](https://www.sitepoint.com/object-oriented-javascript-deep-dive-es6-classes/)을 번역한 글입니다.

# 목차
1. [생성자](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5-Javascript---Class#1-%EC%83%9D%EC%84%B1%EC%9E%90)
2. [비공개 데이터 유지](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5-Javascript---Class#2-%EB%B9%84%EA%B3%B5%EA%B0%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%9C%A0%EC%A7%80)
3. [현재 객체 참조](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5-Javascript---Class#3-%ED%98%84%EC%9E%AC-%EA%B0%9D%EC%B2%B4-%EC%B0%B8%EC%A1%B0)
4. [정적 속성과 메서드](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5-Javascript---Class#4-%EC%A0%95%EC%A0%81-%EC%86%8D%EC%84%B1%EA%B3%BC-%EB%A9%94%EC%84%9C%EB%93%9C)
5. [서브클래스's](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5-Javascript---Class#5-%EC%84%9C%EB%B8%8C%ED%81%B4%EB%9E%98%EC%8A%A4s)
6. [중복을 피하기 위한 상속](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5-Javascript---Class#6-%EC%A4%91%EB%B3%B5%EC%9D%84-%ED%94%BC%ED%95%98%EA%B8%B0-%EC%9C%84%ED%95%9C-%EC%83%81%EC%86%8D)
7. [대체 서브클래스 상속](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5-Javascript---Class#7-%EB%8C%80%EC%B2%B4-%EC%84%9C%EB%B8%8C%ED%81%B4%EB%9E%98%EC%8A%A4-%EC%83%81%EC%86%8D)
8. [설탕 보다 더한것](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5-Javascript---Class#8-%EC%84%A4%ED%83%95%EB%B3%B4%EB%8B%A4-%EB%8D%94%ED%95%9C-%EA%B2%83sugar)
9. [창의적인 방식으로 새로운 기능 사용하기](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5-Javascript---Class#9-%EC%B0%BD%EC%9D%98%EC%A0%81%EC%9D%B8-%EB%B0%A9%EC%8B%9D%EC%9C%BC%EB%A1%9C-%EC%83%88%EB%A1%9C%EC%9A%B4-%EA%B8%B0%EB%8A%A5-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)
10. [팩토리 클래스로 다중상속](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5-Javascript---Class#10-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%8C%A9%ED%86%A0%EB%A6%AC%EB%A1%9C-%EB%8B%A4%EC%A4%91%EC%83%81%EC%86%8D)
11. [결론](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5-Javascript---Class#11-%EA%B2%B0%EB%A1%A0)


# 서문

종종 우리는 자동차 엔진, 컴퓨터 파일, 라우터, 또는 온도계같은 프로그램에서 우리의 아이디어나 개념을 표현 할 필요가 있습니다. 코드에서 이런 개념을 나타내는 것은 상태를 표현하는 데이터와 행동을 표현하는 함수 이 두가지로 나뉩니다. 클래스는 우리의 개념을 표현 할수 있는 객체의 상태와 행동을 정의하는 편리한 구문을 제공합니다. 그것들은 초기화 함수가 불러지는 것을 보장함으로써 우리의 코드를 안전하게 만들고, 데이터를 조작하고, 유효한 상태를 유지하는 함수 집합을 쉽게 정의 할 수 있게 해줍니다. 만약 별도의 엔티티로써 *어떤'것'*을 생각한다면, 당신의 프로그램에 그 *어떤'것'*을 나타내기 위한 클래스를 정의 해야 합니다.  
아래 클래스 코드가 아닌 것을 보십시오. 얼마나 많은 오류를 찾을 수 있겠습니까? 그럼 어떻게 그것을 해결 하겠습니까?

``` javascript
// set today to December 24
let today = {
  month: 12,
  day: 24,
};

let tomorrow = {
  year: today.year,
  month: today.month,
  day: today.day + 1,
};

let dayAfterTomorrow = {
  year: tomorrow.year,
  month: tomorrow.month,
  day: tomorrow.day + 1 <= 31 ? tomorrow.day + 1 : 1,
};
```

`today` 날짜는 유효하지 않습니다. 또한 `today`는 년도(`year`)가 빠져서 완전하게 초기화 되지 않았습니다. 만약 까먹지 않게 초기화 할 수 있는 함수가 있었으면 좋을 것 같습니다. 그리고 하루를 추가할 때, 우리는 한 곳(함수)에서 *day*가 31이 넘어가는지 체크를 합니다. 하지만 다른 곳에서 체크 되는 것을 놓쳤습니다. (but missed that check in another place.) 각각의 유효한 상태를 유지하고 있는 작고 고정된 함수셋을 통해 데이터와 상호작용하면 더 좋을 것 입니다.  

다음은 클래스를 사용한 수정된 버전입니다.

``` javascript
class SimpleDate {
  constructor(year, month, day) {
    // Check that (year, month, day) is a valid date
    // ...

    // If it is, use it to initialize "this" date
    this._year = year;
    this._month = month;
    this._day = day;
  }

  addDays(nDays) {
    // Increase "this" date by n days
    // ...
  }

  getDay() {
    return this._day;
  }
}

// "today" is guaranteed to be valid and fully initialized
let today = new SimpleDate(2000, 2, 28);

// Manipulating data only through a fixed set of functions ensures we maintain valid state
today.addDays(1);
```

> 팁:
- 클래스나 객체에 함수가 할당되면, 그것은 `method`라고 불립니다.
- 클래스에서 객체가 생성되면, 클래스의 인스턴스(instance)라고 불립니다.

# 1. 생성자
생성자메서드는 특별하며, 위에 첫번째 문제를 해결해줍니다. 생성자는 유효한 상태로 인스턴스를 초기화 해주는 작업을 합니다. 그리고 자동으로 호출되기 때문에, 초기화 작업을 까먹을 일이 없습니다.

# 2. 비공개 데이터 유지
우리는 데이터가 유효한 상태를 보장하도록 클래스를 설계하려고 노력합니다.그리고 유효한 값을 생성하는 생성자를 제공하고, 항상 유효한 값을 유지하는(남겨두는) 메소드를 설계합니다.  
그러나 우리가 모든 사람들이 접근 할 수 있는 클래스 데이터를 남겨두면, 누군가가 데이터를 망칠 것 입니다. 우리는 우리가 제공하는 함수를 통해서를 제외하고는 데이터에 접근 할 수 없도록 하여 이 문제를 해결해야 합니다.  

> 팁:
- 비공개 데이터를 보호하는 것을 *캡슐화(encapsulation)* 라고 합니다.

## 2.1 Privacy with Conventions
 불행하게도, 자바스크립트에는 객체 속성을 비공개하는 방법은 없습니다. 대신 우리는 페이크를 할 수 있습니다. 이를 할 수 있는 일반적인 방법은 간단한 규칙을 고수하는 것입니다. 만약 속성 이름의 접두사(*prefix*)가 *underscore*라면,(또는 덜 흔한 방식인 접미사(*suffix*)에 underscore) 비공개로 처리해야합니다.  
 우리는 위의 코드 예제에서 접두사 방식을 사용했습니다. 일반적으로 이런 간단한 규칙은 작동하지만, 사실 데이터는 기술적으로 누구나 접근 할 수 있습니다. 그래서 우리는 올바르게 사용하기 위한 훈련을 해야합니다.

## 2.2 Privacy with Privileged Methods
객체 속성을 비공개로 속일 수 있는 다른 흔한 방법은, 생성자 안에서 일반 변수를 사용하고, 클로저로 그것들을 캡쳐하는 것 입니다. 이 방법은 밖에서 접근 할 수 없는 정확한 비공개 데이터를 줍니다. 하지만 이 트릭을 작동하기 위해서는 클래스의 메서드는 생성자 안에서 정의되고, 인스턴스에 붙여져야 합니다.

``` javascript
class SimpleDate {
  constructor(year, month, day) {
    // Check that (year, month, day) is a valid date
    // ...

    // If it is, use it to initialize "this" date's ordinary variables
    let _year = year;
    let _month = month;
    let _day = day;

    // Methods defined in the constructor capture variables in a closure
    this.addDays = function(nDays) {
      // Increase "this" date by n days
      // ...
    }

    this.getDay = function() {
      return _day;
    }
  }
}
```

## 2.3 Privacy with Symbols
`Symbol`은 자바스크립트에 새로나온 기능이며, 객체 속성을 비공개로 할 수 있는 또다른 트릭을 제공합니다. *underscore* 속성 이름 대신에, 유니크한 `symbol` 객체를 키로 사용하며, 우리 클래스에서 클로저로 그것들의 키를 캡쳐합니다. 하지만 이 또한 문제가 있습니다. 자바스크립트의 또다른 새로운 기능인 `Object.getOwnPropertySymbols` 때문입니다. `Object.getOwnPropertySymbols`는 우리가 비공개로 유지하고 있는 데이터를 외부에서 접근 할 수 있게 허락해줍니다.

``` javascript
let SimpleDate = (function() {
  let _yearKey = Symbol();
  let _monthKey = Symbol();
  let _dayKey = Symbol();

  class SimpleDate {
    constructor(year, month, day) {
      // Check that (year, month, day) is a valid date
      // ...

      // If it is, use it to initialize "this" date
      this[_yearKey] = year;
      this[_monthKey] = month;
      this[_dayKey] = day;
    }

    addDays(nDays) {
      // Increase "this" date by n days
      // ...
    }

    getDay() {
      return this[_dayKey];
    }
  }

  return SimpleDate;
}());
```

## 2.4 Privacy with Weak Maps
`Weak.map` 또한 자바스크립트의 새로운 기능입니다. 우리의 인스턴스를 `key`로 사용하여 `key/value`쌍으로 우리의 객체 속성을 비공개로 저장할 수 있습니다. 그리고 클래스는 `key/value map`을 클로저로 캡쳐 할 수 있습니다.

``` javascript
let SimpleDate = (function() {
  let _years = new WeakMap();
  let _months = new WeakMap();
  let _days = new WeakMap();

  class SimpleDate {
    constructor(year, month, day) {
      // Check that (year, month, day) is a valid date
      // ...

      // If it is, use it to initialize "this" date
      _years.set(this, year);
      _months.set(this, month);
      _days.set(this, day);
    }

    addDays(nDays) {
      // Increase "this" date by n days
      // ...
    }

    getDay() {
      return _days.get(this);
    }
  }

  return SimpleDate;
}());
``` 

## 2.5 Other Access Modifiers
`protected`, `internal`, `package private`, `friend`와 같은 다른 언어에서 `private`외에 다른 단계의 가시성이 있습니다.(There are other levels of visibility besides “private” that you’ll find in other languages, such as “protected”, “internal”, “package private”, or “friend”.) 자바스크립트는 여전히 다른 수준의 가시성을 강화하는 방법을 제공하지 않습니다. 만약 이런것들이 필요하다면, 규칙이나, 자신의 훈련을 통해 의존 해야 합니다.

# 3. 현재 객체 참조
위 `getDay()`를 다시 보면 매개변수를 지정하지 않았는데, 어떻게 호출 되는 객체를 알 수 있습니까?  
함수가 `object.function` 표기를 사용하는 메소드로 호출되면, 객체를 식별하기 위해 사용하는 암시적 인자(*argument*)가 있습니다. 이 암시적 인자는 `this`라는 암시적 매개변수에 할당 됩니다.  
이를 설명하기 위해 아래, 명시적으로 객체 인자를 전달 하는 방법이 있습니다.

``` javascript
// Get a reference to the "getDay" function
let getDay = SimpleDate.prototype.getDay;

getDay.call(today); // "this" will be "today"
getDay.call(tomorrow); // "this" will be "tomorrow"

tomorrow.getDay(); // same as last line, but "tomorrow" is passed implicitly
```

# 4. 정적 속성과 메서드
우리는 클래스 일부분 이면서 해당 클래스 인스턴스의 부분이 아닌 데이터와 함수를 정의 할 수 있는 옵션이 있습니다.  
각 인스턴스를 새롭게 복사하기 보단 정적 속성 복사본 하나만 갖고 있을 것입니다.

``` javascript
class SimpleDate {
  static setDefaultDate(year, month, day) {
    // A static property can be referred to without mentioning an instance
    // Instead, it's defined on the class
    SimpleDate._defaultDate = new SimpleDate(year, month, day);
  }

  constructor(year, month, day) {
    // If constructing without arguments,
    // then initialize "this" date by copying the static default date
    if (arguments.length === 0) {
      this._year = SimpleDate._defaultDate._year;
      this._month = SimpleDate._defaultDate._month;
      this._day = SimpleDate._defaultDate._day;

      return;
    }

    // Check that (year, month, day) is a valid date
    // ...

    // If it is, use it to initialize "this" date
    this._year = year;
    this._month = month;
    this._day = day;
  }

  addDays(nDays) {
    // Increase "this" date by n days
    // ...
  }

  getDay() {
    return this._day;
  }
}

SimpleDate.setDefaultDate(1970, 1, 1);

let defaultDate = new SimpleDate();
```

# 5. 서브클래스's
종종 우리는 통합 하려는 클래스들 사이에 반복코드를 보게 됩니다. **서브클래스**는 다른 클래스들의 상태나 행동들을 우리 자신의 클래스내로 통합시켜줍니다. 이런 과정을 우리는 **상속(*inheritance*)**이라고 부릅니다. 그리고 서브클래스는 부모클래스나 슈퍼클래스로부터 **상속받다** 라고 말합니다. **상속**은 중복을 피하게 해주고, 다른 클래스와 동일한 데이터나 함수가 필요한 클래스의 구현을 단순화 시켜줍니다. 또한 **상속**은 공통 슈퍼클래스가 제공하는 인터페이스에 의존하여 서브클래스를 대체 하게 해줍니다.

# 6. 중복을 피하기 위한 상속
다음은 상속을 사용하지 않은 예제입니다.

``` javascript
class Employee{
	constructor(firstName, familyName){
		this._firstName = firstName;
		this._familyName = familyName;
	}

	getFullName(){
		return `${this._firstName} ${this._familyName}`;
	}
}

class Manager{
	constructor(firstName, familyName){
		this._firstName = firstName;
		this._familyName = familyName;
		this._managedEmployees = [];
	}

	getFullName(){
		return `${this._firstName} ${this._familyName}`;
	}

	addEmployee(employee){
		this._managedEmployees.push(employee);
	}
}
```

`_firstName`과 `_familyName` 속성과 `getFullName` 메서드는 클래스내에서 반복적으로 사용되고 있습니다. 우리는 `Manager`클래스가 `Employee`클래스를 상속받게 함으로써 중복을 제거 할 수 있습니다. 우리가 상속을 진행하면, `Employee`클래스의 상태와 행동은 `Manager`클래스에 통합됩니다.

다음은 상속을 사용한 예제 입니다. `super`를 사용한 것에 주목하십시오.

``` javascript
class Employee{
	constructor(firstName, familyName){
		this._firstName = firstName;
		this._familyName = familyName;
	}

	getFullName(){
		return `${this._firstName} ${this._familyName}`;
	}
}

class Manager extends Employee{
	constructor(firstName, familyName){
		super(firstName, familyName);
		this._managedEmployees = [];
	}

	addEmployee(employee){
		this._managedEmployees.push(employee);
	}
}
```

## 6.1 IS-A 와 WORKS-LIKE-A
상속을 하기 위해 적절한 때를 결정하기 위한 좋은 디자인 법칙이 있습니다. 상속은 항상 *IS-A* 와 *WORKS-LIKE-A* 관계 모델링을 해야 합니다. 즉 `Manager` 는(*IS-A*) 특정 종류의 `Employee` 와 같이 일한다 이며(*works like a*), 슈퍼클래스 인스턴스에서 작동하며, 서브클래스 어디서든 대체 할 수 있어서 모든것이 잘 작동 해야 합니다.  
위와 같은 원칙을 준수하거나, 위반 하는 차이는 때때로 미묘 할 수 있습니다. 미묘하게 위반하는 예는 `Rectangle` 수퍼클래스에 `Square` 서브클래스입니다.

``` javascript
class Rectangle{
  set width(w){
    this._width = w;
  }

  get width(){
    return this._width;
  }

  set height(h){
    this._height = h;
  }

  get height(){
    return this._height;
  }
}

function f(rectangle){
  rectangle.width = 5;
  rectangle.height = 4;

  if(rectangle.width * rectangle.height !== 20){
    throw new Error("expected the rectangle's area");
  }
}
// square 는(is-a) rectangle 이 올바른가요?
class Square extends Rectangle{
  set width(w){
    super.width = w;
    super.height = w;
  }

  set height(h){
    super.height = h;
    super.width = h;
  }
}
// square의 대체제로 rectangle을 할 수 있을까요?
f(new Square()); //error
```

정사각형은 수학적으로는 사각형일 수 있습니다. 그러나 정사각형은 사각형처럼 동작하지 않습니다.  
수퍼클래스 인스턴스 사용하는 경우 서브클래스 인스턴스는 슈퍼클래스 인스턴스로 대체 되어질 수 있어야 한다는 법칙을 **[리스코프 치환 법칙(LSP)](https://en.wikipedia.org/wiki/Liskov_substitution_principle)** 이라고 부릅니다. 이것은 OOP 디자인의 중요한 부분입니다.  

## 6.2 Beware Overuse
어디서나 흔한 공통점을 찾을 수 있으며, 완벽한 기능을 갖춘 class를 제공하는 것은 숙련된 개발자들에게도 매력적일 수 있습니다. 그러나 상속 또한 단점이 있습니다.  
우리는 작고 고정된 함수 집합을 통해 데이터를 조작하여 유효한 상태를 보장한다는 점을 상기 해야 합니다. 그러나 우리가 상속을 사용하면, 데이터를 직접적으로 조작해야하는 함수 목록들이 늘어나고, 이러한 추가된 함수집합들도 유효한 상태를 유지해야 할 책임이 있습니다. 만약 데이터를 직접적으로 조작할 수 있는 함수들이 많으면, 이것은 전역변수만큼 나빠질 수 있습니다. 지나친 상속은 캡슐화를 희석시키고, 수정하기가 더 어렵고, 재사용이 어려워지는 단단한 클래스를 만들어버립니다. 그래서 하나의 컨셉만 가지고 있는 미니멀한 클래스를 디자인 하기를 추천합니다.  
코드 중복 문제에 대해 다시 한번 살펴보겠습니다. 우리는 상속없이 해결 할 수 있을까요? 상속없이 해결하기 위한 방법으로 객체를 참조로 연결하는 `부분-전체` 관계로 표현 하는 것입니다. 우리는 이것을 **구성**(*composition*) 이라고 부릅니다.  
다음은 상속이 아닌 *Composition*으로 `manager-employee`를 구현한 버전입니다.

``` javascript
class Employee{
  constructor(firstName, familyName){
    this._firstName = firstName;
    this._familyName = familyName;
  }

  getFullName(){
    return `${this._firstName} ${this._familyName}`;
  }
}

class Group{
  constructor(manager /* : Employee*/){
    this._manager = manager;
    this._managedEmployees = [];
  }

  addEmployee(employee){
    this._managedEmployees.push(employee);
  }
}
```

여기서 `manager`는 별도의 클래스가 아닙니다. 대신에 `manager`는 `Group`인스턴스가 참조를 소유하는 일반 `Employee` 인스턴스 입니다. 만약 상속 모델이 `IS-A`관계라면,*Composition*모델은 `HAS-A`관계입니다. 즉 **`Group`에는 `manager`가 있습니다** 라고 할 수 있습니다.  
만약 상속 이나 *Composition*이 우리 프로그램안에 컨셉과 관계를 합리적으로 표현 할 수 있다면, *Composition*을 선호하십시오.

# 7. 대체 서브클래스 상속
또한 상속은 공통 슈퍼클래스가 제공하는 인터페이스로 서로 다른 서브클래스를 교환하여 사용 할 수 있게 해줍니다. 슈퍼클래스 인스턴스를 인수로 사용하는 함수는 함수가 서브클래스에 대해 알 필요 없는 서브클래스 인스턴스를 전달 할 수 있습니다. 공통 슈퍼클래스를 가진 클래스를 대체하는 것을 다형성(*polymorphism*)이라고 부릅니다.

``` javascript
// 공통 슈퍼클래스
class Cache{
  get(key, defaultValue){
    let value = this._doGet(key);
    if(value === undefined || value === null){
      return defaultValue;
    }
  }

  set(key, value){
    if(key === undefined || key === null){
      throw new Error('invalid argument');
    }
    this._doSet(key, value);
  }

  // overriden
  // _doGet()
  // _doSet()
}

// 서브클래스는 새로운 public 메서드를 정의하지 않습니다.
// public 인터페이스는 슈퍼클래스에 정의되어있습니다.
class ArrayCache extends Cache{
  _doGet(){
    // ...
  }

  _doSet(){
   // ...
  }
}

class LocalStorageCache extends Cache{
  _doGet(){
    // ...
  }

  _doSet(){
    // ...
  }
}

class LocalStorageCache extends Cache{
  _doGet(){
    // ...
  }

  _doSet(){
    // ...
  }
}

//함수는 슈퍼클래스 인터페이스를 통하여 상호작용하여 모든 캐시에서 다형성으로 작동 할 수 있습니다.
function compute(cache){
  let cached = cache.get('result');
  if(!cached){
    let result = // ...
    cache.set('result', result);
  }

  // ...
}

compute(new ArrayCache());
compute(new LocalStorageCache());
```

# 8. 설탕보다 더한 것(sugar!)
자바 스크립트의 클래스 구문은 많은면 에서 문법적 설탕이라고 하지만, 실제 차이점도 있습니다. ES5로는 할 수없는 ES6 클래스로만 할 수있는 것들입니다.

## 8.1 Static Properties Are Inherited
ES5는 생성자 함수 사이에 진짜 상속을 만들지 않았습니다. `Object.create` 는 일반 객체를 생성 할 수 있지만, 함수 객체는 아닙니다. 정적 속성을 수동으로 복사하여 상속하는 가짜입니다. 이제 ES6 클래스를 사용하여 서브클래스 생성자 함수와 슈퍼클래스 생성자 사이에 실제 프로토타입 연결해봅시다.

``` javascript
//ES5
function B(){}
B.f = function(){};

function D(){}
D.prototype = Object.create(B.prototype);

D.f(); //error
```
``` javascript
//ES6
class B{
  static f(){}
}

class D extends B {}
D.f(); //ok
```

## 8.2 Built-in Constructors Can Be Subclassed
일부 객체는 특이하지만 일반 객체처럼 행동 하진 않습니다. 예를들어, 배열은 길이 속성을 최대 정수 인덱스보다 더 크게 조정합니다. ES5에서 배열로 하위 클래스를 만들려고 할 때, `new` 연산자는 우리의 하위 클래스의 `Array` 진짜 객체가 아니라 하위 클래스에 일반 객체를 할당할 것입니다.

``` javascript
//es5
function D(){
  Array.apply(this, arguments);
}

D.prototype = Object.create(Array.prototype);

var d = new D();
d[0] = 42;

d.length; // 0 - bad, no array exotic behavior
```

ES6 클래스는 객체가 언제, 누구에 의해 할당 되어지는지를 변경하여 수정합니다. ES5 객체는 서브 클래스 생성자가 호출 되기전에 할당되었으며, 서브 클래스는 수퍼클래스 생성자에 해당 객체를 전달 하였습니다.  
하지만 ES6 클래스는 수퍼클래스 생성자가 호출 되기전에 객체가 할당 되어지며, 수퍼클래스는 해당 객체를 서브 클래스에서 사용할 수 있게 만들어줍니다. 이대로 한다면, 우리의 `new` 연산자로 서브클래스를 호출 하면, `Array` 진짜 객체를 할당할 수 있게 해줍니다.

``` javascript
//es6
class D extends Array{}
let d = new D();
d[0] = 42;
d.length; // 1 - good, array exotic behavior
```

## 8.3 Miscellaneous
다른 종류의 잡다한 구색이 있지만, 아마도 덜 중요한 차이 일 것 입니다. 클래스 생성자는 함수 호출을 할 수 없습니다. 이는 `new`와 함께 생성자를 호출 하는 것을 잊어버리지 않게 하기 위한 보호입니다. 또한 클래스 생성자의 프로토타입 속성을 재할당 할 수 없습니다. 이는 자바스크립트 엔진이 클래스 객체를 최적화 하는데 도움이 됩니다. 마지막으로 클래스 메서드는 프로토타입 속성을 가질 수 없습니다. 이러면 불필요한 객체를 제거함으로써 메모리를 절약 할 수 있습니다.

# 9. 창의적인 방식으로 새로운 기능 사용하기
여기에 설명된 많은 기능과 다른 SitePoint 기사는 자바스크립트에 익숙치 않으며, 커뮤니티에서 창의적인 방식으로 새로운 기능을 사용하기 위해 여러 실험을 하고 있습니다.

## 9.1 Multiple Inheritance with Proxies
다양한 실험중 하나는 다중 상속을 구현하기 위해 자바스크립트 새로운 기능인 `proxies` 를 사용하는 것입니다.  
자바스크립트 프로토타입 체인은 단일 상속만 허용합니다. 객체는 하나의 다른 객체에만 위임할 수 있습니다. 하지만 `Proxies`를 이용하면, 여러 다른 객체 속성에 접근 권한을 위임 할 수 있습니다.

``` javascript
let transmitter = {
  transmit(){}
};

let receiver = {
  receive(){}
};

// Create a proxy object that intercepts property accesses and forwards to each parent,
// returning the first defined value it finds
let inheritsFromMultiple = new Proxy([transmitter, receiver], {
  get: function(proxyTarget, propertyKey){
    const foundParent = proxyTarget.find(parent => parent[propertyKey] !== undefined);
    return foundParent && foundParent[propertyKey];
  }
});

inheritsFromMultiple.transmit();// works
inheritsFromMultiple.receive(); // works
```

그렇다면 proxy를 클래스와 함께 작동 하도록 할 수 있을까요? 클래스 프로토타입은 여러 다른 프로토타입에 액세스를 전달하는 프록시 일 수 있습니다.  
자바스크립트 커뮤니티는 이 작업을 진행중에 있습니다. 당신이 할 수 있다면, 토론에 참가하여 당신의 아이디어를 공유해주세요.

# 10. 클래스 팩토리로 다중상속
자바스크립트 커뮤니티가 실험한 다른 접근 방식으로 다양한 슈퍼클래스를 확장하기 위해 클래스 생성을 주문하는 것입니다.
각 클래스는 단일 부모클래스 체계 이지만, 흥미로운 방식으로 각 부모 클래스를 연결 할 수 있습니다.

``` javascript
function makeTransmitterClass(Superclass = Object){
  return class Transmitter extends Superclass{
    transmit(){}
  };
}

function makeReceiverClass(Superclass = Object){
  return class Receiver extends Superclass{
    receive(){}
  };
}

class InheritsFromMultiple extends makeTransmitterClass(makeReceiverClass()){}

let inheritsFromMultiple = new InheritsFromMultiple();

inheritsFromMultiple.transmit();// works
inheritsFromMultiple.receive(); // works
```

# 11. 결론
이 글을 통해 ES6에서 클래스가 어떻게 작동하는지와 우리 주변에 둘러싸인 용어에 대한 인사이트를 줄 수 있길 바랍니다. 불행하게도 이 글을 쓰는 시점에 클래스에 대한 지원이 부족하여, `transpiler`나 `Babel`을 사용해야 합니다.(역자주: 현재는 상관없는 이야기입니다.) 이럼에도 불구하고, 나는 클래스에 대한 당신의 생각과 이것들이 ES6의 특징인지 에 대해 이야기를 나누고 싶습니다.
