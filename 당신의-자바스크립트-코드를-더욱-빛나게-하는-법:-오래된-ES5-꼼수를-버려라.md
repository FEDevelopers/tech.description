> 이 문서는 https://rainsoft.io/make-your-javascript-code-shide-knockout-old-es5-hack/ 를 번역한 내용입니다.

ECMAScript 2015 이전의 자바스크립트 언어는 다루기 까다로웠다.
간단한 ES5 스크립트는 괜찮았다. 하지만 어플리케이션의 복잡도가 커지게 되면서, 언어 구조의 부족함으로 인해 코드가 불편해 진다는 것을 느끼기 시작하게 된다.

당신은 어플리케이션 요구사항에 맞게 개발을 해야하기 때문에, 당신은 ES5 문법으로 개발하게 되면 골칫거리가 될 코드를 그냥 넘기지 못한다. 그에 대한 해결책으로 합리적이진 않지만, 꼼수를 사용하게 된다.

꼼수와 차선책으로 개발된 여러 줄의 코드는 문제를 발생하게 된다. 더불어 쉽게 읽을 수 없게 되고, 유지보수를 하기에도 어렵게 된다.

그리고 일반적인 말로

> 자바스크립트 꼼수를 적용하는 것은 카드로 집을 짓는 것과 같다.


