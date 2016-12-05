> 이 문서는 http://exploringjs.com/es6/ch_async.html 를 번역한 내용입니다.

#목차
1. [자바스크립트 호출 스택(call stack)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#1-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%ED%98%B8%EC%B6%9C-%EC%8A%A4%ED%83%9Dcall-stack)
2. [브라우저 이벤트 루프(event loop)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#2-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84event-loop)
 1. [타이머(Timers)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#2-1-%ED%83%80%EC%9D%B4%EB%A8%B8timers)
 2. [DOM 변경 표시(Displaying DOM changes)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#2-2-dom-%EB%B3%80%EA%B2%BD-%ED%91%9C%EC%8B%9C-displaying-dom-changes)
 3. [ Run-to-completion 의미(고유 용어로써 영어사용)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#2-3-run-to-completion-%EC%9D%98%EB%AF%B8%EA%B3%A0%EC%9C%A0-%EC%9A%A9%EC%96%B4%EB%A1%9C%EC%8D%A8-%EC%98%81%EC%96%B4%EC%82%AC%EC%9A%A9)
 4. [이벤트 루프 차단(Blocking the event loop)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#2-4-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84-%EC%B0%A8%EB%8B%A8blocking-the-event-loop)
 5. [블로킹 피하기(Avoiding blocking)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#2-5-%EB%B8%94%EB%A1%9C%ED%82%B9-%ED%94%BC%ED%95%98%EA%B8%B0avoiding-blocking)
3. [비동기적 결과 수신(Receiving results asynchronously)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#3-%EB%B9%84%EB%8F%99%EA%B8%B0%EC%A0%81-%EA%B2%B0%EA%B3%BC-%EC%88%98%EC%8B%A0receiving-results-asynchronously)
 1. [events를 통한 비동기 결과(Asynchronous results via events)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#3-1-events%EB%A5%BC-%ED%86%B5%ED%95%9C-%EB%B9%84%EB%8F%99%EA%B8%B0-%EA%B2%B0%EA%B3%BCasynchronous-results-via-events)
  1. [암시적 요청(Implicit requests)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#3-1-1-%EC%95%94%EC%8B%9C%EC%A0%81-%EC%9A%94%EC%B2%ADimplicit-requests)
  2. [events는 하나의 결과는 잘 작동하지 않습니다. (Events don’t work well for single results)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#3-1-2-events%EB%8A%94-%ED%95%98%EB%82%98%EC%9D%98-%EA%B2%B0%EA%B3%BC%EB%8A%94-%EC%9E%98-%EC%9E%91%EB%8F%99%ED%95%98%EC%A7%80-%EC%95%8A%EC%8A%B5%EB%8B%88%EB%8B%A4-events-dont-work-well-for-single-results)
 2. [callbacks를 통한 비동기 결과(Asynchronous results via callbacks)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#3-2-callbacks%EB%A5%BC-%ED%86%B5%ED%95%9C-%EB%B9%84%EB%8F%99%EA%B8%B0-%EA%B2%B0%EA%B3%BCasynchronous-results-via-callbacks)
 3. [Continuation-passing style(역자주: 고유명사 보통 CPS라고 불림)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#3-4-cps-%EC%95%88%EC%97%90-%EC%BD%94%EB%93%9C-%EC%9E%91%EC%84%B1composing-code-in-cps)
 4. [CPS 안에 코드 작성(Composing code in CPS)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#3-4-cps-%EC%95%88%EC%97%90-%EC%BD%94%EB%93%9C-%EC%9E%91%EC%84%B1composing-code-in-cps)
 5. [콜백의 장단점(Pros and cons of callbacks)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#3-5-%EC%BD%9C%EB%B0%B1%EC%9D%98-%EC%9E%A5%EB%8B%A8%EC%A0%90pros-and-cons-of-callbacks)
4. [다음에 할 내용(Looking ahead)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#4-%EB%8B%A4%EC%9D%8C%EC%97%90-%ED%95%A0-%EB%82%B4%EC%9A%A9looking-ahead)
5. [추가로 읽을 거리(Further reading)](https://github.com/FEDevelopers/tech.description/wiki/%5BES6%5D-%EB%B9%84%EB%8F%99%EA%B8%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D#5-%EC%B6%94%EA%B0%80%EB%A1%9C-%EC%9D%BD%EC%9D%84-%EA%B1%B0%EB%A6%ACfurther-reading)

#Asynchronouse programming (Background)
 이 챕터는 자바스크립트의 비동기 프로그래밍(asynchronous programming) 기본에 대한 이야기입니다. 또한 이번 챕터는 **ES6 Promise**을 위한 기본 배경 지식이기도 합니다.

## 1. 자바스크립트 호출 스택(**call stack**)
 `function f` 가 `function g`를 호출할 때 `function g`는 끝난 후에 어디로 돌아가야 하는지 알아야합니다.(`function f` 안에서)  
일반적으로 이런 정보는 호출스택(call stack)인 **스택(*stack*)**이 관리를 합니다.  
아래 예제를 봅시다.

```` javascript
function h(z) {
    // Print stack trace
    console.log(new Error().stack); // (A)
}
function g(y) {
    h(y + 1); // (B)
}
function f(x) {
    g(x + 1); // (C)
}
f(3); // (D)
return; // (E)
````

처음에는 위 프로그램이 시작될 때, **호출 스택**(the call stack)은 비어있습니다. (D) 라인에 있는 함수 `f(3)`이 호출되면 스택은 한가지 항목을 가지게 됩니다.

* 전역 스코프 (Location in global scope)

(C) 라인에 있는 함수 `g(x+1)`이 호출 된 후에는 1개가 추가됩니다.

* 함수 `f` (Location in `f `)
* 전역 스코프 (Location in global scope)

(B) 라인에 있는 함수 `h(y+1)` 이 호출된 후에도 1개가 추가됩니다.

* 함수 `g` (Location in g)
* 함수 `f` (Location in `f `)
* 전역 스코프 (Location in global scope)

마지막 (A)라인이 실행되면 **stack trace** 는 아래와 같이 어떤 stack이 쌓여서 호출돼있는지 확인할 수 있습니다.

```` javascript
Error
    at h (stack_trace.js:2:17)
    at g (stack_trace.js:6:5)
    at f (stack_trace.js:9:5)
    at <global> (stack_trace.js:11:1)
````

다음으로, 각 함수는 종료되고, 스택의 최상위 요소가 제거 될때마다 함수가 종료됩니다. 마지막 함수 `f`가 끝나면, `전역 스코프(global scope)`로 복귀하여 **호출 스택**은 비어 있습니다. 결국 (E)라인이 오면 반환되어 **스택**은 비워지고 프로그램은 종료됨을 의미합니다.

## 2. 브라우저 이벤트 루프(event loop)
 간단히 말해서, 각 브라우저 탭은 **싱글 프로세스**(single process : [the event loop](https://html.spec.whatwg.org/multipage/webappapis.html#event-loop))에서 실행됩니다.  
이 loop(*event loop*)는 task queue(고유명사로써 영어를 사용한다.)를 통해 공급되는 브라우저 관련 작업(*task*)을 실행합니다.  
**task**의 예는 다음과 같습니다. 

* task의 예
 1. HTML 파싱
 2. 자바스크립트 코드 실행
 3. 사용자 input 감지 후 반응(마우스 클릭, key 입력 등등)
 4. 비동기 네트워크 요청 수행 결과

위 2-4 항목은 브라우저에 내장된 엔진을 통해 자바스크립트 코드를 실행하는 작업입니다. 그것들은 코드가 종료될 때 종료됩니다. 그런 다음 큐에서 다음 *task*를 가져와 실행합니다. 다음 그림은 이러한 모든 메커니즘이 어떻게 연결되는지 방법을 제공합니다.

![task 메커니즘](http://exploringjs.com/es6/images/async----event_loop.jpg)

**event loop**는 병렬로 함께 실행되는 다른 프로세스(timers, input handling, etc)로 둘러싸여 있습니다. 이 프로세스는 각각의 *task*를 **task queue**에 추가하여 프로세스와 통신합니다.

### 2-1 타이머(Timers)
 > 브라우저는 [timers](https://html.spec.whatwg.org/multipage/webappapis.html#timers)를 가지고 있으며 타이머는 `setTimeout()`을 제공합니다. 그리고 `setTimeout()`이 발생 될 때까지 기다렸다가, 큐에 **task**를 추가합니다.

```` javascript
setTimeout(callback, ms);
````

`setTimeout()`은 **ms**(milliseconds) 경과 후 `callback`이 **task queue** 에 추가됩니다. 타이머에 `callback`은 **ms**(milliseconds) 이후 실제 실행되는 게 아니라 **task queue**에 추가되는것이 중요하다는 걸 기억해야 합니다. 왜냐하면 
만약 **event loop**가 차단 당하면 지정된 시간 이후보다 나중에 setTimeout() `callback`이 실행된다는 걸 확인할 수 있기 때문입니다.  

보통 `setTimeout()` **ms**를 ‘0’ 으로 세팅하는건 **task queue** 에 바로 추가하기 위한 일반적인 방법입니다. 그러나 몇몇 브라우저는 실제로 ‘0’ ms 이후 추가되지 않고 브라우저마다 ‘0’이 아닌 최소 ms가 세팅 되어져 있습니다.(ex : firefox는 4ms임)

### 2-2 DOM 변경 표시 (Displaying DOM changes)
 대부분 DOM 변경(특히 layout 재갱신이 포함된 경우)의 경우 화면은 바로 업데이트되지 않습니다. `“레이아웃 새로고침은 16ms 정도 시간이 흐른다.”`[@bz_moz](https://twitter.com/bz_moz/status/513777809287028736) 그리고 **event loop**를 통해 실행 할 기회가 주어집니다.  

`requestAnaimationFrame()`을 [문서](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame)에서 확인해 보면 브라우저가 DOM을 업데이트 빈번하게 업데이트할 경우 레이아웃 흐름에 방해되지 않게 조정할 수가 있다고 합니다.

### 2-3 Run-to-completion 의미(고유 용어로써 영어사용)
 자바스크립트는 소위 “run-to-completion” 라는 걸 가지고 있습니다. 항상 현재 **task**는 다음 **task** 가 실행되기 전에 끝나게 됩니다. 즉, 각 작업이 현재 모든 상태를 완벽하게 제어 할 수 있다는 걸 의미하며, 동시적으로 간섭이 되는 걸 걱정할 필요가 없습니다.  
아래 예제를 확인해봅시다.

```` javascript
setTimeout(function () { // (A)
    console.log('Second');
}, 0);
console.log('First'); // (B)
````

함수 (A) 라인이 시작되면 즉시 **task queue**에 추가 되지만, (B)라인 코드 조각 작업이 끝난 후에 실행되게 됩니다.  
즉 이 코드의 출력은 항상 다음과 같습니다.

````
First
Second
````

### 2-4 이벤트 루프 차단(Blocking the event loop)
 우리가 봤듯이 일부 탭(일부 브라우저에서, 전체 브라우저)은 싱글 프로세스로 운영됩니다. - 사용자 인터페이스, 모든 다른 연산작업들 포함하여.  
즉 해당 프로세스 안에서 엄청 긴 연산작업을 수행하게 하여 사용자 인터페이스를 멈추게 할 수 있다는 걸 의미합니다.  
아래 데모 코드를 확인해봅시다.

```` javascript
<a id="block" href="">Block for 5 seconds</a>
<p>
<button>This is a button</button>
<div id="statusMessage"></div>
<script>
    document.getElementById('block')
    .addEventListener('click', onClick);

    function onClick(event) {
        event.preventDefault();

        setStatusMessage('Blocking...');

        // Call setTimeout(), so that browser has time to display
        // status message
        setTimeout(function () {
            sleep(5000);
            setStatusMessage('Done');
        }, 0);
    }
    function setStatusMessage(msg) {
        document.getElementById('statusMessage').textContent = msg;
    }
    function sleep(milliseconds) {
        var start = Date.now();
        while ((Date.now() - start) < milliseconds);
    }
</script>
````
> 온라인에서 위 코드를 확인해보아요 [링크](http://rauschma.github.io/async-examples/blocking.html)

코드 안에 `Block for 5 seconds`링크를 클릭하면 함수 `onClick()`이 트리거 되고, 5초 동안 **event loop**를 차단 하기 위하여 동기함수인 `sleep()`을 사용합니다. 이 시간 동안 사용자 인터페이스는 작동이 멈추게 됩니다. 예를 들어 당신은 “Simple button”을 클릭할 수 없을것 입니다.

### 2-5 블로킹 피하기(Avoiding blocking)
> 다음 두 가지 방법으로 **event loop**를 차단하지 마십시오.

 첫 번째 당신은 메인 프로세스 안에서 엄청 긴 연산작업을 수행하면 안됩니다. 다른 프로세스로 옮겨야 합니다. **Worker API** 를 통해 수행할 수 있습니다.  
 두 번째 당신은 긴 연산 작업 결과를(동기적으로) 기다려서는 안 됩니다.(네트워크요청, 작업자 프로세스 안에 자신의 알고리즘 등)  **event loop**에서 수행시키고, 그 작업이 완료되면 통보를 하게 합니다. 사실 당신은 브라우저에서 이 작업을 하는 거에 대해 선택 여지가 없습니다. 예를 들어 내장된 동기 `sleep`(이전에 `sleep()`을 구현한 것 같은)이 없습니다. 대신에 `setTimeout()`으로 비동기적인 `sleep`을 수행할 수 있습니다.  

다음 섹션에서는 비동기 적으로 결과를 기다리는 기술에 대해 설명합니다.

## 3. 비동기적 결과 수신(Receiving results asynchronously)
> 비동기적 결과를 수신하기 위한 2가지 패턴 : **events** 와 **callback**

### 3-1 events를 통한 비동기 결과(Asynchronous results via events)
 결과를 비동기적으로 수신받기 위해서는, 각 요청 별 객체를 생성하고, 생성 객체와 함께 **이벤트 핸들러**(events handlers)를 등록합니다.(:하나는 성공적인 계산을위한 것이고 다른 하나는 오류를 처리하는 것입니다.)  
아래 코드는 `XMLHttpRequest` API가 어떻게 작동하는지 보여주는 코드입니다.

```` javascript
var req = new XMLHttpRequest();
req.open('GET', url);

req.onload = function () {
    if (req.status == 200) {
        processData(req.response);
    } else {
        console.log('ERROR', req.statusText);
    }
};

req.onerror = function () {
    console.log('Network Error');
};

req.send(); // Add request to task queue
````

마지막 라인은 실제로 요청을 수행하지 않고, **task queue** 에 추가됩니다. 따라서, `onload` , `onerror` 가 세팅 되기 전에, `open()` 메서드를 후에 수행 할 수 있습니다. 자바스크립트 **run-to-completion** 때문에 동일하게 작동합니다.

### 3-1-1 암시적 요청(Implicit requests)
 브라우저 API인 **IndexedDB**는 약간 특이한 스타일인 이벤트 처리(event handling)를 가지고 있습니다.

```` javascript
var openRequest = indexedDB.open('test', 1);

openRequest.onsuccess = function (event) {
    console.log('Success!');
    var db = event.target.result;
};

openRequest.onerror = function (error) {
    console.log(error);
};
````

첫 번째로 요청 객체를 만들고, 결과를 통지받기 위한 이벤트리스너를 등록합니다. 그러나 `open()`을 사용하여 요청을 명시적으로 **queue**에 추가 할 필요가 없습니다. 그것은 현재 **task**가 완료된 후에 실행됩니다. 그래서 `open()`을 호출 한 후 이벤트핸들러를 등록 할 수 있습니다.  

만약 당신이 **멀티스레드**(multi-thread)프로그래밍에 익숙하다면, 아마도 이 핸들링 요청 스타일은 낯설 것입니다. 그것은 마치 **race condition**(경쟁상태)와 같은 것처럼 보일 것입니다. 그러나 **run-to-completion** 때문에 항상 안전할 것입니다.

### 3-1-2 events는 하나의 결과는 잘 작동하지 않습니다. (Events don’t work well for single results)
 만약 여러 번 결과를 수신받을 경우, 비동기 연산 결과 처리하는 것(handling)은 OK입니다. 그러나 단일 결과는 다음의 다변적인 문제를 일으킵니다. 그 사례로 콜백은 매우 인기가 많아지게 되었습니다.

### 3-2 callbacks를 통한 비동기 결과(Asynchronous results via callbacks)
 만약 당신이 비동기 결과를 콜백을 통하여 처리 한다면, 비동기 함수 또는 메서드 호출에 후행 매개변수로 콜백 함수를 전달 합니다.  

아래 **Node.js** 예제를 봅시다. 우리는 `fs.readFile()`을 비동기로 호출하여 text 파일 내용을 읽습니다. 

```` javascript
// Node.js
fs.readFile('myfile.txt', { encoding: 'utf8' },
    function (error, text) { // (A)
        if (error) {
            // ...
        }
        console.log(text);
    });
````

만약 `readFile()`이 성공하면 A라인 안에 **text** 파라미터를 통하여 결과를 수신받을 수 있습니다. 만약 성공하지 못하면 첫 번째 파라미터로 error를 받게 됩니다.

아래는 전통적인 함수형 프로그래밍 스타일로 만든 위 예제와 같은 코드입니다.

```` javascript
// Functional
readFileFunctional('myfile.txt', { encoding: 'utf8' },
    function (text) { // success
        console.log(text);
    },
    function (error) { // failure
        // ...
    });
````

### 3-3 Continuation-passing style(역자주: 고유명사 보통 CPS라고 불림)
다음 단계 (연속)가 명시 적으로 매개 변수로 전달되기 때문에 콜백 사용 프로그래밍 스타일 (특히 앞에서 설명한 기능적 방식)은 *Continuation-passing-style*(CPS)라고 부릅니다.  
이렇게하면 호출 된 함수가 다음에 발생할 일을보다 잘 제어 할 수 있습니다.  
아래 *CPS*를 표현한 코드를 봅시다.

```` javascript
console.log('A');
identity('B', function step2(result2) {
    console.log(result2);
    identity('C', function step3(result3) {
       console.log(result3);
    });
    console.log('D');
});
console.log('E');

// Output: A E B D C

function identity(input, callback) {
    setTimeout(function () {
        callback(input);
    }, 0);
}
````

아래 각 절차는 콜백 안에서 프로그램이 진행하는 제어 흐름입니다. 이 중첩된 함수는 콜백지옥(callback hell)이라고 불리 웁니다. 그러나 당신은 중첩을 피할 수 있습니다. 왜냐하면 자바스크립트 함수는 호이스팅(hoisted:스코프 최상단으로 위치시키는 것을 의미함)하기 때문입니다. 이 말은 당신이 먼저 호출하고, 함수 정의는 나중에 한다는 걸 의미합니다. 아래 호이스팅을 사용한 예제를 확인해봅시다.

```` javascript
console.log('A');
identity('B', step2);
function step2(result2) {
    // The program continues here
    console.log(result2);
    identity('C', step3);
    console.log('D');
}
function step3(result3) {
   console.log(result3);
}
console.log('E');
````

[More information on CPS is given in [3]](http://www.2ality.com/2012/06/continuation-passing-style.html)

### 3-4 CPS 안에 코드 작성(Composing code in CPS)
 일반적인 자바스크립트 스타일은 코드 조각 들의 조합입니다.

- 자바스크립트의 일반적인 코드가 사실은 '조각들의 순차적인 조합'이라는 것은 말할 필요 없이 명백하다. 하지만 다시 한 번 짚고 넘어가도 나쁠 건 없다.
- `map()`, `filter()`,`forEach()`와 같은 배열 메소드  
- `for, while`과 같은 루프

[Async.js](https://github.com/caolan/async) 라이브러리는 **Node.js** 콜백스타일과 함께 **CPS** 비슷한 것을 할 수 있게 제공합니다.   
그것은 아래 예제 코드와 같습니다. 3개의 파일의 컨텐츠를 불러오고 배열에 이름을 저장하는 예제 입니다.

```` javascript
var async = require('async');

var fileNames = [ 'foo.txt', 'bar.txt', 'baz.txt' ];
async.map(fileNames,
    function (fileName, callback) {
        fs.readFile(fileName, { encoding: 'utf8' }, callback);
    },
    // Process the result
    function (error, textArray) {
        if (error) {
            console.log(error);
            return;
        }
        console.log('TEXTS:\n' + textArray.join('\n----\n'));
    });
````

### 3-5 콜백의 장단점(Pros and cons of callbacks)
콜백을 사용하면 근본적으로 다른 프로그래밍 스타일 인 CPS가 발생합니다. CPS는 주요한 이점은 기본적인 메커니즘이 이해하기 쉽다는 것 입니다. 그러나 단점도 있습니다.

- 에러 처리가 더 복잡해졌다: 에러를 처리하는 방법은 두 가지가 있다. 콜백 함수를 이용하는 방법과 예외를 이용하는 것 2가지가 있다. 그러므로 이 두 가지를 동시에 사용할 경우 조심해야 한다.
- 코드 직관성이 떨어진다: 동기 함수에서는 입력(매개변수)과 출력(함수 결과) 사이에 구분이 명확하다. 콜백을 사용하는 비동기 함수에서는 이 두 개가 섞여 있다. 함수의 매개변수 중 몇 개는 입력을 위해, 나머지는 출력을 위해 사용된다.
- 구성이 더 복잡해졌다: 출력을 위한 개념이 함수의 매개변수에 존재하기 때문에 구성이 더 복잡해졌다.

**Node.js** 콜백스타일은 3가지 단점을 가지고 있습니다.(함수 스타일과 비교)

- 에러를 처리하기 위한 if문이 많다.
- 에러 핸들러를 재사용하기 힘들다.
- 또한, 기본 에러 핸들러를 제공하는 게 더 어렵다. 기본 에러 핸들러는 당신이 함수를 호출 할 때 자신만의 핸들러를 작성하지 않을 때 유용합니다. 또한 만약 호출자가 특정한 핸들러를 지정하지 않는다면 함수에 의해 사용될 수도 있다.

##4. 다음에 할 내용(Looking ahead)
 다음 챕터는 **Promise**와 **ES6 Promise** API을 다룹니다. **Promise**는 콜백보다 더 복잡합니다. 중요한 이점을 제공함으로써 콜백의 단점을 제거합니다.

##5. 추가로 읽을 거리(Further reading)
- [1] “[Help i’m stuck in an event-loop](http://vimeo.com/96425312)” by Philip Roberts (video).
- [2] “[Event loops](https://html.spec.whatwg.org/multipage/webappapis.html#event-loops)” in the HTML Specification
- [3] “[Asynchronous programming and continuation-passing style in JavaScript](http://www.2ality.com/2012/06/continuation-passing-style.html)” by Axel Rauschmayer.