> 이 문서는 http://www.2ality.com/2014/10/es6-promises-api.html 를 번역한 내용입니다.

> 또한 [[ES6] Promises(1):the API](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API) 다음 편 입니다.


#목차
1. [Promises](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#1-promises)
2. [첫번째 예제](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#2-%EC%B2%AB%EB%B2%88%EC%A7%B8-%EC%98%88%EC%A0%9C)
3. [Promises 생성과 사용](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-Promises(1):-the-API#3promises-%EC%83%9D%EC%84%B1%EA%B3%BC-%EC%82%AC%EC%9A%A9)
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
6. 에러 처리

#6. 에러 처리
 앞서 언급했듯이, 에러 처리에서 무엇을 반환하든 fulfillment 값이 될 것입니다.(rejection 값이 아닌) 이는 실패 할 경우에 사용 할 수 있는 기본값을 지정 할 수 있다는 걸 의미 합니다.

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

##6-1 예외를 잡자(Catching exceptions)
 **executor** 안에 예외(Exceptions)는 다음 에러 처리자 에게 전달 되어 집니다.

```` javascript
    new Promise(function (resolve, reject) {
        throw new Error();
    })
    .catch(function (err) {
        // Handle error here
    });
````

**then**'s 매개변수 중 하나에서 발생하는 예외는 다음과 같습니다.

```` javascript
    asyncFunc()
    .then(function (value) {
        throw new Error();
    })
    .catch(function (reason) {
        // Handle error here
    });
````

##6-2 에러's 체이닝(Chaining errors)
 아래 예제를 보면 에러처리를 제공 하지 않는 `then()`메서드를 한개 또는 여러 개를 체이닝 하여 사용 할 수 있습니다. 그러면 에러는 에러처리기가 있는 곳 까지 전달 되어집니다.

```` javascript
    asyncFunc1()
    .then(asyncFunc2)
    .then(asyncFunc3)
    .catch(function (reason) {
        // Something went wrong above
    });
````

#7.Composition
> 이번 섹션은 새로운 것을 생성하기 위해 기존 **promises**를 구성 하는 법을 설명하고자 합니다. 우리는 이미 **promises**를 구성하는 방법에 대해 본 적이 있습니다(:`then()`을 통한 순차적인 체이닝)<br>
추가적으로 `Promise.all()`과 `Promise.race()`로 **promises**를 구성하는 법을 제공합니다.

##7-1 `Promise.all()` 에 의한 `map()`
 **Promises**가 한가지 좋은 것은 promise기반 함수는 결과를 반환 해주므로, 많은 동기로 작동하는 도구(:라이브러리 tools)가 여전히 작동한다는 것 입니다. 예를 들어, 배열이 제공하는 `map()` 메서드를 다음과 같이 사용 할 수 있습니다.

```` javascript
    var fileUrls = [
        'http://example.com/file1.txt',
        'http://example.com/file2.txt'
    ];
    var promisedTexts = fileUrls.map(httpGet);
````

아래 예제 에서 `promisedTexts`는 **promises** 배열 입니다. `Promise.all()`은 **promises** 배열을 받고(*thenables*과 다른 값은 `Promise.resolve()`에 의해 **promise**로 변화됩니다.) 그리고, 모든 **promises**가 성공(fulfilled)하면, 그것들의 값의 배열로 결과가 전달됩니다.(once all of them are fulfilled, it fulfills with an array of their values:)

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

##7-2 `Promise.race()`를 통한 Timing out
`Promise.race()`는 promises 배열을 받으며, 새로운 promise **P**를 반환 합니다.(*thenables*와 다른 값은 `Promise.resolve()`를 통해 promises 로 변환 됩니다.)  만약 첫번째 Promise가 성공또는 거절로 설정되면 그 Promise를 반환되어집니다. 

아래 timeout을 확장한 `Promise.race()` 예제를 봅시다.
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

#8. 항상 비동기인 Promises
promise 라이브러리는 동기(즉시실행) 또는 비동기(현재 상황 이후에 promises실행)적인 결과를 전달 하는 것을 완벽하게 통제 할 수 있습니다.
그러나 **Promise/A+**은 후자의 경우(현재 상황이후에 promises실행)를 사용하길 요구합니다. 
`then()` 메서드를 위해 [requirement](https://promisesaplus.com/#point-34) 를 따르기를 권장 합니다. (It states so via the following requirement (2.2.4) for the then() method:)

> 실행컨텍스트 스택은 플랫폼 코드를 포함하기 전까지 `onFulfilled` 또는 `onRejected` 를 호출 할 수 없습니다.

이 의미는 여러분의 코드는 [run-to-completion](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#2-3-run-to-completion-%EC%9D%98%EB%AF%B8%ED%95%B4%EC%84%9D%ED%95%98%EA%B8%B0-%EB%AA%A8%ED%98%B8%ED%95%9C-%EA%B3%A0%EC%9C%A0-%EC%9A%A9%EC%96%B4) 에 의존 할 수 있다는 것입니다. 
and that chaining promises won’t starve other tasks of processing time
그리고 promises 체이닝은 다른  **tasks**의 프로세스 시간을 