![카드집](https://rainsoft.io/content/images/2016/08/House-of-cards-014-1.jpg)


차선 책으로는 Underscore 혹은 lodash와 같은 유틸리티 라이브러리를 사용하는 것이다. 라이브러리는 어려운 것을 쉽게 해결할 수 있는 다양한 함수를 제공한다.
훨씬 나은 방법이지만, 이것 역시 여전히 차선책이다. 당신은 추가 의존성을 유지해야되고, 필요한 해결책을 항상 찾지 않게 될지도 모른다.

ECMAScript 2015, 2016, 그리고 곧 나올 2017은 이런 현상을 개선하기 위해 효과적인 것들을 만들고 있다. 이미 꼼수로 개발된 대부분의 것들을 안정적인 것들로 쉽게 대체될 수 있다. 또한 당신의 프로젝트의 프로세스를 개선할 수 있다.

어떻게 하면 자바스크립트 코드를 바위처럼 단단히, 빛나게 개발할 수 있을까? 당신의 코드에서 꼼수를 제거하고, 해결책을 적용하고 있는 이 가이드를 따라라. 

## 1. 배열 내 요소가 존재하는지 증명하는 법

> array.indexOf(element) !== -1 를 사용해서 요소를 탐색

Since every application deals with arrays, it's a common task to verify if an element exists within a collection.
모든 어플리케이션이 배열로 다루어진다면, 집합 내에 특정 요소가 존재하는지 증명하는 건 평범한 작업이다.

- ES5에서는 보통 Array.prototype.indexOf(element, [fromIndex]) 메소드를 사용했고, 리턴되는 값과 -1을 비교해서 요소의 존재를 증명했다.

이 .indexOf() 메소드를 사용하는 증명법은 부작용이 있는데, 배열 내 요소가 존재하면 요소의 인덱스를 리턴하고, 존재하지 않다면 그냥 -1을 리턴한다.

예제를 살펴보자.

``` javascript
var months = ['January', 'March', 'July'];  
months.indexOf('March') !== -1;  // => true  
months.indexOf('August') !== -1; // => false  
```

months라는 배열에서 한 요소의 존재를 증명하기 위해서 months.indexOf()를 사용했고, 리턴되는 값과 -1을 비교한다. 존재하는 요소인 months.indexOf('March')를 사용하게 되면 인덱스 1을 리턴하게 되고, 1 !== -1 비교 값은 참이 된다. 'March'는 존재한다는 의미다.
months.indexOf('August')은 -1을 리턴하는데, 이 의미는 요소가 없다는 것: -1 !== -1은 거짓이다. 'August' 요소는 존재하지 않는다는 의미다.

더욱 나쁜 방법은 비교를 하는 방법이 다양하다는 점이다.

- months.indexOf('March') != -1
- months.indexOf('Match') < 0
- ~months.indexOf('March')
- and so on...

이 방법으로 요소를 탐색하는 것은 간편하지 않을 뿐더러 꼼수를 사용하는 것 같은 느낌이다.

- 이 문제를 해결하기 위해, ECMAScript 2015는 새로운 메소드인 Array.prototype.includes(element, [fromIndex])를 소개한다. 이 메소드는 배열 내 요소가 존재하는지 참 / 거짓 값으로 리턴을 해준다.

![includes](https://rainsoft.io/content/images/2016/08/1-4.jpg)

예제를 향상 시켜보자.

``` javascript
var months = ['January', 'March', 'July'];  
months.includes('March');  // => true  
months.includes('August'); // => false  
```

존재하는 요소의 경우 months.includes('March') 결과 값으로 참, 존재 하지 않는 요소의 경우 months.includes('August') 결과 값으로 거짓을 리턴한다.

간단하고 편리하다! 당연히 .indexOf(element) !== -1은 .includes(element)로 바뀌어야 한다.

## 2. 함수 인자 값에 접근하는 법

> arguments를 사용하는 건 하드코딩을 하는 느낌이다.

arguments는 함수 호출 부분에 전달되는 인자들을 포함하는 하나의 객체다.

내 생각에, arguments 객체는 하드코드 되어있는 제한적인 구조물이다.

- arguments는 유사 배열이다. 그래서 배열 메소드인 .forEach(), .reduce(), etc 등을 사용하는 것은 불가능하다.
- arguments 이름은 바꿀 수 없다. 예를들어 args로 바꾸는 것도 안 된다.
- 모든 함수의 스코프는 자신의 arguments를 정의한다. 내부 함수에서 외부 함수의 arguments에 접근하기 위해서는 var outerArgs = arguments; 와 같은 임시 변수가 필요하다.
- 함수의 기본 형태인 function myFun() {}와 arguments의 실제 사용에는 모순이 있다. 함수 형태에서는 파라미터를 넘겨주지 않지만, 함수 내 코드에서는 갑자기 arguments 객체를 사용하게 되면 코드 상에서 이질감이 생긴다.

예제를 살펴보자.

``` javascript
function sum() {  
  return Array.prototype.reduce.call(arguments, function(sum, el) {
    return sum + el;
  });
}
sum(10, 5, 2); // => 17  
```

sum() 함수는 arguments의 합계를 리턴한다. 위에서 말한 것처럼, arguments는 유사 배열이다. 그래서 우회적인 방법으로 .reduce()를 사용해야 한다. function sum() {} 이 함수 선언의 형태는 아무런 인자를 받지 않고 있다, 하지만 함수 내의 arguments는 넘어오지 않는 것처럼 보이는 함수의 인자에 접근하고 있다. 이 부분은 혼란을 야기한다, 그래서 함수의 선언 부분에서는 실핸 부분을 보지 않고서라도, 어떤 인자가 넘어오는지 명확히 인지할 수 있어야 한다.

- 이 문제를 해결하기 위해서 es2015에서 제공하는 나머지 매개변수를 적용한다, function funName(...restParam)

![rest parameter](https://rainsoft.io/content/images/2016/08/2-2.jpg)

The rest parameter is present in the function signature as a regular parameter, only prefixed by three dots .... The rest parameter puts into an array the arguments passed to function on invocation. And as any other parameter, it can be named accordingly to its meaning.
나머지 매개변수는 함수 

Let's get rid of arguments object from the above example and use a rest parameter:

``` javascript
Try in repl.it
function sum(...numbers) {  
  return numbers.reduce(function(sum, el) {
    return sum + el;
  });
}
sum(10, 5, 2); // => 17  
```
numbers rest parameter contains an array of arguments [10, 5, 2] that function sum(10, 5, 2) was invoked with. 
The problems specific to arguments object are now solved:

- numbers can call .reduce() array method
- It is possible to name the variable according to its meaning, in our case as an array of numbers
- The function signature function sum(...numbers) obviously indicates that arguments list is going into numbers parameter.

Without considerable amount of refactoring, but for a considerable code readability arguments object should be migrated to rest parameters.

