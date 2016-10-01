> 이 문서는 http://exploringjs.com/es6/ch_async.html 를 번역한 내용입니다.

1. [자바스크립트 호출 스택(call stack)](https://github.com/FEDevelopers/tech.description/wiki/_new#1-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%ED%98%B8%EC%B6%9C-%EC%8A%A4%ED%83%9Dcall-stack)
2. [브라우저 이벤트 루프(event loop)](https://github.com/FEDevelopers/tech.description/wiki/_new#2-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84event-loop)
 1. [타이머(Timers)](https://github.com/FEDevelopers/tech.description/wiki/_new#2-1-%ED%83%80%EC%9D%B4%EB%A8%B8timers)
 2. [DOM 변경 표시(Displaying DOM changes)](https://github.com/FEDevelopers/tech.description/wiki/_new#2-2-dom-%EB%B3%80%EA%B2%BD-%ED%91%9C%EC%8B%9C-displaying-dom-changes)
 3. [Run-to-completion 의미(해석하기 애매한 고유 용어)](https://github.com/FEDevelopers/tech.description/wiki/_new#2-3-run-to-completion-%EC%9D%98%EB%AF%B8%ED%95%B4%EC%84%9D%ED%95%98%EA%B8%B0-%EC%95%A0%EB%A7%A4%ED%95%9C-%EA%B3%A0%EC%9C%A0-%EC%9A%A9%EC%96%B4)
 4. [이벤트 루프 블락(Blocking the event loop)](https://github.com/FEDevelopers/tech.description/wiki/_new#2-4-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84-%EB%B8%94%EB%9D%BDblocking-the-event-loop)
 5. [블럭킹 피하기(Avoiding blocking)](https://github.com/FEDevelopers/tech.description/wiki/_new#2-5-%EB%B8%94%EB%9F%AD%ED%82%B9-%ED%94%BC%ED%95%98%EA%B8%B0avoiding-blocking)
3. [비동기적 결과 수신(Receiving results asynchronously)](https://github.com/FEDevelopers/tech.description/wiki/_new#3-%EB%B9%84%EB%8F%99%EA%B8%B0%EC%A0%81-%EA%B2%B0%EA%B3%BC-%EC%88%98%EC%8B%A0receiving-results-asynchronously)


#Asynchronouse programming (Background)
 이 챕터는 자바스크립트의 비동기 프로그래밍(asynchronous programming) 기본에 대한 이야기입니다. 또한 이번 챕터는 **ES6 Promise**을 위한 기본 배경 지식이기도 합니다.

## 1. 자바스크립트 호출 스택(**call stack**)
 `function f` 가 `function g`를 호출 할 때 `function g`는 자신이 실행 후에 어디에 `return` 되야 하는지 알 필요가 있습니다.(`function f` 안에 / inside f() ) 보통 이런 정보는 **stack**(the call stack) 이 관리를 합니다.
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

최초 위 프로그램이 시작 될 때, **호출 스택**(the call stack)은 비어있습니다. (D) 라인에 있는 함수 `f(3)`이 호출 되면 스택은 한가지 항목을 가지게 됩니다.

* 전역 스코프 (Location in global scope)

(C) 라인에 있는 함수 `g(x+1)`이 호출 된 후에는 1개가 추가가 됩니다.

* 함수 `f` (Location in `f `)
* 전역 스코프 (Location in global scope)

(B) 라인에 있는 함수 `h(y+1)` 이 호출 된 후에도 1개가 추가가 됩니다.

* 함수 `g` (Location in g)
* 함수 `f` (Location in `f `)
* 전역 스코프 (Location in global scope)

마지막 (A)라인이 실행되면 **stack trace** 는 아래와 같이 어떤 stack이 쌓여서 호출되있는지 확인 할 수 있습니다.

```` javascript
Error
    at h (stack_trace.js:2:17)
    at g (stack_trace.js:6:5)
    at f (stack_trace.js:9:5)
    at <global> (stack_trace.js:11:1)
````

각 함수 실행은 종료되고 상위 함수 부터 stack에서 제거 됩니다. 마지막 함수 `f`가 끝나면, `전역 스코프(global scope)`로 컴백하여 **호출 스택**은 비워집니다. 결국 (E)라인이 오면 `return`되어 **stack**은 비워지고 프로그램은 종료됩니다.

## 2. 브라우저 이벤트 루프(event loop)
 각 브라우저 탭은 **싱글 프로세스**(single process : [the event loop](https://html.spec.whatwg.org/multipage/webappapis.html#event-loop))로 돌아갑니다. 
이 루프(이하 **event loop**로 명칭한다.)는 브라우저와 관련된 작업들(**tasks**라고 부른다.)을 실행 시킵니다. 앞에 브라우저와 관련된 작업들은 **task queue**(고유명사는 영어로 명칭)가 공급해줍니다.

* taks의 예
 1. HTML 파싱
 2. 자바스크립트 코드 실행
 3. 사용자 input 감지 후 반응(마우스클릭, key입력 등등)
 4. 비동기 네트워크 요청 수행 결과

위 2,3,4 항목은 브라우저에 내장된 엔진을 통해 자바스크립트 코드가 실행되는 작업입니다. 그것들은 코드가 종료 될 때 제거 된다. 그러면 큐에서 다음 작업(task)을 가져와 다음 작업을 실행 합니다. 다음 그림은 이러한 모든 메커니즘들이 어떻게 연결되는지 방법을 제공합니다.


![task 메커니즘](http://exploringjs.com/es6/images/async----event_loop.jpg)


**event loop**는 병렬로 실행되는(타이머, input handling 등등) 다른 프로세스에 의해 둘러싸여 있습니다. 이러한 프로세스는 큐에 작업(task)이 추가됨으로써 연결됩니다.(The event loop is surrounded by other processes running in parallel to it (timers, input handling, etc.). These processes communicate with it by adding tasks to its queue.)

### 2-1 타이머(Timers)
 > 브라우저는 [timers](https://html.spec.whatwg.org/multipage/webappapis.html#timers)를 가지고 있으며  타이머는 `setTimeout()`을 제공합니다. 그리고 `setTimeout()`이 발생 될 때까지 기다렸다가, 큐에 **task**를 등록합니다.

```` javascript
setTimeout(callback, ms);
````

`setTimeout()`은 **ms**(milliseconds) 경과 후 `callback`이 **taks queue** 에 추가됩니다. 타이머에 `callback`은 **ms**(milliseconds) 이후 실제 실행되는게 아니라 **task queue**에 등록이 된다는 것이 중요하다는걸 기억해야 합니다. 왜냐하면 
만약 **event loop**가 블락 당하면 지정된 시간 이후 보다 나중에 setTimeout() `callback`이 실행된다는 걸 확인 할 수 있기 때문입니다.

보통 `setTimeout()` **ms**를 ‘0’ 으로 세팅하면 **task queue** 에 바로 추가한다는 걸 의도합니다. 그러나 몇몇 브라우저는 실제로 ‘0’ ms 이후 추가되지 않고 브라우저마다 ‘0’이 아닌 최소 ms가 세팅 되어져 있습니다.(ex : firefox는 4ms임)

### 2-2 DOM 변경 표시 (Displaying DOM changes)
 대부분 DOM 변경이 발생할 경우(특히 layout 재갱신이 포함된 경우) 화면은 바로 갱신되지 않습니다. “레이아웃 새로고침은 16ms 정도 시간이 흐른다.”[@bz_moz](https://twitter.com/bz_moz/status/513777809287028736) 그리고 **event loop**를 통해 발생할 기회가 주어집니다.

`requestAnaimationFrame()`을 [문서](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame)에서 확인해 보면 브라우저가 DOM을 업데이트 빈번하게 업데이트 할 경우 레이아웃 흐름에 방해 되지 않게 조정 할 수 가 있다고 합니다.

### 2-3 Run-to-completion 의미(해석하기 애매한 고유 용어)
 자바스크립트는 소위 “run-to-completion” 라는 걸 가지고 있습니다. 항상 현재 **task**는 다음 **task** 가 실행되기 전에 끝나게 됩니다. 즉 각 작업이 현재 모든 상태를 완벽하게 컨트롤 할 수 있다는걸 의미하며, 동시적으로 간섭이 되는걸 걱정할 필요가 없습니다.
아래 예제를 확인해봅시다.

```` javascript
setTimeout(function () { // (A)
    console.log('Second');
}, 0);
console.log('First'); // (B)
````

함수 (A) 라인이 시작되면 즉시 **task queue**에 추가가 됩니다. 그렇지만 (B)라인 코드 조각 작업이 끝난 후에 실행되게 됩니다. 다음 output이 나오는걸 확인할 수 있습니다.

````
First
Second
````

### 2-4 이벤트 루프 블락(Blocking the event loop)
 우리가 봤듯이 각 탭(일부 브라우저 에서, 전체 브라우저)은 싱글 프로세스로 운영됩니다. - 사용자 인터페이스, 모든 다른 연산작업들 포함하여.
이것은 프로세스안에 엄청 긴 연산작업이 수행되게 하여 사용자 인터페이스를 멈추게 할 수 있다는걸 의미합니다. 
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

코드안에 `Block for 5 seconds`링크를 클릭 하면 함수 `onClick()` 가 트리거 됩니다. 그것은 5초동안 **event loop**를 블락 하기 위하여 동기함수인 `sleep()` 사용합니다. 이 시간동안 사용자 인터인터페이스는 작동이 멈추게됩니다. 예를 들어 당신은 “Simple button”을 클릭 할 수 없을 것 입니다.

### 2-5 블럭킹 피하기(Avoiding blocking)
>  **event loop**가 블럭킹을 피하기 위한 2가지 방법이 있습니다.

 첫번째 당신은 메인 프로세스안에서 엄청 긴 연산작업을 수행 하면 안됩니다. 그것을 다른 프로세스로 옮겨야 합니다. **Worker API** 를 통해 그것을 수행 할 수 있습니다.
 두번째 당신은 긴 연산 작업 결과를(동기적으로) 기다려서는 안됩니다.(네트워크요청, 작업자 프로세스 안에 자신의 알고리즘 등)  **event loop**에서 수행시키고, 그 작업이 완료 되면 통보를 하게 합니다. 사실 당신은 브라우저에서 이 작업을 하는거 에  대해 선택 여지가 없습니다. 예를 들어 내장된 동기 `sleep`이 없습니다. (이전에 구현한 `sleep()` 과 같은) 대신에 `setTimeout()`으로 비동기적인 `sleep`을 수행 합니다.

다음 섹션은 결과를 위하여 비동기적으로 대기 할 수 있는 기술적인 설명을 할 것입니다.

## 3. 비동기적 결과 수신(Receiving results asynchronously)
> 비동기적 결과를 수신하기 위한 2가지 패턴 : **events** 와 **callback**