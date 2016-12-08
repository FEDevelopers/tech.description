> 이 글은 [https://www.sitepoint.com/object-oriented-javascript-deep-dive-es6-classes/](https://www.sitepoint.com/object-oriented-javascript-deep-dive-es6-classes/)을 번역한 글입니다.

#서문

종종 우리는 자동차 엔진, 컴퓨터 파일, 라우터, 또는 온도계같은 프로그램에서 우리의 아이디어나 개념을 표현 할 필요가 있습니다. 코드에서 이런 개념을 나타내는 것은 상태를 표현하는 데이터와 행동을 표현하는 함수 이 두가지로 나뉩니다. 클래스는 우리의 개념을 표현 할수 있는 객체의 상태와 행동을 정의하는 편리한 구문을 제공합니다. 그것들은 초기화 함수가 불러지는 것을 보장함으로써 우리의 코드를 안전하게 만들어며, 데이터를 조작하고, 유효한 상태를 유지하는 함수 집합을 쉽게 정의 할 수 있게 해줍니다. 만약 별도의 엔티티로써 *어떤'것'*을 생각한다면, 당신의 프로그램에 그 *어떤'것'*을 나타내기 위한 클래스를 정의 해야 합니다.  
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

#1. 생성자
생성자메서드는 특별하며, 위에 첫번째 문제를 해결해줍니다. 생성자는 유효한 상태로 인스턴스를 초기화 해주는 작업을 합니다. 그리고 자동으로 호출되기 때문에, 초기화 작업을 까먹을 일이 없습니다.

#2. 비공개 데이터 유지
우리는 데이터가 유효한 상태를 보장하도록 클래스를 설계하려고 노력합니다.그리고 유효한 값을 생성하는 생성자를 제공하고, 항상 유효한 값을 유지하는(남겨두는) 메소드를 설계합니다.  
그러나 우리가 모든 사람들이 접근 할 수 있는 클래스 데이터를 남겨두면, 누군가가 데이터를 망칠 것 입니다. 우리는 우리가 제공하는 함수를 통해서를 제외하고는 데이터에 접근 할 수 없도록 하여 이 문제를 해결합니다.  

> 팁:
- 비공개 데이터를 보호하는 것을 *캡슐화(encapsulation)* 라고 합니다.

##2.1 Privacy with Conventions
 불행하게도, 자바스크립트에는 객체 속성을 비공개하는 것은 없습니다. 대신 우리는 페이크를 할 수 있습니다. 이를 할 수 있는 일반적인 방법은 간단한 규칙을 고수하는 것입니다. 만약 속성 이름의 접두사(*prefix*)가 *underscore*라면,(또는 덜 흔한 방식인 접미사(*suffix*)에 underscore) 비공개로 처리해야합니다.  
 우리는 위의 코드예제에서 접두사 방식을 사용했습니다. 일반적으로 이런 간단한 규칙은 작동하지만, 사실 데이터는 기술적으로 누구나 접근 할 수 있습니다. 그래서 우리는 올바르게 사용하기 위해 훈련을 해야합니다.

##2.2 Privacy with Privileged Methods
객체 속성을 비공개로 속일 수 있는 다음으로 흔한 방법은, 생성자안에서 일반 변수를 사용하고, 클로저로 그것들을 캡쳐하는 것 입니다. 이 방법은 밖에서 접근 할 수 없는 정확한 비공개 데이터 줍니다. 하지만 이 트릭을 작동하기 위해서는 클래스의 메서드는 생성자 안에서 정의되고, 인스턴스에 붙여져야 합니다.

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

##2.3 Privacy with Symbols
`Symbol`은 자바스크립트에 새로나온 기능이며, 객체 속성을 비공개로 할 수 있는 또다른 트릭을 제공합니다. *underscore* 속성 이름 대신에, 유니크한 `symbol` 객체를 키로 사용하며, 우리 클래스에서 클로저로 그것들의 키를 캡쳐합니다. 그러나 누출이 있습니다. 자바스크립트의 또다른 새로운 기능인 `Object.getOwnPropertySymbols` 때문입니다. `Object.getOwnPropertySymbols`는 우리가 비공개로 유지하고 있는 데이터를 외부에서 접근 할 수 있게 허락해줍니다.

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

