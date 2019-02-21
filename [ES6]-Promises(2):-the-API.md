> 이 문서는 http://www.2ality.com/2014/10/es6-promises-api.html 를 번역한 내용입니다.

> 또한 [[ES6] Promises(1):the API](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API) 다음 편 입니다.


# 목차
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
    1. [Promise.all() 을 통한 map()](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(2):-the-API#7-1-promiseall-%EC%9D%84-%ED%86%B5%ED%95%9C-map)
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

# 6. 에러 처리
 앞서 언급했듯이, 에러 처리에서 반환하는 것은 *fulfillment* 값이 될 것입니다.(*rejection* 값이 아닌) 따라서 실패시 사용하는 기본값을 지정 할 수 있습니다.

```` javascript
 retrieveFileName()
    .catch(function () {
        // Something went wrong, use a default value
        return 'Untitled.txt';
    })
    .then(function (fileName) {
        ...
    });
````

## 6-1 예외를 잡자(Catching exceptions)
 **executor**의 예외(Exceptions)는 다음 에러 핸들러(*handler*) 에게 전달 됩니다.

```` javascript
    new Promise(function (resolve, reject) {
        throw new Error();
    })
    .catch(function (err) {
        // Handle error here
    });
````

**then**'s 매개변수 중에서 발생하는 예외는 다음과 같습니다.

```` javascript
    asyncFunc()
    .then(function (value) {
        throw new Error();
    })
    .catch(function (reason) {
        // Handle error here
    });
````

## 6-2 에러's 체이닝(Chaining errors)
 아래 예제를 보면 에러 핸들러를 제공 하지 않는 `then()`메서드는 하나 이상의 호출이 있을 수 있습니다. 그런 다음 에러 핸들러가 있는 곳 까지 에러는 전달 됩니다.

```` javascript
    asyncFunc1()
    .then(asyncFunc2)
    .then(asyncFunc3)
    .catch(function (reason) {
        // Something went wrong above
    });
````

# 7.Composition
> 이번 섹션은 새로운 것을 만들기 위해 기존 **promises**를 구성(*composition*) 하는 법을 설명하고자 합니다. 우리는 이미 **promises**를 구성(*composition*)하는 방법에 대해 본 적이 있습니다(:`then()`을 통한 순차적인 체이닝)  
`Promise.all()`과 `Promise.race()`은 추가적인 **promises**를 구성(*composition*)방법을 제공합니다.

## 7-1 `Promise.all()` 을 통한 `map()`
 **Promises**가 한가지 좋은 것은 promise기반 함수는 결과를 반환 하기 때문에, 여전히 많은 동기적으로 작동하는 도구(:라이브러리 tools)가 잘 작동한다는 것 입니다. 예를 들어, 배열이 제공하는 `map()` 메서드를 다음과 같이 사용 할 수 있습니다.

```` javascript
    var fileUrls = [
        'http://example.com/file1.txt',
        'http://example.com/file2.txt'
    ];
    var promisedTexts = fileUrls.map(httpGet);
````

아래 예제 에서 `promisedTexts`는 **promises** 배열 입니다. `Promise.all()`은 **promises** 배열을 받고(*thenables*과 다른 값은 `Promise.resolve()`에 의해 **promise**로 변화됩니다.) 모든 **promises**가 성공(fulfilled)하면, 그것들의 값의 배열로 결과가 전달됩니다.

```` javascript
    Promise.all(promisedTexts)
    .then(function (texts) {
        texts.forEach(function (text) {
            console.log(text);
        });
    })
    .catch(function (reason) {
        // Receives first rejection among the promises
    });
````

## 7-2 `Promise.race()`를 통한 Timing out
`Promise.race()`는 *promises* 배열을 받으며, 새로운 **promise P**를 반환 합니다.(*thenables*와 다른 값은 `Promise.resolve()`를 통해 promises 로 변환 됩니다.)  
만약 첫번째 **Promise**가 성공 또는 거절로 확정되면 그 *Promise*가 전달되어집니다. 

아래 `Promise.race()`로 구현한 *timeout* 예제를 봅시다.

```` javascript
    Promise.race([
        httpGet('http://example.com/file.txt'),
        delay(5000).then(function () {
            throw new Error('Timed out')
        });
    ])
    .then(function (text) { ... })
    .catch(function (reason) { ... });
````

# 8. 항상 비동기인 Promises
*promise* 라이브러리는 동기(즉시실행) 또는 비동기(현재 상황 이후에 promises실행)적으로 결과를 전달 하는 것을 완벽하게 통제 할 수 있습니다.  
그러나 **Promise/A+**은 후자의 경우(현재 상황이후에 promises실행)를 사용하길 요구합니다.  
`then()` 메서드를 위해 [requirement](https://promisesaplus.com/#point-34) 를 따르기를 권장 합니다. (It states so via the following requirement (2.2.4) for the then() method:)

> 실행컨텍스트 스택은 플랫폼 코드를 포함되기 전까지 `onFulfilled` 또는 `onRejected` 를 호출 할 수 없습니다.

이 의미는 여러분의 코드는 [run-to-completion](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#2-3-run-to-completion-%EC%9D%98%EB%AF%B8%ED%95%B4%EC%84%9D%ED%95%98%EA%B8%B0-%EB%AA%A8%ED%98%B8%ED%95%9C-%EA%B3%A0%EC%9C%A0-%EC%9A%A9%EC%96%B4) 에 의존 할 수 있다는 것입니다.   
그리고 *promises* 체이닝은 다른  **tasks**의 프로세스를 처리하지 않을 것입니다.

# 9. Cheat sheet: the ECMAScript 6 promise API
 이번 섹션은 [명세](https://tc39.github.io/ecma262/#sec-promise-objects)에 설명 된대로 *ECMAScript6 promise API* 에 대한 개요를 설명합니다.

## 9.1 Glossary(어휘)
**Promise API**는 결과를 비동기적으로 제공하는 것에 관한 내용입니다. *promise* 객체는 그 객체를 통해 전달되는 결과를 전달하는 대리자입니다.

#### 상태:
- promise는 항상 3개중에 1개의 상호배타적인 상태를 가집니다.
 - 결과가 준비되기전에는 promise는 대기중(*pending*) 상태입니다.
 - 만약 결과가 준비됬으면, promise는 완료(*fulfilled*) 상태입니다.
 - 만약 에러가 발생하면, promise 는 거절(*rejected*) 상태입니다.
- 어떤 행위가 끝났다는건 promise 상태가 확정 됬다는 것입니다.(*fulfilled* 또는 *rejected*던지간에..)
- promise는 한번 확정되면, 더이상 변하지 않습니다.

#### 상태 변화 반응(*Reacting*):
- *Promise* 반응(*reactions*)은 *promise* `then()`에 등록한 콜백이며, *fulfillment* 또는 *rejection*을 통지를 받습니다.
- *thenable*은 **promise** 스타일 `then()` 메서드를 가진 객체입니다. **API**는 promise가 설정됬다는 알림을 받는 것에 관심있을 뿐 아니라 *thenables*도 요구 합니다.

#### 변경 상태: 
*promise*는 상태를 변경하기 위해 2가지 방법이 있습니다. 당신이 한번 호출 한 후에 재 호출은 아무 효과가 없습니다.

- promise가 거부(*Rejecting*)됬다는 것은 promise가 거절(*rejected*)된다는 것을 의미합니다.
- promise가 해결(*Resolving*)됬다는 것은 당신이 어떤 값을 가지고 있냐에 따라 다른 영향을 끼칩니다.
 - 일반적인 값(*thenable*이 아닌)으로 해결(*Resolving*)하면 그 값으로 충족(*fulfills*)됩니다.
 - *thenable* **T**를 가진 promise **P**를 해결(*Resovling*)하는 것은 더이상 promise **P**는 사용할수 없고, *fulfillment* 또는 *rejection* 값을 가진 **T**의 상태를 따르게 됩니다. 적절한 **P**의 반응(*reactions*)은 **T**가 해결되면 즉시 재호출 되는 것입니다.

## 9.2 생성자
 promise 생성자는 다음과 같은 시그니쳐를 따릅니다.

```` javascript
    var p = new Promise(executor(resolve, reject));
````

이는 콜백 실행자(executor)에 의해 행동이 결정되는 **promise** 를 생성합니다. 생성자 파라미터를 사용하여 `var p`를 해결(*resolve*)하거나 거절(*reject*)할 수 있습니다.:

- `x`를 통해 `p`를 해결하는 `resolve(x)`:
 - 만약 `x`가 *thenable*이라면, 그 확정(*settlement*)은 `p`로 전달 됩니다.(`then()`을 통해 등록된 트리거 반응을 포함하여)
 - 그렇지 않으면, `p`는 `x`로 처리(*fulfilled*) 됩니다.
- `reject(e)`의 변수`e`를 통해 `p`는 거절(*reject*)됩니다.(`Error` 대신에)

## 9.3 정적 메소드
> **Promise**의 모든 정적메소드는 *subclassing* 을 제공합니다.

 *subclassing*은 *receiver*를 통해 새로운 인스턴스를 생성합니다.(`new this(..)`) 또한 *subclassing*을 통해 다른 정적메소드에 접근합니다.(`this.resolve(...) VS `Promise.resolve(...))

### Promises 생성
아래 2개 메서드는 새로운 *receiver* 인스턴스를 생성합니다.(*subclassing*의 this)

- Promise.resolve(x):
 - 만약 `x`가 *thenable* 이면, *promise*로 변환됩니다.(receiver의 인스턴스)
 - 만약 `x`가 *promise* 이면, 변경되지 않은채로 반환됩니다.
 - 그렇지않으면, `x`가 처리된 새로운 *receiver* 인스턴스를 반홥니다.
- Promise.reject(reason): 어떤 값-*reason* 을 통해 거절(*rejected*)된 새로운 *promise*를 생성합니다. 

### Promises 합성
 정적메소드인 `Promise.all()`과 `Promise.race()`는 단일 *promise*를 반복 가능한 *promise's*로 구성합니다.  
그것은 즉:

- 그것들은 **iterable**를 줍니다. **iterable**의 요소들은 `this.resolve()`를 통해 *promises*로 변환됩니다.
- 그것들은 새로운 *promise*를 반환합니다. 그 *promise*는 *receiver*의 새로운 인스턴스입니다.

Method 들 :

- Promise.all(iterable): *promise* 반환
 - 만약 *iterable* 요소들이 모두 완료(*fulfilled*)되면, *promise.all*은 완료(*fulfilled*)됩니다. **Fulfillment** 값: 완료(*fulfillment*)값들의 배열
 - 만약 *iterable* 요소중 어느 하나라도 거절(*rejected*)되면 *promise.all*은 거절(*rejected*)됩니다. **Rejection** 값 : 첫번째 거절(*rejection*)값
- Promise.race(iterable): 확정(*settled*)된 *iterable* 첫번째 요소는 다음 반환되어지는 *promise*를 해결하기 위해 사용됩니다. 

## 9.4 인스턴스 프로토타입 메서드's
#### `Promise.prototype.then(onFulfilled, onRejected)`:

- *onFulfilled* 와 *onRejected* 콜백을 반응(reactions)이라고 부릅니다.
- *onFulfilled*는 promise가 이미 처리(*fulfilled*) 되었을 경우 즉시 호출되고 또는 그것이 실현되자마자 즉시 호출됩니다. 마찬가지로 *onRejected*는 거절에 관한 정보를 제공 받습니다.
- `then()`은 새로운 `promise Q`를 반환합니다.(*receiver* 생성자를 통해 생성된..):
 - 만약 반응(*reactions*)중 하나라도 값을 반환하면, *Q*는 해결(*resolved*)됩니다.
 - 만약 반응(*reactions*)중 하나라도 예외가 발생하면, *Q*는 거절(*rejected*) 됩니다.
- 생략에 의한 반응들(Omitted reactions):
 - 만약 *onFulfilled*가 생략되었다면, *receiver*의 완료(*fulfillment*)가 `then()`의 결과로 전달됩니다.
 - 만약 *onRejected*가 생략되었다면, *receiver*의 거절(rejection)이 `then()`의 결과로 전달됩니다.

*생략된 것의 반응* 의 기본 값은 다음과 같이 구현됩니다.

```` javascript
    function defaultOnFulfilled(x) {
        return x;
    }
    function defaultOnRejected(e) {
        throw e;
    }
````

#### `Promise.prototype.catch(onRejected)`:

- `then(null, onRejected)`와 동일

# 10. promises의 장단점
## 10.1 장점
### 통합 비동기 API
*promise*는 중요한 장점이 한가지 있습니다. *promise*는 점점 비동기식 브라우저 API's에 사용될 것이고, 현재 다양하고 호환되지 않은 패턴과 규칙들을 통합 할 것입니다.  
아래 2가지 promise 기반 API's를 봅시다.  

*fetch* API는 *XMLHttpRequest* 대신에 선택할 수 있는 promise 기반 API입니다. 

```` javascript
fetch(url)
.then(request=>request.text())
.then(str=>...)
````

`fetch()`는 실제 요청(*request*)에 대한 *promise*를 반환하고, `text()`는 컨텐츠에 관한 *promise*를 문자열로 반환합니다.  

모듈 프로그래밍 방식인 `Sytem.import`는 [ECMAScript 6 API](http://www.2ality.com/2014/09/es6-modules-final.html#the_ecmascript_6_module_loader_api)에 *promise* 기반입니다.

```` javascript
Sytem.import('some_module.js')
then(some_module=>{
   ....
})
````

### Promises VS events
 *events*에 비해, 일회성 결과를 다루기에는 *promise*가 더 낫습니다. *promise*가 연산되어지는 전이든 후든, 그 결과를 등록했는지 여부는 중요하지 않습니다. 언제 등록 했는지에 상관없이 당신은 결과를 얻을 수 있습니다. 이런 *promise*장점은 자연에 본질적입니다.  
반면에, 당신은 반복 이벤트를 다룰때는 사용할 수 없습니다. 체이닝은 *promise*의 또다른 장점이지만, 하나의 이벤트 처리기에 등록됩니다.

### Promise VS callbacks
*callbacks*와 비교하자면, *promise*는 보다 깨끗한 함수(또는 메서드) 시그니쳐를 가지고 있습니다.  
*callback*은 매개변수에 입력과 출력을 사용합니다. 

```` javascript
    fs.readFile(name, opts?, function (err, data))
````

*promise*의 모든 매개변수는 입력으로써 사용됩니다.:

```` javascript
    readFilePromisified(name, opts?)
        .then(dataHandler, errorHandler)
````

추가적인 *promise* 장점은 에러처리를 포함하고 있습니다.(예외를 통합) 그리고 쉽게 합성할수 있습니다.(왜냐면 `Array.prototype.map()`와 같은 비동기 툴을 재사용 할수 있기 때문입니다.)

## 10.2 단점
*Promises*는 단일 비동기 처리에 유리합니다. 하지만 다음은 적합하지 않습니다.
- 반복적인 이벤트 : 만약 당신이 관심이 있다면, *reactive programming*를 보길 바랍니다.(일반적인 이벤트 처리 체이닝을 현명하게 추가하는 방법)
- 데이터 스트림 : 현재 데이터 스트림을 제공하기 위해 [표준](https://streams.spec.whatwg.org/)을 개발중입니다.

ECMAScript6 *promise*는 때때로 유용할 수 있는 2가지 특성이 없습니다.

- 당신은 *promise*를 취소 할 수 없습니다.
- 당신은 *promise*가 얼만큼 진행 됬는지 요청 할수 없습니다.(예를들어 클라이언트 UI 에서 progress bar를 표시해주는것)

**Q promise** 라이브러리는 위 사항의 2번째 것을 [제공](https://github.com/kriskowal/q#progress-notification)하고, **Promise/A+**는 위 2가지 사항을 추가할 것을 [계획](https://github.com/promises-aplus)하고 있습니다.

# 11. Promises와 generators
`Q.spawn()`같은 유틸리티 함수의 도움으로, 당신은 *generators*통해 구현된 *shallow coroutine*내에서 promise기반 함수를 사용 할수 있습니다. 이는 코드가 동기적으로 보여지고, `try-catch`같은 동기적인 메카니즘을 수행 할 수 있는 중요한 장점을 가지고 있습니다.

```` javascript
    Q.spawn(function* () {
        try {
            let [foo, bar] = yield Promise.all([ // (A)
                httpGet('foo.json'),
                httpGet('bar.json')
            ]);
            render(foo);
            render(bar);
        } catch (e) {
            console.log('Read failed: ' + e);
        }
    });
````

`Q.spawn()` 매개변수는 *generator* 함수입니다. 만약 `yield` 연산자가 사용되었으면, 다음과 같이 발생합니다.

1. 함수의 실행은 일시정지입니다.
2. `yield` 연산자는 함수의 반환입니다. (그것은 정확히 반환은 아니고, 현재는 무시되는 상태입니다.)
3. 이후, 이 함수는 값 또는 예외로 다시 시작합니다. 전자의 경우(값인 경우) 그것이 정지된 상태에서 이후로 다시 실행되고, `yield`는 값을 반환합니다. 후자의 경우(예외인 경우)`yield` 연산자 내부에 예외를 발생시킨 것처럼, 예외는 함수 내에 발생합니다. 

따라서 `Q.spawn()`은 무엇을 해야 하는 지 분명합니다. *generator* 함수 *yield* promise가 실행되면, `spaw`은 반응(*reactions*)을 등록하고, 처리 되기를 기다립니다. 만약 *promise*가 *fulfilled*라면, *generator*는 결과와 함께 다시 재개합니다. 그리고 만약 *promise*가 *rejected*라면, 예외를 *generator* 내에 던집니다.  
새로운 구문 생성자 *async function*으로, `spawn`기능을 제공하자는 [제안](https://github.com/tc39/ecmascript-asyncawait)이 있습니다.  
*async* 함수로써 이전 예제는 다음과 같습니다. 내부적으로는, 큰 차이가 없습니다. - *async* 함수는 *generators* 기반입니다.

```` javascript
    async function () {
        try {
            let [foo, bar] = await Promise.all([
                httpGet('foo.json'),
                httpGet('bar.json')
            ]);
            render(foo);
            render(bar);
        } catch (e) {
            console.log('Read failed: ' + e);
        }
    }
````

> [3번째 챕터](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(3):-the-API)에서 이어집니다.