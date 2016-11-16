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
