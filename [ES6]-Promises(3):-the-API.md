> 이 문서는 http://www.2ality.com/2014/10/es6-promises-api.html 를 번역한 내용입니다.

> 또한 [[ES6] Promises(2):the API](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API) 다음 편 입니다.


#목차
1. [Promises](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#1-promises)
2. [첫번째 예제](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#2-%EC%B2%AB%EB%B2%88%EC%A7%B8-%EC%98%88%EC%A0%9C)
3. [Promises 생성과 사용](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#3-promises-%EC%83%9D%EC%84%B1%EA%B3%BC-%EC%82%AC%EC%9A%A9)
 1. [promise 생산(Producing a promise)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#3-1-promise-%EC%83%9D%EC%82%B0producing-a-promise)
 2. [promise의 사용(Consuming a promise)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#32-promise%EC%9D%98-%EC%82%AC%EC%9A%A9consuming-a-promise)
 3. [성공 또는 거절만 처리(Only handling fulfillments or rejections)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#33-%EC%84%B1%EA%B3%B5-%EB%98%90%EB%8A%94-%EA%B1%B0%EC%A0%88%EB%A7%8C-%EC%B2%98%EB%A6%AConly-handling-fulfillments-or-rejections)
4. [예제(Examples)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#4-%EC%98%88%EC%A0%9Cexamples)
 1. [예제:promisifying XMLHttpRequest](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#41-%EC%98%88%EC%A0%9Cpromisifying-xmlhttprequest)
 2. [예제: delaying an activity](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#42-%EC%98%88%EC%A0%9C-delaying-an-activity)
 3. [예제: timing out a promise(promise의 시간 초과)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#43-%EC%98%88%EC%A0%9C-timing-out-a-promisepromise%EC%9D%98-%EC%8B%9C%EA%B0%84-%EC%B4%88%EA%B3%BC)
5. [then() 체이닝](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#5-then-%EC%B2%B4%EC%9D%B4%EB%8B%9D)
 1. [일반 값으로 해결(Resolving with normal values)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#51-%EC%9D%BC%EB%B0%98-%EA%B0%92%EC%9C%BC%EB%A1%9C-%ED%95%B4%EA%B2%B0resolving-with-normal-values)
 2. [Resolving with thenable(thenable로 해결)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#52-resolving-with-thenablethenable%EB%A1%9C-%ED%95%B4%EA%B2%B0)
6. [에러 처리](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API#6-%EC%97%90%EB%9F%AC-%EC%B2%98%EB%A6%AC)
 1. [예외를 잡자(Catching exceptions)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API#6-1-%EC%98%88%EC%99%B8%EB%A5%BC-%EC%9E%A1%EC%9E%90catching-exceptions)
 2. [에러's 체이닝(Chaining errors)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API#6-2-%EC%97%90%EB%9F%ACs-%EC%B2%B4%EC%9D%B4%EB%8B%9Dchaining-errors)
7. [Composition](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API#7composition)
 1. [Promise.all() 에 의한 map()](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API#7-1-promiseall-%EC%97%90-%EC%9D%98%ED%95%9C-map)
 2. [Promise.race()를 통한 Timing out](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API#7-2-promiserace%EB%A5%BC-%ED%86%B5%ED%95%9C-timing-out)
8. [항상 비동기인 Promises](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API#8-%ED%95%AD%EC%83%81-%EB%B9%84%EB%8F%99%EA%B8%B0%EC%9D%B8-promises)
9. [Cheat sheet: the ECMAScript 6 promise API](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API#9-cheat-sheet-the-ecmascript-6-promise-api)
 1. [Glossary(어휘)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API#91-glossary%EC%96%B4%ED%9C%98)
 2. [생성자](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API#92-%EC%83%9D%EC%84%B1%EC%9E%90)
 3. [정적 메소드](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API#93-%EC%A0%95%EC%A0%81-%EB%A9%94%EC%86%8C%EB%93%9C)
 4. [인스턴스 프로토타입 메서드's](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API#94-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EB%A9%94%EC%84%9C%EB%93%9Cs)
10. [promises의 장단점](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API#10-promises%EC%9D%98-%EC%9E%A5%EB%8B%A8%EC%A0%90)
 1. [장점](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API#101-%EC%9E%A5%EC%A0%90)
 2. [단점](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API#102-%EB%8B%A8%EC%A0%90)
11. [Promises와 generators](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API#11-promises%EC%99%80-generators)


#12. Debugging promises
 비동기 코드를 디버깅하는 가장 주요 난점은 비동기 함수와 메서드 호출이 포함 되어있다는 것입니다. 비동기 호출은 하나의 *task*에서 생성되어, 새로운 *task*에서 수행되어집니다. 만약 새로운 *task*가 잘못되면, *stack trace*는 이전 *task*에 관한 정보를 포함하지 않은 그 *task*만 적용합니다. 그래서 당신은 비동기 프로그래밍에서 훨씬 적은 디버깅 정보를 사용 해야합니다.<br><br>
최근에 구글 크롬에서 비동기 코드를 디버깅 할 수 있게 되었습니다.
그것은 아직 완벽하게 *promises*를 서포트 해주지 않습니다. 그러나 정상적으로 비동기 호출을 어떻게 잘 다룰 수 있는지에 대해선 인상깊습니다.<br>
예를 들어 아래 코드에 따르면, 첫번째는 비동기적으로 두번째를 호출하고, 이는 다시 세번째를 호출합니다.

```` javascript
    function first() {
        setTimeout(function () { second('a') }, 0); // (A)
    }
    function second(x) {
        setTimeout(function () { third('b') }, 0); // (B)
    }
    function third(x) {
        debugger;
    }
    first();
````

아래 스크린샷을 보면, 디버거는 3개 함수를 포함하고 있는 *stack trace*를 보여 줍니다. 심지어 익명 함수(A),(B)까지 포함되어져 있습니다.
![스샷](http://3.bp.blogspot.com/-kpQjy16k4xw/VDEiTaCfsaI/AAAAAAAAA4w/fqG3nssNZ5Q/s400/debugging_callbacks.png)

#13. promises 의 내부
이번 섹션에서는 *promises*를 다른 각도로 다가갈 것 입니다.: **API**을 어떻게 사용하는지 배우는 대신에, 우리는 그것을 간단하게 구현하는 것을 볼 것입니다.<br>
저는 이 다른 각도를 통해 *promises*를 만드는 것에 크게 도움이 되었습니다.<br><br>
**DemoPromise** 라고 부르는 promise를 구현하고, [GitHub](https://github.com/rauschma/demo_promise)통해 사용할수 있습니다.
쉽게 이해하게 하려고, API와 완전하게 일치하지 않게 하였습니다. 하지만 실제 당신이 직면한 도전에 인사이트를 줄만큼 충분히 가깝게 되어있습니다.<br><br>
**DemoPromise**는 생성자와 3가지 prototype 메서드가 있습니다.:
- DemoPromise.prototype.resolve(value)
- DemoPromise.prototype.reject(reason)
- DemoPromise.prototype.then(onFulfilled, onRejected)

즉 *resolve*와  *reject*는 메서드 입니다.(생성자 매개변수로 전달되는 함수)

##13.1 A stand-alone promise
우리의 첫번째 구현은 최소한의 기능만 갖춘 독립형 *promise* 입니다.

- *promise*를 생성 할수 있습니다.
- *promise*는 *resolve*또는 *reject* 할 수 있으며, 오직 한번 만 할수 있습니다.
- `then()`을 통해 반응(*reactions*)(콜백)을 등록 할 수 있습니다. 이 메서드는 아직 체이닝을 할 수 없습니다. - 그 어느것도 반환 하지 않습니다. 그것은 *promise*가 이미 처리 됬는지 안됬는지 상관없이 독립적으로 작동 해야 할 것입니다.

다음과 같이 하면 첫번째 구현한 것을 사용할 수 있습니다.

```` javascript
    var dp = new DemoPromise();
    dp.resolve('abc');
    dp.then(function (value) {
        console.log(value); // abc
    });
````

다음 다이어그램은 어떻게 우리의 첫번째 **DemoPromise** 가 작동하는지 보여줍니다.
![다이어그램](http://1.bp.blogspot.com/-YtdGXGH__gk/VDEiTAXcqtI/AAAAAAAAA3s/3IwMMkVJSps/s1600/promise1_simple.jpg)

첫번째로 2가지 경우를 처리하는 `then()`에 대해 설명하겠습니다. 

- If the promise is still pending, it queues invocations of onFulfilled and onRejected, to be used when the promise is settled.
- 
- If the promise is already fulfilled or rejected, onFulfilled or onRejected can be invoked right away.

