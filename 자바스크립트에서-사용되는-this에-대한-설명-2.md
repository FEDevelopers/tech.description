> 이 문서는 https://rainsoft.io/gentle-explanation-of-this-in-javascript/ 를 번역한 내용입니다.

## 목차


1. [this에 대한 미스터리](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#1-this%EC%97%90-%EB%8C%80%ED%95%9C-%EB%AF%B8%EC%8A%A4%ED%84%B0%EB%A6%AC)
1. [함수 실행](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#2-%ED%95%A8%EC%88%98-%EC%8B%A4%ED%96%89)
  1. [함수 실행에서의 this](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#22-%EC%97%84%EA%B2%A9-%EB%AA%A8%EB%93%9C%EC%97%90%EC%84%9C-%ED%95%A8%EC%88%98-%EC%8B%A4%ED%96%89%EC%97%90%EC%84%9C%EC%9D%98-this)
  1. [엄격 모드에서 함수 실행에서의 this](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#22-%EC%97%84%EA%B2%A9-%EB%AA%A8%EB%93%9C%EC%97%90%EC%84%9C-%ED%95%A8%EC%88%98-%EC%8B%A4%ED%96%89%EC%97%90%EC%84%9C%EC%9D%98-this)
  1. [실수: 내부 함수에서의 this를 사용할 때](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#23-%EC%8B%A4%EC%88%98-%EB%82%B4%EB%B6%80-%ED%95%A8%EC%88%98%EC%97%90%EC%84%9C%EC%9D%98-this%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%A0-%EB%95%8C)
1. [메소드 실행](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#3-%EB%A9%94%EC%86%8C%EB%93%9C-%EC%8B%A4%ED%96%89)
  1. [메소드 실행에서의 this](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#31-%EB%A9%94%EC%86%8C%EB%93%9C-%EC%8B%A4%ED%96%89%EC%97%90%EC%84%9C%EC%9D%98-this)
  1. [실수: 객체로부터 메소드를 분리할 때](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#32-%EA%B0%9D%EC%B2%B4%EB%A1%9C%EB%B6%80%ED%84%B0-%EB%A9%94%EC%86%8C%EB%93%9C%EB%A5%BC-%EB%B6%84%EB%A6%AC%ED%95%A0-%EB%95%8C)
1. [생성자 실행](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#4-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%8B%A4%ED%96%89) 
  1. [생성자를 실행할 때의 this](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#41-%EC%83%9D%EC%84%B1%EC%9E%90-%EC%8B%A4%ED%96%89%EC%97%90%EC%84%9C%EC%9D%98-this)
  1. [실수: new 깜빡할 때](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1#42-%EC%8B%A4%EC%88%98-new-%EA%B9%9C%EB%B9%A1%ED%95%A0-%EB%95%8C)
1. [간접 실행 ](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-2#5-%EA%B0%84%EC%A0%91-%EC%8B%A4%ED%96%89)
  1. [간접 실행에서의 this](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-2#51-%EA%B0%84%EC%A0%91-%EC%8B%A4%ED%96%89%EC%97%90%EC%84%9C%EC%9D%98-this)
1. [바인딩 함수](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-2#6-%EB%B0%94%EC%9D%B8%EB%94%A9-%ED%95%A8%EC%88%98)
  1. [바인딩 함수에서의 this](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-2#61-%EB%B0%94%EC%9D%B8%EB%94%A9-%ED%95%A8%EC%88%98%EC%97%90%EC%84%9C%EC%9D%98-this)
1. [화살표 함수](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-2#7-%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98)
  1. [화살표 함수에서의 this](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-2#71-%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98%EC%97%90%EC%84%9C%EC%9D%98-this)
  1. [실수: 화살표 함수로 메소드를 정의할 때](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-2#72-%EC%8B%A4%EC%88%98-%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98%EB%A1%9C-%EB%A9%94%EC%86%8C%EB%93%9C%EB%A5%BC-%EC%A0%95%EC%9D%98%ED%95%A0-%EB%95%8C)
1. [결론](https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-2#8-%EA%B2%B0%EB%A1%A0)


## 5. 간접 실행
간접 실행은 함수가 .call()이나 .apply() 메소드와 함께 호출될 때 수행된다. 


자바스크립트의 함수는 일급 객체다. 함수 역시 객체라는 말이다. 여기서의 this 객체는 Function을 의미한다.

[함수 객체에 있는 메소드](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function#메서드)를 보면 .call() and .apply()가 있는데, 이 메소드의 역할은 함수가 실행될 때의 문맥을 수정해준다.


.call(thisArg[, arg1[, arg2[, ...]]]) 메소드의 첫 번째 인자는 실행 문맥(this)를 받고, 나머지 인자는 호출되는 함수에 전달 될 매개 변수를 하나씩 받는다.

그리고 .apply(thisArg, [args]) 메소드는 첫 번째 인자로 실행 문맥(this)를 받고, 두 번째 인자로 호출되는 함수에 전달 될 유사 배열을 받는다. 이 유사 배열은 호출되는 함수에 전달된다.


아래의 예제는 간접 실행을 보여준다.


``` javascript
function increment(number) {  
  return ++number;  
}
increment.call(undefined, 10);    // => 11  
increment.apply(undefined, [10]); // => 11  
```


increment.call() 와 increment.apply()는 모두 10이라는 숫자 매개 변수를 증가한다.


이 둘 간의 가장 큰 차이점으로 .call() 메소드는 인수를 그대로 넘길 수 있다는 것이다. 예를 들어 myFunction.call(thisValue, 'value1', 'value2')에서는 인수들이 그대로 낱개 형태로 전달 되었지만, .apply() 메소드는 myFunction.apply(thisValue, ['value1', 'value2'])와 같이 유사 배열 형태로 전달해야 한다.


## 5.1. 간접 실행에서의 this
> 간접 실행인 .call() 그리고 .apply() 메소드에서는 첫 번째 매개 변수로 this를 받는다.


아래의 예제를 보자.


``` javascript
var rabbit = { name: 'White Rabbit' };  
function concatName(string) {  
  console.log(this === rabbit); // => true
  return string + this.name;
}
// Indirect invocations
concatName.call(rabbit, 'Hello ');  // => 'Hello White Rabbit'  
concatName.apply(rabbit, ['Bye ']); // => 'Bye White Rabbit'  
```


간접 실행은 함수의 실행 문맥을 특정 문맥으로 지정할 수 있기 때문에 유용하다. 함수 실행에서의 문맥은 항상 window, 혹은 엄격 모드에서는 undefined를 카리키게 되는데, 간접 실행으로 이 문제를 해결할 수 있다. 이 방법은 객체 내의 메소드를 호출할 때에도 사용될 수 있다.


또 다른 예제로 ES5 문법으로 부모 클래스의 생성자 함수를 호출하는 내용이다.


``` javascript
function Runner(name) {  
  console.log(this instanceof Rabbit); // => true
  this.name = name;  
}
function Rabbit(name, countLegs) {  
  console.log(this instanceof Rabbit); // => true
  // 간접 실행. 부모 생성자 호출
  Runner.call(this, name);
  this.countLegs = countLegs;
}
var myRabbit = new Rabbit('White Rabbit', 4);  
myRabbit; // { name: 'White Rabbit', countLegs: 4 }  
```


Rabbit 안에 있는 Runner.call(this, name)은 부모 함수를 간접 실행해서 객체를 초기화 한다.


## 6. 바인딩 함수
바인딩 함수는 객체가 바인딩 된 함수다. 보통 이것은 일반 함수에 .bind() 메소드가 적용된 것을 의미한다. 일반 함수와 바인딩 함수는 동일한 코드와 스코프를 공유한다. 하지만 실행 문맥은 다르다.


.bind(thisArg[, arg1[, arg2[, ...]]]) 메소드는 첫 번째 인자로 바인딩 함수에 적용할 실행 문맥(this)를 받고, 나머지 인자로 호출 될 함수에 전달 할 매개 변수를 전달한다. 그리고 이것은 문맥이 바인딩 된 새로운 함수로 반환된다.


아래의 코드는 바인딩 함수를 생성하는 예제다.


``` javascript
function multiply(number) {  
  'use strict';
  return this * number;
}
// 문맥을 지정해서 바인딩 함수를 생성
var double = multiply.bind(2);  
// 바인딩 함수를 실행
double(3);  // => 6  
double(10); // => 20  
```


multiply.bind(2)은 double이라는 새로운 함수 객체를 반환한다. 이 바인딩 함수는 this로 숫자 2를 가지고 있다. multiply과 double 함수는 동일한 코드와 스코프를 가진다.


즉시 실행이 되는 .apply() and .call() 메소드와는 다르게 .bind() 메소드는 미리 지정해놓은 this를 가진 새로운 함수를 반환하는데, 이건 원하는 시점에 실행할 수 있다.


## 6.1. 바인딩 함수에서의 this
> .bind() 메소드의 첫 번째 인자 this는 바인딩 함수가 실행될 때의 문맥을 의미한다.


.bind()의 역할은 새로운 함수를 만든다. 이 함수의 실행 문맥은 .bind()의 첫 번째 인자로 받은 게 된다. 바인딩 함수는 미리 정의해둔 this 값으로 함수를 생성할 수 있기 때문에 강력한 기술이다.


바인딩 함수를 만드는 예제를 살펴보자.


``` javascript
var numbers = {  
  array: [3, 5, 10],
  getNumbers: function() {
    return this.array;    
  }
};
// 바인딩 함수
var boundGetNumbers = numbers.getNumbers.bind(numbers);  
boundGetNumbers(); // => [3, 5, 10]  
// 객체로부터 메소드를 추출 = 함수 호출
var simpleGetNumbers = numbers.getNumbers;  
simpleGetNumbers(); // => undefined 혹은 error(엄격 모드)
```


boundGetNumbers는 numbers.getNumbers에 numbers 객체가 바인딩 된  함수다. boundGetNumbers() 함수는 numbers를 this로 가리키기 때문에 실행시 올바른 배열을 반환한다.

numbers.getNumbers 함수는 바인딩 없이 simpleGetNumbers라는 변수에 추출 되었다. simpleGetNumbers() 함수 호출은 this를 window 혹은 엄격 모드에서 undefined를 가리킨다. 그렇기 때문에 이 경우 simpleGetNumbers()는 올바른 배열을 반환하지 않는다.


.bind() 메소드는 문맥을 강제로 지정하고, 그 문맥을 항상 유지시켜 준다. 바인딩 함수는 .call() 혹은 .apply()를 적용시켜서 또 다른 문맥을 지정하는 게 불가능하다. 하더라도 영향이 전혀 없다.

오직 바인딩 함수에 new를 붙여 생성자 실행 형태로 해야 문맥을 바꿀 수 있다. 하지만 이 방법은 생성자 실행을 할 때 올바른 접근 방법이 아니다.

아래의 예제는 바인딩 함수를 정의하고, 미리 정의된 문맥을 변경해보는 내용이다.


``` javascript
function getThis() {  
  'use strict';
  return this;
}
var one = getThis.bind(1);  
// 바인딩 함수 실행
one(); // => 1  
// 바인딩 함수에 .apply()와 .call()을 이용해 문맥 변경 시도
one.call(2);  // => 1  
one.apply(2); // => 1  
// 다시 시도
one.bind(2)(); // => 1  
// 바인딩 함수를 생성자 함수로 호출
new one(); // => Object  
```


오직 new one()만이 바인딩 함수의 문맥을 변경했다. 나머지 실행은 모두 문맥이 변경되지 못했으므로 항상 1을 가리킨다.


## 7. 화살표 함수
화살표 함수는 함수를 정의할 때 간단한 형태로, 그리고 문맥을 바인드하기 위해 개발되었다. 

아래와 같은 형태로 사용된다.


``` javascript
var hello = (name) => {  
  return 'Hello ' + name;
};
hello('World'); // => 'Hello World'  
// 짝수만 호출
[1, 2, 5, 6].filter(item => item % 2 === 0); // => [2, 6]
```


화살표 함수는 function이라는 키워드를 사용하지 않기 때문에 짧게 사용할 수 있는 가벼운 문법이다. 

심지어 함수에 코드가 1줄만 있으면 return을 생략해도 된다.


화살표 함수는 익명 함수다. 그렇기 때문에 화살표 함수의 name 속성은 '' 빈 스트링이다. 그렇기 때문에 이 화살표 함수는 이름을 가지지 않는다. 그래서 재귀 함수를 만들거나, 이벤트 핸들러를 붙일 때 유용하다.

또한 화살표 함수에는 arguments 객체가 없기 때문에 일반적으로 사용하는 함수와 다르다. 하지만 ES6의 나머지 매개변수 문법을 사용하면 매개변수에 접근할 수 있다.


``` javascript
var sumArguments = (...args) => {  
   console.log(typeof arguments); // => 'undefined'
   return args.reduce((result, item) => result + item);
};
sumArguments.name      // => ''  
sumArguments(5, 5, 6); // => 16  
```


## 7.1. 화살표 함수에서의 this
> 여기서의 this는 화살표 함수가 정의된 인근 문맥과 동일하다.


화살표 함수는 자채 실행 문맥을 가지지 않기 때문에 외부 함수의 this를 상속 받는다.

아래의 예제는 문맥의 직관성을 보여준다.


``` javascript
class Point {  
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
  log() {
    console.log(this === myPoint); // => true
    setTimeout(()=> {
      console.log(this === myPoint);      // => true
      console.log(this.x + ':' + this.y); // => '95:165'
    }, 1000);
  }
}
var myPoint = new Point(95, 165);  
myPoint.log();  
```


setTimeout은 log() 메소드처럼 myPoint 객체를 바라보는 화살표 함수를 호출한다. 보다시피 화살표 함수는 외부 함수의 문맥을 상속 받았다.

이 예제에서 만약 일반 함수를 사용하려 했다면, 그 함수는 새로운 문맥(window 혹은 undefined)을 만들게 된다. 그래서 일반 함수로 같은 결과를 얻기 위해서는 문맥을 지정해주는 작업이 따로 또 필요하다.

setTimeout(function() {...}.bind(this))은 장황하다. 하지만, 화살표 함수를 사용하면 깔끔하고 단어 길이도 훨씬 짧다.


만약 화살표 함수가 최상단 스코프에 정의 되었다면, 여기서의 문맥은 항상 전역 객체다.


``` javascript
var getContext = () => {  
   console.log(this === window); // => true
   return this;
};
console.log(getContext() === window); // => true  
```


화살표 함수의 문맥은 바뀔 수 없다. 문맥을 수정하는 메소드를 사용하더라도 절대 바뀌지 않는다.


``` javascript
var numbers = [1, 2];  
(function() {  
  var get = () => {
    console.log(this === numbers); // => true
    return this;
  };
  console.log(this === numbers); // => true
  get(); // => [1, 2]
  // 화살표 함수에 .apply() 혹은 .call()을 적용
  get.call([0]);  // => [1, 2]
  get.apply([0]); // => [1, 2]
  // Bind
  get.bind([0])(); // => [1, 2]
}).call(numbers);
```


함수 표현식이 .call(numbers)을 통해 간접 실행되었다. 여기서의 실행 문맥은 numbers다. get이라는 화살표 함수는 this를 numbers로 가리킨다. 왜냐하면 구성 문맥을 상속받기 때문이다.

get 함수가 어떻게 실행되던, numbers라는 초기 문맥을 유지한다. .call(), .apply(), .bind()로 문맥을 바꿔봐도 효과가 전혀 없다.

화살표 함수는 생성자 함수로 사용될 수 없다. 만약 new get()처럼 생성자 함수로 실행된다면, 자바스크립트는 ```TypeError: get is not a constructor```라는 에러를 반환한다.


## 7.2. 실수: 화살표 함수로 메소드를 정의할 때
객체 내부의 메소드를 화살표 함수로 정의하고 싶을지도 모른다. 좋다. 이 표기법은 기존 함수 표현식와 비교했을 때 훨씬 짧기 때문이다. 기존에는 function(param) {..}로 썼다면 화살표 함수로는 (param) => {...}로 사용된다.


이 예제는 Period라는 클래스에 format이라는 화살표 함수를 정의하는 내용이다.


``` javascript
function Period (hours, minutes) {  
  this.hours = hours;
  this.minutes = minutes;
}
Period.prototype.format = () => {  
  console.log(this === window); // => true
  return this.hours + ' hours and ' + this.minutes + ' minutes';
};
var walkPeriod = new Period(2, 30);  
walkPeriod.format(); // => 'undefined hours and undefined minutes'  
```


format은 화살표 함수고, 전역 문맥에 정의 되었기 때문에 여기서의 this는 window 객체를 가리키게 된다.

심지어 메소드 호출 형태로 실행 되더라도 여기서의 문맥은 window다. 왜냐하면 화살표 함수는 어떠한 실행 타입이더라도 변하지 않는 정적 문맥을 가지기 때문이다.

this는 window를 가리킨다. 그래서 this.hours 와 this.minutes는 undefined다. 이 메소드는 'undefined hours and undefined minutes'를 반환한다. 기대한 결과와는 다르다.

함수 표현식은 이 문제를 해결해준다. 왜냐하면 일반함수는 실행 환경에 따라서 문맥이 바뀌기 때문이다.


``` javascript
function Period (hours, minutes) {  
  this.hours = hours;
  this.minutes = minutes;
}
Period.prototype.format = function() {  
  console.log(this === walkPeriod); // => true
  return this.hours + ' hours and ' + this.minutes + ' minutes';
};
var walkPeriod = new Period(2, 30);  
walkPeriod.format(); // => '2 hours and 30 minutes'  
```


walkPeriod.format()은 walkPeriod 객체를 문맥으로 가리키는 메소드 실행이다. this.hours는 2, this.minutes는 30을 가리킨다. 따라서 반환되는 값은 기대했던 값과 동일하다. '2 hours and 30 minutes'


## 8. 결론
함수의 실행 환경은 this에 가장 큰 영향을 미친다. 따라서 오늘부터는 이렇게 생각하면 안 된다.


> this가 어디로부터 오는가?


올바른 사고 방식은 다음과 같다.


> 함수가 어떻게 실행되는가?


화살표 함수의 경우에는 이렇게 생각해야 한다.


> 화살표 함수가 정의된 곳의 문맥은?


이러한 사고 방식은 복잡한 this 개념을 다룰 때 도움된다. 