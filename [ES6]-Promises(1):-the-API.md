> 이 문서는 http://www.2ality.com/2014/10/es6-promises-api.html 를 번역한 내용입니다.

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


> 이번 포스트는 일반적인 **promise**를 통한 비동기 프로그래밍과 **ES6 promise API**에 일부를 소개하고자 합니다.  
2개의 비동기 프로그래밍 포스트 중 2번째이며, 충분히 이해하기 위해선 [1번째 포스트](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D)를 읽어보는 것이 좋을 것입니다.

#1. Promises
 **Promises**는 비동기 프로그래밍의 한 부분을 도와주는 패턴입니다.(함수 또는 메서드를 비동기적으로 결과를 받는 것) 이런 기능을 구현하기 위해선 promise 를 반환해야 합니다. 이러한 함수를 구현하기 위해서는 결과에 대해 위임하는 객체인 **promise**를 반환해야 합니다.  
함수 호출자는 결과가 연산 되었으면 통지 받을 **promise**와 함께 콜백을 등록합니다.

자바스크립트 **promises**의 사실상 표준은 [**Promises/A+**](https://promisesaplus.com/) 입니다. ECMAScript6 **promise** API는 이 표준을 따릅니다.

#2. 첫번째 예제
 아래의 첫 번째 예제를 보면 어떻게 **promise**를 다루는지 맛볼수 있습니다.  
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

여전히 콜백이 존재하지만, **promise**는 결과(`then()`,`catch()`)에서 호출되는 메서드를 통해 제공됩니다. (B)라인 안에 error 콜백은 2가지의 편리성을 가지고 있습니다. 첫 번째, 에러 처리의 단일 스타일입니다. 두 번째로, 당신은 `readFilePromisified()`와 (A)라인에서 콜백 오류를 핸들링 할 수 있습니다.

#3. Promises 생성과 사용
> **promise**가 생산자(Producer)와 소비자(consumer) 측면에서 어떻게 작동하는지 알아봅시다.

## 3-1. promise 생산(Producing a promise)
 생산자는 **promise**를 생성하고 결과를 전송합니다.

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
- 완료(Fulfilled) : 성공적으로 완료되었다.
- 거절(rejected) : 처리되는 동안 실패가 발생하였다.

**promise**는 연산이 끝난 뒤 완료(*fulfilled*) 혹은 거절(*rejected*) 상태로 처리(settle)됩니다. promise는 한 번 처리되면 그 처리 상태로 유지됩니다. 

![promise state](http://4.bp.blogspot.com/-iiX2B0bNZe4/VDEiVTNrpqI/AAAAAAAAA4Q/selZM4dBM7k/s1600/promise_states_simple.jpg)

`new Promise()`의 파라미터를( (A)라인 시작점 ) 실행자(**executor** 모호한 단어라 이하 영문표기로 명칭)라고 부릅니다.

- 만약 연산이 잘 되었다면, **executor**는 `resolve()` 통해 결과를 전송합니다. 보통 **promise** 완료(fulfills)를 말합니다.(*promise*가 *resolve*였지만 실제로 아닐 경우 뒤에 설명하겠습니다.)
- 만약 에러가 발생할 경우, **executor**는 `reject()`를 통해 promise-소비자(consumer)에게 통보 합니다. 즉 **promise**는 거절(reject) 상태입니다.

##3.2 promise의 사용(Consuming a promise)
**promise** 소비자(consumer)로서, 당신은 반응(*reactions*)을 통해 완료(`fulfillment)` 혹은 거절(`rejection`) 상태에 대해 'then()` 메소드에 등록한 콜백 함수로부터 통보를 받게 됩니다.

```` javascript
    promise.then(
        function (value) { /* fulfillment */ },
        function (reason) { /* rejection */ }
    );
````

**promise**가 비동기 함수(일회성 결과)에 유용한 점은, **promise**상태가 한 번 확정되면 더이상 변하지 않게 된다는 점이다.  
게다가 **promise**가 확정(*settled*)되기 전이나 후에 당신이 `then()`을 호출 했는지는 중요하지 않기 때문에 어떤 경쟁상태(*race condition*)도 존재하지 않습니다.

- 전자의 경우, **promise** 상태가 확정(*settled*)되는대로 바로 적절한 반응(*reaction*)이 호출됩니다.
- 후자의 경우, **promise** 결과(**fulfillment** 또는 **rejection** 값)가 캐시 되어, 적절하게 원하는 타이밍에 즉시 `then()` 다룰수 있게 해줍니다.(task로 큐에 저장) 

##3.3 성공 또는 거절만 처리(Only handling fulfillments or rejections)
 만약 당신이 성공에만 관심 있다면, `then()`의 2번째 파라미터를 생략할 수 있습니다.

```` javascript
    promise.then(
        function (value) { /* fulfillment */ }
    );
````

만약 당신이 거절(reject)에만 관심 있다면, 1번째 파라미터를 생략할 수 있습니다. `catch()`메서드는 같은 작동을 하게 해주는 더 간단한 방법입니다. 

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

성공(*fulfillments*)을 위해서는 `then()`을 사용하고 에러는 `catch()`를 사용하는 것을 추천합니다. 왜냐하면 `catch`는 콜백에 멋진 라벨을 지정하고 또 동시에 여러 **promise**의 거절(*rejections*)을 처리할 수 있습니다. (어떻게 하는지 나중에 설명)

#4. 예제(Examples)
> 몇 가지 예제를 통하여 이러한 기본 구성요소를 사용해봅시다.

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

(A)라인에서 파라미터 없이 `resolve`를 호출합니다.(`resolve(undefined)`를 호출하는 것과 동일). (B)라인에 성공(*fulfillment*) 결과 값은 필요 없습니다. 그냥 통보 받는것 만으로도 충분합니다.

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

시간 경과 이후 거절((A)라인) 요청을 취소하진 않지만, 그 결과로 *promise*가 수행되지 않도록 방지할 뿐입니다.

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

즉 *Q*의 `then()`을 호출하여 **promise**기반 흐름을 제어할 수 있게 유지 한다는 것입니다.

- **Q**는 `onFulfilled` 또는 `onRejected` 중 하나에 의해 반환된 것으로 해결(`resolved`) 합니다.
- **Q**는 `onFulfilled` 또는 예외를 던진 `onRejected`중 하나에 의해 거절(`rejected`) 합니다.

##5.1 일반 값으로 해결(Resolving with normal values)
 만약 `then()`에 의해 반환된 **promise Q**의 값을 정상적인 값으로 해결(resolve)하면, 그 다음 `then()`을 통해 해당 값을 받을 수 있습니다.

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
또한 당신은 `then()`에 의해 반환되 **promise Q**를  *thenable* **R**로 해결(resolve) 할 수 있습니다. **A** *thenable* 은 **promise** 스타일 : `then()`메서드를 가진 객체입니다. 그래서 **promises**는 *thenable* 입니다.  
**R**을 이용하여 해결(resolve)하는 것은 **Q** 후에 삽입 된다는 것을 의미합니다.(예를들어 `onFulfilled`에서 반환) : **R**의 상태는 **Q**의 `onFulfilled` , `onRejected` 콜백에 전달 됩니다. 어떤 면에서는 **Q**는 **R**이 됩니다.

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

> 이어서 [2번째 챕터](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API)에서 계속 이어집니다.