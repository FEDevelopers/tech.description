> 이 문서는 http://www.2ality.com/2014/10/es6-promises-api.html 를 번역한 내용입니다.

#목차
1. [Promises](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#1-promises)
2. [첫번째 예제](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#2-%EC%B2%AB%EB%B2%88%EC%A7%B8-%EC%98%88%EC%A0%9C)
3. [Promises 생성과 사용](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#3promises-%EC%83%9D%EC%84%B1%EA%B3%BC-%EC%82%AC%EC%9A%A9)
 1. [promise 생산(Producing a promise)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#3-1-promise-%EC%83%9D%EC%82%B0producing-a-promise)


> 이번 포스트는 일반적인 **promise**를 통한 비동기 프로그래밍과 **ES6 promise API**에 일부를 소개하고자 합니다. 2개의 비동기 프로그래밍 포스트 중 2번째이며, 충분히 이해하기 위해선 [1번째 포스트](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D)를 읽어보는 것이 좋을 것입니다.

#1. Promises
 **Promises**는 비동기 프로그래밍의 한 부분을 도와주는 패턴입니다.(함수 또는 메서드를 비동기적으로 결과를 받는 것) 이런 기능을 구현하기 위해선 당신은 결과를 위해 지정해 놓은 객체인(an object that is a placeholder for the result) *promise* 를 반환해야 합니다. <br>함수 호출자는 연산된 결과를 알려주는 **promise**와 함께 콜백을 등록합니다.(The caller of the function registers callbacks with the promise to be notified once the result has been computed) 이 함수는 **promise**를 통해 결과를 전송합니다.<br><br>
자바스크립트 **promises**의 사실상 표준은 [**Promises/A+**](https://promisesaplus.com/) 이라고 부릅니다. ECMAScript6 **promise** API는 표준을 따릅니다.

#2. 첫번째 예제
 첫번째 예제를 봅시다, to give you a taste of what working with promises is like.<br>
**Node-js**스타일 콜백으로 비동기적으로 파일 읽는 법은 다음과 같습니다.
 
```` javascript
    fs.readFile('config.json',
        function (error, text) {
            if (error) {
                console.error('Error while reading config file');
            } else {
                try {
                    var obj = JSON.parse(text);
                    console.log(JSON.stringify(obj, null, 4));
                } catch (e) {
                    console.error('Invalid JSON in file');
                }
            }
        });
````

같은 기능을 **promise** 로 구현하면 다음과 같습니다.

```` javascript
    readFilePromisified('config.json')
    .then(function (text) { // (A)
        var obj = JSON.parse(text);
        console.log(JSON.stringify(obj, null, 4));
    })
    .catch(function (reason) { // (B)
        // File read error or JSON SyntaxError
        console.error('An error occurred', reason);
    });
````

**promise**도 여전히 콜백이 존재합니다. 그러나 **promise**는 결과를 호출 할 수 있는 메서드를 통해 제공됩니다. (`then()`,`catch()`) (B)라인 안에 error 콜백은 2가지의 편리성을 가지고 있습니다. 첫 번째, 에러 처리를 한가지 방식으로 할 수 있습니다. 두 번째로, 당신은 `readFilePromisified()`와 (A)라인의 콜백 둘다 에러를 핸들링 할 수 있습니다.

#3. Promises 생성과 사용
> **promise**가 어떻게 작동하는지 공급자(Producer)와 소비자(consumer) 측면에서 알아봅시다.

## 3-1. promise 생산(Producing a promise)
 공급자로서, **promise**를 생성하고 결과를 전송합니다.

```` javascript
    var promise = new Promise(
        function (resolve, reject) { // (A)
            ...
            if (...) {
                resolve(value); // success
            } else {
                reject(reason); // failure
            }
        });
````

**promise**는 항상 아래 3가지 상태 중(상호배타적인) 1가지 상태입니다.
- 대기(pending) : 아직 결과 처리가 안 됐다.
- 성공(Fulfilled) : 성공적으로 완료되었다.
- 거절(rejected) : 처리되는 동안 실패가 발생하였다.

**promise**는 성공(fulfilled)이나 거절(rejected) 중 하나로 고정됩니다.(연산이 끝났을 때) (A promise is settled (the computation it represents has finished) if it is either fulfilled or rejected.)
**promise** 상태는 한 번만 지정되며, 그 후 상태를 유지합니다. 그 후에 상태를 바꾸려고 해도 아무 변화가 없습니다.

![promise state](http://4.bp.blogspot.com/-iiX2B0bNZe4/VDEiVTNrpqI/AAAAAAAAA4Q/selZM4dBM7k/s1600/promise_states_simple.jpg)

`new Promise()`의 파라미터는( (A)라인 시작점 ) 집행자(**executor** 모호한 단어라 이하 영문표기로 명칭)라고 부릅니다.
- 만약 연산이 잘 되었다면, **executor**는 `resolve()` 통해 결과를 전송합니다. 보통 **promise** 성공(fulfills)을 말합니다.(promise가 resolve였지만 실제로 아닐 경우 뒤에 설명하겠습니다.)
- 만약 에러가 발생할 경우, **executor**는 `reject()`를 통해 promise-소비자(consumer)에게 통보 합니다. 즉 **promise**는 거절(reject) 상태입니다.

##3.2 promise의 사용(Consuming a promise)
**promise** 소비자(consumer)로서, 당신은 `then()` 메서드에 등록한 콜백을 통해 성공 또는 거절 상태 알림을 받게 됩니다.

```` javascript
    promise.then(
        function (value) { /* fulfillment */ },
        function (reason) { /* rejection */ }
    );
````

**promise** 상태가 설정되면 더는 어느 것도 변하지 않게 됩니다. 그래서 **promise**는 비동기함수를 위하여(일회성 결과) 유용하게 만들어졌습니다. 게다가 **promise**는 경쟁상태(race condition)를 일으키지 않습니다. 왜냐면 `then()` 을 **promise** 전이나 상태가 세팅된 후에 실행하든 중요하지 않기 때문입니다.
- 전자의 경우, **promise** 상태가 세팅된 직후 호출됩니다.
- 후자의 경우, **promise** 결과(성공 또는 거절의 결과값)는 캐시 되고, 적절한 반응을 즉시 다룰 수 있게 됩니다.(task 큐와 같이), the promise result (fulfillment value or rejection value) is cached and handed to the appropriate reaction “immediately” (queued as a task).

##3.3 성공 또는 거절만 처리(Only handling fulfillments or rejections)
 만약 당신이 성공에만 관심 있다면, `then()`의 2번째 파라미터를 생략할 수 있습니다.

```` javascript
    promise.then(
        function (value) { /* fulfillment */ }
    );
````

만약 당신이 거절(reject)에만 관심 있다면, 1번째 파라미터를 생략할 수 있습니다. `catch()`메서드는 같은 작동을 하게 해주는 더 적합한 방법입니다. 

```` javascript
    promise.then(
        null,
        function (reason) { /* rejection */ }
    );
    
    // Equivalent:
    promise.catch(
        function (reason) { /* rejection */ }
    );
````

`catch()`메서드를 사용하는 것은 성공 상태를 배제하고 오류만 잡기 위하여 `then()`을 사용할 때 추천하는 방식입니다. 왜냐하면 catch는 나이스한 콜백 식별자이며, 또 동시간대에 여러개 **promise**의 거절상태를 처리할 수 있습니다. (어떻게 하는지 나중에 설명)

#4. 예제(Examples)
> 몇 가지 예제를 통하여 기본적인 빌딩 블록(코드 블록's)을 사용해봅시다.

##4.1 예제:promisifying XMLHttpRequest
 이벤트 기반인 **XMLHttpRequest API** 통해 HTTP GET 메서드를 수행하는 **promise**기반 함수입니다.

```` javascript
    function httpGet(url) {
        return new Promise(
            function (resolve, reject) {
                var request = new XMLHttpRequest();
                request.onreadystatechange = function () {
                    if (this.status === 200) {
                        // Success
                        resolve(this.response);
                    } else {
                        // Something went wrong (404 etc.)
                        reject(new Error(this.statusText));
                    }
                }
                request.onerror = function () {
                    reject(new Error(
                        'XMLHttpRequest Error: '+this.statusText));
                };
                request.open('GET', url);
                request.send();    
            });
    }
````

`httpGet()` 사용방법

```` javascript
    httpGet('http://example.com/file.txt')
    .then(
        function (value) {
            console.log('Contents: ' + value);
        },
        function (reason) {
            console.error('Something went wrong', reason);
        });
````

##4.2 예제: delaying an activity
 **promise** 기반으로 `setTimeout()`을 구현한 `delay()`함수(`Q.delay()`랑 비슷)

```` javascript
    function delay(ms) {
        return new Promise(function (resolve, reject) {
            setTimeout(resolve, ms); // (A)
        });
    }
    
    // Using delay():
    delay(5000).then(function () { // (B)
        console.log('5 seconds have passed!')
    });
````

(A)라인에서 파라미터 없이 `resolve`를 호출합니다.(`resolve(undefined)`를 호출하는 것과 동일). (B)라인에 성공 결과 값은 필요 없습니다. 그냥 통보만 할 뿐 충분합니다.

##4.3 예제: timing out a promise(promise의 시간 초과)
```` javascript
    function timeout(ms, promise) {
        return new Promise(function (resolve, reject) {
            promise.then(resolve);
            setTimeout(function () {
                reject(new Error('Timeout after '+ms+' ms')); // (A)
            }, ms);
        });
    }
````

시간 경과 이후 거절((A)라인) 요청을 취소하진 않으면, 성공 결과가 수행(return)되지 않도록 방지할 뿐입니다.

`timeout()`메서드를 사용하면 다음과 같습니다.

```` javascript
    timeout(5000, httpGet('http://example.com/file.txt'))
    .then(function (value) {
        console.log('Contents: ' + value);
    })
    .catch(function (reason) {
        console.error('Error or timeout', reason);
    });
````

#5. `then()` 체이닝
메소드 호출 결과는 새로운 **promise Q**입니다.

```` javascript
    P.then(onFulfilled, onRejected)
````

이 의미는 *Q*의 `then()`을 호출을 통해서 **promise**기반 흐름을 제어할 수 있게 유지 한다는 것입니다.
- **Q**는 `onFulfilled` 또는 `onRejected` 중 하나에 의해 반환된 것으로 `resolved` 합니다.
- **Q**는 `onFulfilled` 또는 예외를 던진 `onRejected`중 하나에 의해 `rejected` 합니다.

##5.1 일반 값으로 해결(Resolving with normal values)
 만약 당신이 일반값으로 `then()`에 의해 반환되는 **promise Q**를 해결(resolve)하면, 그다음 `then()`을 통해 일반 값을 받을 수 있습니다.

```` javascript
    asyncFunc()
    .then(function (value1) {
        return 123;
    })
    .then(function (value2) {
        console.log(value2); // 123
    });
````

##5.2 Resolving with thenable(thenable로 해결)
또한 당신은 *thenable* **R**을 반환하는 `then()`으로 **promise Q**를 해결(resolve) 할 수 있습니다. **A** *thenable* 은 **promise** 스타일 : `then()`메서드를 가진 객체입니다. 그래서 **promises**는 *thenable* 입니다. 
**R**로 해결(resolve)하는 의미는 **Q** 이후에 삽입된다는 것을 말합니다.(예를들어 `onFulfilled`로 반환 하는 것) : **R**의 상태는 **Q**의 `onFulfilled` , `onRejected` 콜백에 전달 됩니다. 어떤 면에서는 Q가 R이 되는 겁니다.

![thenable R](http://3.bp.blogspot.com/-K9wwF9rRnJA/VDEiVbdCqDI/AAAAAAAAA4g/QkdNWpxIzEc/s1600/resolve_with_thenable.jpg)

이 메카니즘의 주요한 점은 아래 예시와 같은 중첩된 `then()` 호출을 평평하게(flatten) 해주는 것 입니다.

```` javascript
    asyncFunc1()
    .then(function (value1) {
        asyncFunc2()
        .then(function (value2) {
            ...
        });
    })
````

평평한(flat) 버전은 다음과 같습니다.

```` javascript
    asyncFunc1()
    .then(function (value1) {
        return asyncFunc2();
    })
    .then(function (value2) {
        ...
    })
````

> 이어서 2번째 챕터에서 계속 이어집니다.