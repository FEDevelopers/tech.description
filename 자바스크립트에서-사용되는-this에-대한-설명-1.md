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


## 1. this에 대한 미스터리
많은 시간 동안 this 키워드는 자바스크립트 개발자들에게 미스터리의 대상이었다. this는 강력한 기능임에도 불구하고 쉽게 이해하기 힘든 부분이다.


Java, PHP와 같은 언어에서 this는 클래스로부터 생성되는 인스턴스 중 현재 객체를 의미한다. 그 이상 그 이하도 아니다. 대부분 클래스 밖에서는 사용될 수 없으며 이러한 접근 방법으로는 혼란이 생기지 않는다.


자바스크립트에서 this는 함수의 현재 실행 문맥이다. 자바스크립트에는 4가지의 함수 실행 타입이 있기 때문이다.


- 함수 실행: alert('Hello World!')
- 메소드 실행: console.log('Hello World!')
- 생성자 실행: new RegExp('\\d')
- 간접 실행: alert.call(undefined, 'Hello World!')


각각의 타입은 서로 다른 각각의 문맥을 가진다. 이 부분에서 개발자들의 예상과 다른 부분이 생긴다.

더욱이 [엄격 모드](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Strict_mode) 역시 실행 문맥에 영향을 미친다.


this 키워드를 이해할 수 있는 방법은 함수 실행, 함수 실행이 문맥에 어떤 영향을 미치는지에 대해 확실히 이해하는 것이다.

이 글은 실행에 대한 설명에 초점을 맞췄다. 그리고 함수 호출이 this에 어떤 영향을 미치는지, 그리고 문맥을 식별하는 과정에서 저지르는 일반적인 실수들을 보여줄 것이다.


시작하기 전에 아래의 용어들과 친숙해지자.

- 실행이란, 함수 내부에 있는 코드를 수행하는 것이다. (쉽게 말해 함수 호출이라고 보면 됨) 예를 들어 parseInt라는 함수는 parseInt('15', 10)로 실행된다.
- 실행 시점에서의 문맥은 함수 내부에서의 값이다.
- 함수의 스코프는 변수, 객체, 내부 함수들의 집합이다.


## 2. 함수 실행

함수 실행은 함수 객체로 계산 될 표현식이 열림 괄호, 콤마로 구분되는 인자들, 그리고 닫힘 괄호와 함께 수행된다. parseInt('18')가 함수 실행의 예제다.

이 표현식은 myObject.myFunction와 같이 메소드를 실행하기 위해 사용하는 속성 접근자가 될 수 없다. 예를 들어 [1,5].join(',')는 함수 실행이 아니라 메소드 호출이다.


함수 실행에 대한 간단한 예제


``` javascript
function hello(name) {  
  return 'Hello ' + name + '!';
}
// 함수 실행
var message = hello('World');  
console.log(message); // => 'Hello World!'  
```


``` hello('World') ```은 함수 실행이다. ``` hello ```표현식은 뒤따라 오는 'World' 인자와 함께 함수 객체로 계산 될 것이다.


