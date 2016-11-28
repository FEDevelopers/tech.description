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
12. [Debugging promises](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(3):-the-API#12-debugging-promises)
13. [promises 의 내부](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(3):-the-API#13-promises-%EC%9D%98-%EB%82%B4%EB%B6%80)
 1. [A stand-alone promise](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(3):-the-API#131-a-stand-alone-promise)
 2. [체이닝](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(3):-the-API#132-%EC%B2%B4%EC%9D%B4%EB%8B%9D)
 3. [Flattening(편평한)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(3):-the-API#133-flattening%ED%8E%B8%ED%8F%89%ED%95%9C)
 4. [상세한 Promise 상태](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(3):-the-API#134-%EC%83%81%EC%84%B8%ED%95%9C-promise-%EC%83%81%ED%83%9C)
 5. [예외](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(3):-the-API#135-%EC%98%88%EC%99%B8)
 6. [공개 생성자패턴](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(3):-the-API#136-%EA%B3%B5%EA%B0%9C-%EC%83%9D%EC%84%B1%EC%9E%90%ED%8C%A8%ED%84%B4)
14. [추가적인 2가지 유용한 promise 메서드's](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(3):-the-API#14-%EC%B6%94%EA%B0%80%EC%A0%81%EC%9D%B8-2%EA%B0%80%EC%A7%80-%EC%9C%A0%EC%9A%A9%ED%95%9C-promise-%EB%A9%94%EC%84%9C%EB%93%9Cs)
 1. [done()](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(3):-the-API#141-done)
 2. [finally()](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(3):-the-API#142-finally)
15. [ES6 호환되는 promise 라이브러리](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(3):-the-API#15-es6-%ED%98%B8%ED%99%98%EB%90%98%EB%8A%94-promise-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC)
16. [레거시 비동기 코드와의 인터페이스](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(3):-the-API#16%EB%A0%88%EA%B1%B0%EC%8B%9C-%EB%B9%84%EB%8F%99%EA%B8%B0-%EC%BD%94%EB%93%9C%EC%99%80%EC%9D%98-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)
 1. [Node.js 인터페이스](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(3):-the-API#161-nodejs-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)
 2. [jQuery 인터페이스](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(3):-the-API#162-jquery-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)
17. [Further reading](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(3):-the-API#17-further-reading)


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

다음 다이어그램은 첫번째 **DemoPromise** 가 어떻게 작동하는지 알려줍니다.

![다이어그램](http://1.bp.blogspot.com/-YtdGXGH__gk/VDEiTAXcqtI/AAAAAAAAA3s/3IwMMkVJSps/s1600/promise1_simple.jpg)

첫번째로 2가지 경우를 처리하는 `then()`에 대해 설명하겠습니다. 

- 만약 *promise*가 아직도 *pending* 상태라면, *promise*가 결정(*settled*)될때 사용되는 *onFulfilled* 와 *onRejected* 호출을 큐에 삽입합니다.
- 만약 이미 *fulfilled* 또는 *rejected*상태 라면, *onFulfilled* 또는 *onRejected*는 곧바로 호출 할 수 있습니다.

```` javascript

    DemoPromise.prototype.then = function (onFulfilled, onRejected) {
        var self = this;
        var fulfilledTask = function () {
            onFulfilled(self.promiseResult);
        };
        var rejectedTask = function () {
            onRejected(self.promiseResult);
        };
        switch (this.promiseState) {
            case 'pending':
                this.fulfillReactions.push(fulfilledTask);
                this.rejectReactions.push(rejectedTask);
                break;
            case 'fulfilled':
                addToTaskQueue(fulfilledTask);
                break;
            case 'rejected':
                addToTaskQueue(rejectedTask);
                break;
        }
    };
    function addToTaskQueue(task) {
        setTimeout(task, 0);
    }
````

`resolve()`는 다음과 같이 작동합니다. : <br>
만약 이미 *promise*가 해결(*settled*)되었다면, *promise*는 아무것도 하지 않습니다.(*promise*는 오로지 한번만 결정됩니다.) 그렇지 않으면, *promise* 상태는 *fulfilled*로 변경되고, 결과는 `this.promiseResult`에 캐시됩니다. 지금 까지 큐에 추가 되었던 모든 *fulfillment* 반응(*reactions*)들은 바로 실행 될 것입니다.

```` javascript

    Promise.prototype.resolve = function (value) {
        if (this.promiseState !== 'pending') return;
        this.promiseState = 'fulfilled';
        this.promiseResult = value;
        this._clearAndEnqueueReactions(this.fulfillReactions);
        return this; // enable chaining
    };
    Promise.prototype._clearAndEnqueueReactions = function (reactions) {
        this.fulfillReactions = undefined;
        this.rejectReactions = undefined;
        reactions.map(addToTaskQueue);
    };
````

`reject()`는 `resolve()`랑 비슷합니다.

##13.2 체이닝
The next feature we implement is chaining:
다음으로 체이닝을 구현 해봅시다.:

- `then()`은 *onFulfilled*나 *onRejected* 이던간에 해결된 *promise*를 반환합니다.
- 만약 *onFulfilled* 또는 *onRejected*가 놓쳤어도, 어쨋든 그들은  `then()`에 의해 반환된 *promise*를 받았을 것입니다. 

![체이닝](http://2.bp.blogspot.com/-pZ2agjPL54Y/VDEiTUClecI/AAAAAAAAA30/3zAmth5qnrE/s1600/promise2_chaining.jpg)

분명하게 `then()`만 변경됩니다.

```` javacript
    DemoPromise.prototype.then = function (onFulfilled, onRejected) {
        var returnValue = new DemoPromise(); // (A)
        var self = this;
    
        var fulfilledTask;
        if (typeof onFulfilled === 'function') {
            fulfilledTask = function () {
                var r = onFulfilled(self.promiseResult);
                returnValue.resolve(r); // (B)
            };
        } else {
            fulfilledTask = function () {
                returnValue.resolve(self.promiseResult); // (C)
            };
        }
    
        var rejectedTask;
        if (typeof onRejected === 'function') {
            rejectedTask = function () {
                var r = onRejected(self.promiseResult);
                returnValue.resolve(r); // (D)
            };
        } else {
            rejectedTask = function () {
                // Important: we must reject here!
                // Normally, result of `onRejected` is used to resolve
                returnValue.reject(self.promiseResult); // (E)
            };
        }
        ...
        return returnValue; // (F)
    };
````

`then()`은 `new promise`를 생성하여 반환합니다.((A),(F)라인) 게다가, `fulflledTask`와 `rejectedTask`는 다르게 설정되었습니다. 
- *onFulfilled* 결과는 `returnValue`를 해결 하는데 사용됩니다.((B)라인)
 - 만약 *onFulfilled*가 누락된 경우, `returnValue`를 해결하는데 *fulfullment*를 사용합니다.((C)라인)
- *onRejected*의 결과는 `returnValue`를 해결하는데 사용됩니다.(거절이 아닌(*reject*))((D)라인)
 - 만약 *onRejected*가 누락된 경우, `returnValue`를 해결하는데 거절(*rejection*) 값을 사용합니다.

##13.3 Flattening(편평한)
> 역자주 : Flattening 은 해석하기 애매한 단어 임으로, 영어로 표기

**Flattening** is mostly about making chaining more convenient: Normally, returning a value from a reaction passes it on to the next then(). 
**Flattening** 은 체이닝을 좀더 편리하게 만드는 것입니다. : 일반적으로, 반응(*reaction*) 통해서 값을 반환하면 다음 `then()`으로 전달 됩니다. 만약 우리가 *promise*를 반환하면, 그것이 우리를 위해서 풀리지 않았다면, 그것은 아래 예제와 같이 좋을 것입니다. (If we return a promise, it would be nice if it could be “unwrapped” for us, like in the following example:)

```` javascript
    asyncFunc1()
    .then(function (value1) {
        return asyncFunc2(); // (A)
    })
    .then(function (value2) {
        // value2 is fulfillment value of asyncFunc2() promise
        console.log(value2);
    });
````

우리는 라인(A)에서 *promise*를 반환했습니다. 그리고 현재 메서드에서 중첩된 `then()`을 호출 하지 않았습니다. 우리는 메서드 결과에 `then()`을 호출합니다. 따라서, `then()`은 더이상 중첩되지 않고, 모든것이 편평(*flat*)하게 유지됩니다.<br><br>
우리는 `resolve()`가 *flattening*을 수행 할 수 있도록 구현하겠습니다.

- *promise Q*로 *promise P*를 해결(*Resolving*)하는 것은 *Q's*의 해결,결정(*settlement*)은 *P's*의 반응(*reactions*)으로 전달되는 것을 의미합니다.
- *P*는 *Q*에 가둬집니다. :  더이상 해결 할수 없습니다.(*rejected*포함). 그리고 그 상태와 결과는 *Q's*와 항상 같습니다.

우리가 만약 *Q*가 `thenable`이 되도록 허락 한다면, 좀더 제네릭하게 만들 수 있습니다.(*promise*를 대신하여)

![플래튼](http://2.bp.blogspot.com/-yuykRwpwHFw/VDEiT1k2s9I/AAAAAAAAA34/AN8IZI5uoGw/s1600/promise3_flattening.jpg)

위에 말한 것처럼 가두기를(*locking-in*) 구현하기 위해 우리는 새로운 `boolean flag`인 `this.alreadyResolved`를 소개합니다. 일단 flag가 `true`가 되면, `this`는 갇히게 되고, 더이상 해결(*resolve*) 할 수 없습니다. `this`는 여전히 `pending` 상태인것을 기억하십시오. 왜냐하면 그 상태는 지금 *promise*에 고정 된 것과 같기 때문입니다. 

```` javascript
    DemoPromise.prototype.resolve = function (value) {
        if (this.alreadyResolved) return;
        this.alreadyResolved = true;
        this._doResolve(value);
        return this; // enable chaining
    };
````

이제 실제 해결은 private 메서드인 `_doResolve()`에서 발생합니다.

```` javascript
    DemoPromise.prototype._doResolve = function (value) {
        var self = this;
        // Is `value` a thenable?
        if (value !== null && typeof value === 'object'
            && 'then' in value) {
            addToTaskQueue(function () { // (A)
                value.then(
                    function onFulfilled(value) {
                        self._doResolve(value);
                    },
                    function onRejected(reason) {
                        self._doReject(reason);
                    });
            });
        } else {
            this.promiseState = 'fulfilled';
            this.promiseResult = value;
            this._clearAndEnqueueReactions(this.fulfillReactions);
        }
    };
````

*flattening*은 (A)라인에서 수행됩니다. : 만약 `value`가 *fulfilled*면, 우리는 `self`가 *fulfilled*되길 원합니다. 그리고 만약 `value`가 *rejected*라면, 우리는 `self`가 *rejected* 되길 원합니다.<br>
private메서드인 `_doResovle()`와 `_doReject()` 통해 발생하고, `alreadyResolved`를 통해 보호받습니다. 

##13.4 상세한 Promise 상태
 체이닝 통해서 *promises* 상태는 점점 더 복잡해집니다.

![머지?](http://1.bp.blogspot.com/-Nz5wt9gH8Jc/VDEiUlFWj1I/AAAAAAAAA4k/813hgpWviw0/s1600/promise_states_all.jpg)

만약 당신이 *promises*만 사용하면, 당신은 일반적으로 단순한 세계관을 사용하고 가둬둔(*locking-in*) *promise*를 무시할 수 있습니다. 가장 중요한 상태-관련 개념은 **settledness**를 유지하는 것입니다. *promise*는 이행(*fulfilled*)되거나, 거절(*rejected*)되면 해결(*settled*) 됩니다.*promise*가 해결(*settled*)되면 더이상 변하지 않습니다. (상태 및 이행(*fulfillment*) 또는 거절(*rejection*) 값)
<br><br>
만약 당신이 *promise*를 수행하길 원한다면, 문제를 해결하기도 어렵고, 지금 그것을 이해하기는 어렵습니다.

- 직관적으로 해결됨(*resolved*)은 더이상 (직접적으로)해결 할수 없다는 의미입니다. *promise*는 해결(*settled*)되었거나, 갇혀(*locked in*)있었으면, 해결(*resolved*)된것입니다. 스펙 인용 : "*promise*가 해결되지 않았으면, 항상 보류(*pending*) 상태 입니다. 해결된 *promise*는 보류(*pending*), 이행(*fulfilled*), 거절(*rejected*) 상태일 수 있습니다."
- 해결된 *promise* 꼭 해결된(*settling*) 상태는 아닐수 있습니다. : 당신은 보류(*pending*)된 상태로 *promise*를 해결(*resolve*) 할 수 있습니다.
- 해결은 거절된 상태입니다. : 당신은 거절(*reject*)된 *promise*로 해결(*resolving*) 함으로써 *promise*를 거절(*reject*) 할 수 있습니다.

##13.5 예외
As our final feature, we’d like our promises to handle exceptions in user code as rejections. For now, “user code” means the two callback parameters of then().
마지막으로 우리는, 거절(*rejections*)을 사용자 코드단에서 예외로 처리하고 싶습니다. 지금부터 **사용자 코드(user code)**는 `then()`의 2개 콜백 파라미터를 의미합니다.

![예외](http://2.bp.blogspot.com/-x6hBT5B_yw4/VDEiULOJwII/AAAAAAAAA4E/aUTml-VNKRk/s1600/promise4_exceptions.jpg)

The following excerpt shows how we turn exceptions inside onFulfilled into rejections – by wrapping a try-catch around its invocation in line (A).

다음 발췌한 부분은 `onFulfilled` 내부 예외를 거절(*rejections*)로 변환하는 방법에 대해 보여줍니다.

```` javascript
    var fulfilledTask;
    if (typeof onFulfilled === 'function') {
        fulfilledTask = function () {
            try {
                var r = onFulfilled(self.promiseResult); // (A)
                returnValue.resolve(r);
            } catch (e) {
                returnValue.reject(e);
            }
        };
    } else {
        fulfilledTask = function () {
            returnValue.resolve(self.promiseResult);
        };
    }
````

##13.6 공개 생성자패턴
만약 **DemoPromise**를 실제 **promise** 구현체로 변경하고 싶다면, 공개 생성자 패턴을 구현 해야 합니다.  
**ES6** *Promises*는 메서드를 통해 해결(*resolved*)되거나 거절(*rejected*)되어지지 않습니다. 그러나 생성자 콜백 파라미터 실행자에  전달되어지는 함수를 통해서..(but via functions that are handed to the executor, the callback parameter of the constructor)

![공개생성자패턴](http://4.bp.blogspot.com/-GTbj_y1eicI/VDEiUrwlM-I/AAAAAAAAA4I/K4EkfviNrss/s1600/promise5_everything.jpg)

만약 실행자가 `then`으로 예외를 던진다면, 그것의 *promise*는 거절(*rejected*)임에 틀림없습니다.

#14. 추가적인 2가지 유용한 promise 메서드's
> 이번 섹션에서는 ES6 promises에 쉽게 추가 할 수 있는 2가지 유용한 메서드를 보고자 합니다. 많은 promise 라이브러리를 가지고 있습니다.

##14.1 done()
 몇몇 *promise*를 여러개 체이닝 호출을 하면 에러를 조용히 삭제할 수도 있습니다.  
예를들어:

```` javascript
    function doSomething() {
        asyncFunc()
        .then(f1)
        .catch(r1)
        .then(f2); // (A)
    }
````

만약 (A)라인 `then()`이 거절(*rejection*)이면, 아무데서도 처리 되지 않습니다. *promise* 라이브러리 **Q**는 메서드 체인 마지막 요소에 `done()`을 사용합니다. 마지막 `then()` 을 대체합니다.(1~2개의 인자가 있슴)

```` javascript
    function doSomething() {
        asyncFunc()
        .then(f1)
        .catch(r1)
        .done(f2);
    }
````

또는 마지막 `then()` 이후에 삽입됩니다. (0개 인수를 가짐)

```` javascript
    function doSomething() {
        asyncFunc()
        .then(f1)
        .catch(r1)
        .then(f2)
        .done();
    }
````

[Q 문서에 인용문](https://github.com/kriskowal/q/wiki/API-Reference#promisedoneonfulfilled-onrejected-onprogress):  
- `done`의 황금룰과 vs `then` 의 사용은 다음과 같습니다.: 당신의 *promise*를 누군가에게 반환 하거나, 만약 체이닝이 끝이라면 제거를 위해 `done`을 호출 하십시오. 왜냐하면, `catch` 핸들러 자체적으로 에러를 던질 수 있기 때문에 `catch`로 종료하는 것은 유용하지 못합니다.

**ECMAScript6**로 `done()`을 구현하는 방법

```` javascript
    Promise.prototype.done = function (onFulfilled, onRejected) {
        this.then(onFulfilled, onRejected)
        .catch(function (reason) {
            setTimeout(() => { throw reason }, 0);
        });
    };
````

`done()`의 기능은 유용하지만, **ECMAScript6**에는 추가 되지 않습니다. 왜냐하면, 이런 일련의 과정은 미래에 디버거가 자동적으로 수행될 것 입니다.

##14.2 finally()
때론 오류가 발생하던지 말던지, 독립적인 액션을 수행하길 원할 때가 있습니다. 예를들어, 작업이 마친후 리소스를 정리를 해야합니다. 그것이 바로 `finally()` 메서드 입니다. 예외처리에서 `finally` 구문과 비슷하게 작동합니다.  
`finally()` 는 인자가 없는 콜백을 받지만, 해결(*resolution*)인지 거절(*rejection*)인지 통보 받습니다.

```` javascript
    createResource(...)
    .then(function (value1) {
        // Use resource
    })
    .then(function (value2) {
        // Use resource
    })
    .finally(function () {
        // Clean up
    });
````

*Domenic Denicola*가 `finally()`를 구현한 [목적](https://github.com/domenic/promises-unwrapping/issues/18)입니다.

```` javascript
    Promise.prototype.finally = function (callback) {
        let p = this.constructor;
        // We don’t invoke the callback in here,
        // because we want then() to handle its exceptions
        return this.then(
            // Callback fulfills: pass on predecessor settlement
            // Callback rejects: pass on rejection (=omit 2nd arg.)
            value  => p.resolve(callback()).then(() => value),
            reason => p.resolve(callback()).then(() => { throw reason })
        );
    };
````

콜백은 어떻게 수신자(this)의 확정(*settlement*)이 처리되는 방법을 결정합니다.

- 콜백이 예외를 던지거나, *promise then*에 거절(*rejected*)을 반환하면, 거절 값(*rejection value*)이 됩니다.
- 그렇지 않으면, 수신자(receiver)의 결정은 `finally()`에 반환된 *promise* 값으로 됩니다. 우리는 메서드 체인에서 `finally()`를 가져오게 됩니다.

- 예제1 (by [Jake Archibald](https://gist.github.com/jakearchibald/785f79b0dea5bfe0c448)): `finally()`로 스피너를 숨기는 방법. 간단버전:

```` javascript
    showSpinner();
    fetchGalleryData()
    .then(data => updateGallery(data))
    .catch(showNoDataError)
    .finally(hideSpinner);
````

- 예제2 (by [Kris Kowal](https://github.com/domenic/promises-unwrapping/issues/18#issuecomment-27707922) ) 서버 다운 테스트

```` javascript
    var HTTP = require("q-io/http");
    var server = HTTP.Server(app);
    return server.listen(0)
    .then(function () {
        // run test
    })
    .finally(server.stop);
````

#15 ES6 호환되는 promise 라이브러리
많은 promise 라이브러리가 밖에 있습니다. 다음은 *ECMAScript 6 API*에 따르며, 지금 바로 사용 할수 있으며, 쉽게 네이티브 ES6로 나중에 마이그레이션 할 수 있습니다.

- [RSVP.js](https://github.com/tildeio/rsvp.js/) : Stefan Penner에 의해 만들어진 RSVP.JS는 *ES6 Promise API* 상위 집합니다.
 - Jake Archibald의 [ES6-Promise](https://github.com/stefanpenner/es6-promise)는 RSVP.js에서 ES6 API만 뽑아낸것입니다.
- Kyle Simpson의 [Native Promise Only](https://github.com/getify/native-promise-only)는 엄격한 스펙 정의에 가능한한 근접한 네이티브 ES6 promises를 위한 polyfill 입니다.
-  Calvin Metcalf의 [Lie](https://github.com/calvinmetcalf/lie)는 Promises/A+ spec 을 구현한 작고 퍼포먼스있는 promise 라이브러리입니다.
- Kris Kowal 의 [Q.Promise](https://github.com/kriskowal/q#using-qpromise)는 ES6 API입니다.
- 마지막으로 Paul Millr의 [ES6 Shim](https://github.com/paulmillr/es6-shim)는 *Promise*를 포함합니다.

#16.레거시 비동기 코드와의 인터페이스
당신이 *promise* 라이브러리를 사용하면, 때론 *promise*기반이 아닌 비동기 코드가 필요할 때가 있습니다. 이번 섹션은 *Node-js*스타일 비동기함수와 *jQuery deferreds*가 어떻게 작동하는지 설명하고자 합니다.

##16.1 Node.js 인터페이스
*promise* 라이브러리 **Q**는 Node.js 스타일 콜백을 사용하는 함수를 promise를 반환하는 함수로 변환하기 위한 몇몇 [툴 함수](https://github.com/kriskowal/q/wiki/API-Reference#interfacing-with-nodejs-callbacks)를 가지고 있습니다.(반대 함수도 있다. promise를 반환하는 함수를 Node.js 스타일 콜백으로)  
예를들어:

```` javascript
    var readFile = Q.denodeify(FS.readFile);
    
    readFile('foo.txt', 'utf-8')
    .then(function (text) {
        ...
    });
````

[deonodify](https://github.com/matthew-andrews/denodeify/)는 알림기능만 제공하고 ECMAScript6 promise API를 따르는 마이크로 라이브러리 입니다. 

##16.2 jQuery 인터페이스
*jQuery*는 *promise*와 비슷한 **deferreds**를 가지고 있습니다. 그러나 호환성을 방해하는 몇몇 [다른점](https://github.com/kriskowal/q/wiki/Coming-from-jQuery)이 있습니다. **deferreds**의 `then()`은 거의 *ES6 promises*와 비슷합니다.(에러를 캐치 할 수 없는 주요한 다른점이 있습니다.) 따라서 `Promise.resolve()`로 *jQuery deferred*를 *ES6 promise*로 변환 시킬수 있습니다.

```` javascript
    Promise.resolve(
        jQuery.ajax({
            url: 'somefile.html',
            type: 'GET'
        }))
    .then(function (data) {
        console.log(data);
    })
    .catch(function (reason) {
        console.error(reason);
    });
````

#17. Further reading

- [1]: “[Promises/A+](https://promisesaplus.com/)”, edited by Brian Cavalier and Domenic Denicola (the de-facto standard for JavaScript promises)
- [2]: “[JavaScript Promises: There and back again](https://developers.google.com/web/fundamentals/getting-started/primers/promises)” by Jake Archibald  (good general intro to promises)
- [3]: “[Promise Anti-Patterns](http://taoofcode.net/promise-anti-patterns/)” by Tao of Code (tips and techniques)
- [4]: “[Promise Patterns](https://www.promisejs.org/patterns/)” by Forbes Lindesay
- [5]: “[The Revealing Constructor Pattern](https://blog.domenic.me/the-revealing-constructor-pattern/)” by Domenic Denicola (this pattern is used by the Promise constructor)
- [6]: “[Debugging Asynchronous JavaScript with Chrome DevTools](https://www.html5rocks.com/en/tutorials/developertools/async-call-stack/)” by Pearl Chen
- [7]: [Iterators and generators in ECMAScript 6](http://www.2ality.com/2013/06/iterators-generators.html)