A more advanced example is the IIFE (immediately-invoked function expression):
심화 예제로 [IIFE](https://en.wikipedia.org/wiki/Immediately-invoked_function_expression)(즉시실행함수)가 있다.


``` javascript
var message = (function(name) {  
   return 'Hello ' + name + '!';
})('World');
console.log(message) // => 'Hello World!'  
```


즉시실행함수 역시 함수 실행의 종류 중 하나다. 앞 부분에 위치한 괄호 한 쌍은 함수 객체로 바뀔 표현식이고, 뒤에 위치한 괄호 한 쌍은 함수에 전달될 인자들이다. 여기에서는 'World'가 인자다.


## 2.1 함수 실행에서의 this
> 함수 실행에서의 this는 전역 객체다.

전역 객체는 실행 환경에 따라 결정된다. 웹 브라우저에서는 window 객체가 전역 객체다.


함수 실행에서의 실행 문맥은 전역 객체다.

아래의 함수 예제를 통해 문맥을 체크해보자.


``` javascript
function sum(a, b) {  
   console.log(this === window); // => true
   this.myNumber = 20; // 전역 객체에 'myNumber'라는 속성을 추가
   return a + b;
}
// sum()은 함수 호출이다.
// sum()에서의 this는 전역 객체다. (window)
sum(15, 16);     // => 31  
window.myNumber; // => 20  
```


sum(15, 16)이 실행되자마자, 자바스크립트는 자동으로 this를 전역 객체로 가진다. 웹 브라우저 환경에서의 this는 window다.


this가 함수 스코프 밖(최상단: 전역 실행 문맥)에서 사용되었을 경우, 여기서의 this 역시 전역 객체를 참조하게 된다.


``` javascript
console.log(this === window); // => true  
this.myString = 'Hello World!';  
console.log(window.myString); // => 'Hello World!'  
```


``` html
<!-- In an html file -->  
<script type="text/javascript">  
   console.log(this === window); // => true
</script>  
```


## 2.2 엄격 모드에서 함수 실행에서의 this
> 엄격 모드에서 함수 실행에서의 this는 undefined다.


엄격 모드는 코드 안정성과 더 나은 오류 검증을 제공하기 위해 ECMA Script 5.1 버전에서 처음 소개 되었다.
엄격 모드로 작성하기 위해 함수 내부의 최상단에 'use strict'라는 예약어를 적는다. 이 모드는 실행 문맥인 this를 undefined로 만든다. 실행 문맥은 더이상 전역 객체로 되지 않고, 위의 2.1 케이스와 반대의 상황이 된다.


엄격 모드로 실행되는 예제 코드


``` javascript
function multiply(a, b) {  
  'use strict'; // 엄격 모드
  console.log(this === undefined); // => true
  return a * b;
}
// multiply() 함수는 엄격 모드로 실행됨
// multiply()에서의 this는 undefined
multiply(2, 5); // => 10  
```


multiply(2, 5) 함수가 실행될 때 this는 undefined다.


엄격 모드는 현재 스코프 뿐만 아니라 내부 스코프에서도 적용된다. (내부에 정의된 모든 함수에 적용됨)


``` javascript
function execute() {  
   'use strict'; // 엄격 모드
   function concat(str1, str2) {
     // 이곳에서도 마찬가지로 엄격 모드
     console.log(this === undefined); // => true
     return str1 + str2;
   }
   // concat() 함수는 엄격 모드
   // concat() 함수 안에서의 this는 undefined
   concat('Hello', ' World!'); // => "Hello World!"
}
execute();  
```


'use strict'은 최상단에 위치하고, 해당 스코프에 엄격 모드를 실행해준다. concat은 실행 스코프 내에 정의되어있기 때문에 엄격 모드를 상속 받는다. 그리고 concat('Hello', ' World!') 실행은 this를 undefined로 만든다.


자바스크립트 파일에는 엄격 모드, 비 엄격 모드 두 가지 모두 포함되어있다. 그래서 같은 실행 타입에서 서로 다른 모드를 적용할 수 있다.


``` javascript
function nonStrictSum(a, b) {  
  // 비 엄격 모드
  console.log(this === window); // => true
  return a + b;
}
function strictSum(a, b) {  
  'use strict';
  // 엄격 모드
  console.log(this === undefined); // => true
  return a + b;
}
// nonStrictSum() 함수는 비 엄격 모드로 실행
// nonStrictSum()에서의 this는 window 객체
nonStrictSum(5, 6); // => 11  
// strictSum() 함수는 엄격 모드로 실행
// strictSum()에서의 this는 undefined
strictSum(8, 12); // => 20  
```


## 2.3. 실수: 내부 함수에서의 this를 사용할 때
함수를 실행할 때 흔히 하는 실수가 외부 함수에서의 this와 내부 함수에서의 this를 동일하게 생각하는 것이다.


사실 내부 함수의 문맥은 외부 함수의 문맥에 의존되는 게 아니라 오직 실행 환경에 좌우된다. 
기대하는 되로 this가 동작되려면 수정이 필요하다. (call이라 apply 메소드를 사용하는 간접 실행, 또는 바인딩 함수를 적용)


아래의 예제는 두 수의 합계를 계산해준다.


``` javascript
var numbers = {  
   numberA: 5,
   numberB: 10,
   sum: function() {
     console.log(this === numbers); // => true
     function calculate() {
       // this는 window, 엄격 모드였으면 undefined
       console.log(this === numbers); // => false
       return this.numberA + this.numberB;
     }
     return calculate();
   }
};
numbers.sum(); // NaN, 엄격 모드였으면 TypeError
```


numbers.sum()은 객체 내에 있는 메소드를 실행하는 것이다. 그래서 sum 메소드 내의 문맥은 numbers 객체다. calculate 함수는 sum 내부에 정의되어있다. 그래서 아마도 calculate() 역시 this를 numbers 객체로 바라보고 있을 거라고 예상한다.
하지만 calculate()은 메소드 실행이 아닌 함수 실행이다. 그리고 이 함수에서의 this는 전역 객체인 window다. (만약, 엄격 모드였다면 undefined) 비록 외부 함수의 문맥이 numbers 객체지만, calculate 함수에는 영향을 미치지 않는다.

numbers.sum()의 실행 결과는 NaN, 혹은 엄격 모드에서 numberA 속성이 undefined이므로 접근할 수 없어서 TypeError다. calculate 함수는 제대로 실행되지 않았기 때문에 실행 결과는 기대한대로 5 + 10 = 15가 되지 않는다.


이 문제를 해결하기 위해, calculate 함수 역시 sum method와 동일한 문맥으로 되어야 한다. 그래야 numberA와 numberB 속성에 접근할 수 있기 때문이다. 해결책 중 하나로 .call 메소드를 사용하는 것이다.


``` javascript
var numbers = {  
   numberA: 5,
   numberB: 10,
   sum: function() {
     console.log(this === numbers); // => true
     function calculate() {
       console.log(this === numbers); // => true
       return this.numberA + this.numberB;
     }
     // 문맥을 수정하기 위해 .call() 메소드를 적용
     return calculate.call(this);
   }
};
numbers.sum(); // => 15  
```


calculate.call(this)는 이전과 동일하게 계산된다. 하지만 추가로, 첫 번째 파라미터로 들어온 인자로 실행 문맥을 수정해준다. 이제 this.numberA + this.numberB의 결과는 numbers.numberA + numbers.numberB와 동일하게 된다. 그래서 결과값도 예상했던대로 5 + 10 = 15가 된다.


## 3. 메소드 실행
메소드는 객체의 속성으로 있는 함수다. 예를 들어


``` javascript
var myObject = {  
  // helloFunction is a method
  helloFunction: function() {
    return 'Hello World!';
  }
};
var message = myObject.helloFunction();  
```


여기서 ```helloFunction```는 myOjbect의 메소드다. 메소드에 접근하기 위해서는 ```myObject.helloFunction```와 같은 속성 접근자를 이용하면 된다.


메소드 실행은 속성 접근자 형태의 표현식이 함수 객체로 계산되면서 실행된다. 이 표현식은 함수와 마찬가지로 열림 괄호, 함수에 전달할 인자, 닫힘 괄호의 구조다.
이전 예제를 활용하면 myObject.helloFunction()은 myObject라는 객체의 helloFunction라는 메소드 실행이다. [1, 2].join(',') 혹은 /\s/.test('beautiful world') 역시 메소드 실행의 종류다.


앞서 설명한 함수 실행과 메소드 실행이 다르다는 점은 중요하다. 왜냐하면 둘은 서로 다른 타입이기 때문이다. 둘의 가장 큰 차이점은 메소드 실행은 속성 접근자를 통해 function (<expression>.functionProperty() or <expression>['functionProperty']())를 호출한다. 반면에 함수 실행은 속성 접근자를 사용하지 않고, (<expression>())와 같이 바로 호출한다


``` javascript
['Hello', 'World'].join(', '); // 메소드 실행
({ ten: function() { return 10; } }).ten(); // 메소드 실행
var obj = {};  
obj.myFunction = function() {  
  return new Date().toString();
};
obj.myFunction(); // 메소드 실행

var otherFunction = obj.myFunction;  
otherFunction();     // 함수 실행  
parseFloat('16.60'); // 함수 실행  
isNaN(0);            // 함수 실행  
```


## 3.1 메소드 실행에서의 this
this는 메소드 실행에서 메소드를 소유하고 있는 객체다 


객체 내에 있는 메소드를 실행할 때, 여기서의 this는 객체 자신이다.

숫자를 증가하는 메소드를 가진 객체를 하나 만들어보자.


``` javascript
var calc = {  
  num: 0,
  increment: function() {
    console.log(this === calc); // => true
    this.num += 1;
    return this.num;
  }
};
// 메소드 실행. 여기서의 this는 calc.
calc.increment(); // => 1  
calc.increment(); // => 2  
```


calc.increment() 호출은 increment 함수의 문맥을 calc 객체로 만들어준다. 그래서 this.num 참조로 number 속성을 증가시하는 게 가능하도록 해준다.
자바스크립트 객체는 프로토타입에 있는 메소드를 상속 받는다. 상속 받은 메소드를 객체 내에서 실행한다면 메소드에서의 문맥은 객체 자신을 가리키게 된다.


``` javascript
var myDog = Object.create({  
  sayName: function() {
     console.log(this === myDog); // => true
     return this.name;
  }
});
myDog.name = 'Milo';  
// 메소드 실행. 여기서의 this는 myDog.
myDog.sayName(); // => 'Milo'  
```


Object.create()는 myDog라는 새로운 객체를 만들고, 프로토타입을 설정한다. myDog 객체는 sayName이라는 메소드를 상속받는다. myDog.sayName()이 실행될 때, myDog가 실행 문맥이다.


ECMAScript 6의 class 예약어에서 메소드 실행 문맥은 위와 마찬가지로 인스턴스 자신을 가리킨다.


``` javascript
class Planet {  
  constructor(name) {
    this.name = name;    
  }
  getName() {
    console.log(this === earth); // => true
    return this.name;
  }
}
var earth = new Planet('Earth');  
// 메소드 실행. 여기서의 this는 earth.
earth.getName(); // => 'Earth'  
```


## 3.2 실수: 객체로부터 메소드를 분리할 때
객체 내에 있는 메소드는 별도의 변수로 분리할 수 있다. 이 변수를 통해 메소드를 호출할 때, 당신은 아마도 여기서의 this가 메소드가 정의되어있는 객체라고 생각할 것이다.


사실 객체 밖에 있는 메소드를 호출할 경우, 함수 실행을 한 결과와 같다. 함수 실행을 할 경우 this는 전역 객체인 window를 가리킨다. (엄격 모드에서는 undefined)
.bind() 바인딩 함수를 사용해서 문맥을 수정할 경우, 메소드를 객체에 포함시킬 수 있다.


아래는 Animal 생성자로 myCat이라는 인스턴스를 생성하는 예제다. 그리고 setTimeout() 함수로 1초 뒤 myCat 객체의 정보를 출력한다.


``` javascript
function Animal(type, legs) {  
  this.type = type;
  this.legs = legs;  
  this.logInfo = function() {
    console.log(this === myCat); // => false
    console.log('The ' + this.type + ' has ' + this.legs + ' legs');
  }
}
var myCat = new Animal('Cat', 4);  
// "The undefined has undefined legs" 출력
// 혹은 엄격모드라면 TypeError 출력
setTimeout(myCat.logInfo, 1000);  
```


아마도 setTimeout으로 myCat.logInfo()를 호출할 때, myCat 객체가 출력될 거라고 예상할 것이다. 하지만 setTimeout의 매개변수로 전달되었기 때문에 메소드는 객체로부터 분리 되어있고, 1초 뒤 함수 실행이 된다. logInfo가 함수로써 실행되기 때문에 여기서의 this는 전역 객체이거나 엄격 모드에서라면 undefined다. 그렇기 때문에 객체의 정보를 기대한 것대로 출력하지 못한다.


함수는 .bind 메소드를 사용해 문맥을 강제로 지정시킬 수 있다. 만약 분리된 메소드가 myCat 객체로 바인딩 된다면 이 문제는 해결된다.


``` javascript
function Animal(type, legs) {  
  this.type = type;
  this.legs = legs;  
  this.logInfo = function() {
    console.log(this === myCat); // => true
    console.log('The ' + this.type + ' has ' + this.legs + ' legs');
  };
}
var myCat = new Animal('Cat', 4);  
// "The Cat has 4 legs" 출력
setTimeout(myCat.logInfo.bind(myCat), 1000);  
```


myCat.logInfo.bind(myCat)는 객체의 메소드가 logInfo라는 새로운 함수로 실행된다. 하지만 바인딩 메소드 덕분에 함수 실행임에도 불구하고, 여기서의 this는 myCat을 가리키게 된다. 


## 4. 생성자 실행
생성자 실행은 표현식 앞에 new라는 키워드가 붙었을 때, 함수 객체로 계산되어 수행된다. 이 표현식은 함수와 마찬가지로 열림 괄호, 함수에 전달할 인자, 닫힘 괄호의 구조다. 예를 들어 new RegExp('\\d')와 같다.


아래의 예제는 Country라는 함수가 생성자로 실행되는 내용이다.


``` javascript
function Country(name, traveled) {  
   this.name = name ? name : 'United Kingdom';
   this.traveled = Boolean(traveled); // boolean으로 타입 변환
}
Country.prototype.travel = function() {  
  this.traveled = true;
};
// Constructor invocation
var france = new Country('France', false);  
// Constructor invocation
var unitedKingdom = new Country;

france.travel(); // Travel to France  
```


```new Country('France', false)```은 Country 함수의 생성자 실행이다. 이것의 실행 결과는 France라는 이름을 가진 새로운 객체다.
만약 생성자에 아무런 매개 변수 없이 실행 된다면, ```new Country```처럼 괄호가 생략되어도 된다. 


ECMAScript 6에서는 생성자를 class라는 키워드로 정의할 수 있게 해준다.


``` javascript
class City {  
  constructor(name, traveled) {
    this.name = name;
    this.traveled = false;
  }
  travel() {
    this.traveled = true;
  }
}
// 생성자 실행
var paris = new City('Paris', false);  
paris.travel();  
```


new City('Paris')은 생성자 실행이다. 이 객체에서 초기 값은 constructor라는 특수 메소드로 설정할 수 있다. 이 메소드 내에서의 this는 새로 만들어지는 객체를 바라보게 된다.


생성자 호출은 생성자의 프로토타입으로부터 속성을 상속받는 새로운 빈 객체를 만든다. 생성자 함수의 역할은 객체를 초기화하는 것이다. 
이미 알고 있을지도 모르지만, 이 타입에서 this는 인스턴스를 가리킨다. 


myObject.myFunction과 같은 속성 접근자가 new 키워드 뒤에 오게되면, 자바스크립트는 메소드 실행이 아닌 생성자 실행으로 계산한다.
예를 들어 new myObject.myFunction()의 경우, 첫 번째로 extractedFunction = myObject.myFunction와 같이 함수가 추출되고, 그 다음으로 new extractedFunction()와 같이 생성자 실행으로 새로운 객체가 만들어진다.


## 4.1 생성자 실행에서의 this
> 생성자 실행에서의 this는 새롭게 만들어진 객체이다.


생성자 실행에서의 문맥은 새롭게 만들어진 객체다. 생성자 실행은 객체에 초기값을 셋팅하기 위해 사용된다. 초기값 셋팅의 예로는 생성자 함수의 매개 변수로 받은 데이터, 속성을 위한 환경 변수, 이벤드 핸들러 등이 있다.


아래 예제를 통해 문맥을 체크해보자.


``` javascript
function Foo () {  
  console.log(this instanceof Foo); // => true
  this.property = 'Default Value';
}
// 생성자 실행
var fooInstance = new Foo();  
fooInstance.property; // => 'Default Value'  
```


new Foo()은 생성자 실행이다. 여기서의 문맥은 Foo의 인스턴스가 된다. Foo의 내부에서는 초기값이 셋팅되었다. this.property는 default value라는 값을 가진다.


ES6에서 사용 가능한 class 문법 역시 같은 형식이다. 초기값 셋팅은 오직 생성자 메소드에서 할 수 있다.


``` javascript
class Bar {  
  constructor() {
    console.log(this instanceof Bar); // => true
    this.property = 'Default Value';
  }
}
// Constructor invocation
var barInstance = new Bar();  
barInstance.property; // => 'Default Value'  
```


new Bar()가 실행되면서, 자바스크립트는 생성자 메소드를 통해 문맥을 설정한 빈 객체를 만든다. this.property = 'Default Value'와 같이 ```this``` 키워드를 통해 객체에 속성 값을 추가할 수 있다.


## 4.2 실수: new 깜빡할 때
몇몇 자바스크립트 함수는 생성자 실행 형태로 실행됐을 때 뿐만 아니라 함수 실행으로도 인스턴스를 생성한다. 예를 들어 RegExp가 있다.


``` javascript
var reg1 = new RegExp('\\w+');  
var reg2 = RegExp('\\w+');

reg1 instanceof RegExp;      // => true  
reg2 instanceof RegExp;      // => true  
reg1.source === reg2.source; // => true  
```

new RegExp('\\w+')와 RegExp('\\w+')가 실행될 때, 자바스크립트는 동일한 정규식 객체를 생성한다.


객체 생성을 위해 함수 실행을 사용하는 것(팩토리 패턴을 제외)은 잠재적 문제를 만들게 된다. 왜냐하면 new 키워드가 생략되었을 때 생성자 함수는 객체를 초기화하는 로직을 생략할지도 모른다. 

아래는 해당 문제점이 나타나는 예제다.


``` javascript
function Vehicle(type, wheelsCount) {  
  this.type = type;
  this.wheelsCount = wheelsCount;
  return this;
}
// Function invocation
var car = Vehicle('Car', 4);  
car.type;       // => 'Car'  
car.wheelsCount // => 4  
car === window  // => true  
```


Vehicle은 타입과 바퀴개수 속성을 가지는 객체를 만들어주는 함수다. 

Vehicle('Car', 4)를 실행하게 되면 객체가 반환된다. 이 객체는 올바른 속성을 가지고 있다. car.type으로 'Car'를, car.wheelCount로 4를 나타낸다. 아마도 초기값을 가진 새로운 객체가 잘 생성되었으리라 예상할 것이다.

하지만 여기서의 this는 함수 실행이 되므로 window 객체를 가리키게 된다. 그리고 Vehicle('Car', 4)은 속성을 window 객체에 추가한다. 잘못된 사용이다. 새로운 객체가 만들어지지 않았다.


생성자를 호출할 때에는 꼭 new 연산자를 사용해야 한다.


``` javascript
function Vehicle(type, wheelsCount) {  
  if (!(this instanceof Vehicle)) {
    throw Error('Error: Incorrect invocation');
  }
  this.type = type;
  this.wheelsCount = wheelsCount;
  return this;
}
// 생성자 실행
var car = new Vehicle('Car', 4);  
car.type               // => 'Car'  
car.wheelsCount        // => 4  
car instanceof Vehicle // => true

// 함수 실행. 잘못된 방식.
var brokenCar = Vehicle('Broken Car', 3);  
```


new Vehicle('Car', 4)은 정상 동작한다. 초기 값을 가진 새로운 객체가 생성되었다. 왜냐하면 생성자 실행 앞에 new 키워드를 썼기 때문이다. 

검증하는 방법은 생성자 함수에 추가되어 있다. ```this instanceof Vehicle```로 실행 문맥으로 올바른 객체 타입이 맞는지 체크한다. 만약 여기서의 this가 Vehicle이 아니라면 에러가 발생한다. 이와 같은 경우 만약 Vehicle('Broken Car', 3)가 new 키워드 없이 실행된다면, 올바른 실행이 아니라는 에러 메세지를 반환하게 된다.
