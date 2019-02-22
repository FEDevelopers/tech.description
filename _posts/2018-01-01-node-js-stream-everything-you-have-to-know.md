---
layout: post
title: Node.js Stream 당신이 알아야할 모든 것
---

> 원본: [https://medium.freecodecamp.com/node-js-streams-everything-you-need-to-know-c9141306be93](https://medium.freecodecamp.com/node-js-streams-everything-you-need-to-know-c9141306be93)

![](https://cdn-images-1.medium.com/max/2000/1*xGNVMFqXXTeK7ZyK2eN21Q.jpeg)

노드js의 스트림은 정말 다루기 어렵고 이해하기 힘든 것으로 유명합니다. 글세요. 이제는 더 이상 그렇지만은 않을 거에요.

지난 몇년간 개발자들은 스트림을 쉽게 다루기 위한 노드 패키지를 만들어 왔지만, 이 글에서는 [내이티브 노드 스트림 API](https://nodejs.org/api/stream.html)에만 중점을 두겠습니다.

> 스트림은 노드의 가장 멋진 아이디어 입니다. 동시에 가장 오해하고 있는 것이기도 합니다. - Dominic Tarr

## 스트림이 정확히 뭔가요?

스트림은 배열이나 문자열같은 데이터 컬력션입니다. 스트림이 어려운 것은 한번에 모든 데이터를 얻을 수 없다는 점이죠. 게다가 메모리에 딱 맞지도 않구요. 스트림의 이러한 점은 엄청 큰 데이터를 다룰 때나, 외부 소스로부터 데이터를 한번에 한 청크(chunk)씩 가져올때 힘을 발휘합니다.

스트림이 큰 데이터만 다루는 것은 아닙니다. 코드를 조합할수 있도록 해 주지요. 리눅스 명령어를 다른 작은 명령어들과 파이핑(piping)하여 구성할수 있는 것처럼, 노드 스트림에서도 완전 똑같이 할 수 있습니다.

```bash
$ grep -R exports * | wc -l
```

```js
const grep = ... // grep 출력을 위한 스트림
const wc = ... // wc 입력을 위한 스트림

grep.pipe(wc)
```

노드의 많은 내장 모듈은 스트림 인터페이스를 구현합니다.

![](https://cdn-images-1.medium.com/max/800/1*lhOvZiDrVbzF8_l8QX3ACw.png)
<small>스크린샷은 제 Pluralsight course 에서 캡쳐한 것입니다 - Advanced Node.js</small>

위에 있는 목록은 읽기 가능하거나 쓰기 가능한 스트림인 네이티브 노드 객체들입니다. 이것들 중에는 TCP 소켓, zlib, crypto 같이 읽기/쓰기 모두 가능한 객체도 있지요.

이 객체들은 서로 밀접하게 관련되어 있습니다. 클라이어트에서는 HTTP 응답이 읽기 가능한 스트림인 반면 서버에서는 쓰기 가능한 스트림이 됩니다. HTTP의 경우 기본적으로 하나의 오브젝트로부터 읽고(`http.ImcomingMessage`) 다른 곳으로 쓰기(`http.ServerResponse`) 때문입니다.

또한 `stdio` 스트림들(`stdin`, `stdout`, `stderr`)이 자식 프로세스로 어떻게 반대 스트림 타입을 갖는지 확인해 보세요. 매우 수월한 방법으로 메인 프로세스의 `stdio` 스트림과 연결할 수 있습니다.

## 스트림의 실제 예제

이론은 훌륭하지만 실제로는 충분히 좋지 않는 경우가 있습니다. 메모리 사용과 관련해 서로 다른 스트림이 만들수 있는 코드를 예제를 통해 살펴 보겠습니다.

먼저 큰 파일을 만들어 보죠.

```js
const fs = require('fs');
const file = fs.createWriteStream('./big.file');

for(let i=0; i<= 1e6; i++) {
  file.write('Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.\n');
}

file.end();
```

제가 큰 파일을 만들기 위해 무엇을 사용했는지 보세요. 쓰기 가능한 스트림이죠!

`fs` 모듈은 스트림 인터페이스로 파일을 쓰고 읽는데 사용할 수 있습니다. 위 예제에서는 반복문으로 백만 줄의 쓰기 가능한 스트림을 통해 `big.file`를 만들고 있습니다.

이 스크립트를 실행하면 약 400MB 정도의 파일을 생성합니다.

아래는 `big.file`만 제공하도록 디자인된 간단한 노드 웹 서버입니다.

```js
const fs = require('fs');
const server = require('http').createServer();

server.on('request', (req, res) => {
  fs.readFile('./big.file', (err, data) => {
    if (err) throw err;

    res.end(data);
  });
});

server.listen(8000);
```

요청을 받은 서버는 비동기 메소드인 `fs.readFile`로 큰 파일을 제공할 것입니다. 하지만 여러분, 이벤트 루프를 막는 것이 좋은 방법은 아닌것 같은데...... 모든 것이 훌륭한가요? 그런가요?

음, 우리가 서버를 구동하고 클라이언트에서 요청할 때 어떠한 일이 일어나는지 살펴봅시다. 그리고 메모리 상태를 모니터링 해봅시다.

제가 서버를 돌렸을 때, 보통의 메모리 사용량인 8.7MB로 시작했습니다.

![](https://cdn-images-1.medium.com/max/1600/1*125_8HQ4KzJkeBcj1LcEiQ.png)

그리고나서 서버로 접속했죠. 이때 메모리 소비에 무슨 일이 벌어지는지 보세요.

![](https://cdn-images-1.medium.com/max/1600/1*SGJw31T5Q9Zfsk24l2yirg.gif)

헐! 메모리소비가 무려 434.8MB로 올랐어요.

`big.file`을 응답 객체로 쓰기 전에 기본적으로 파일 내용을 전부 메모리에 올려 놓습니다. 이것은 매우 비효율적이죠.

HTTP 응답 객체(위 코드의 `res`)는 쓰기 가능한 객체이기도 합니다. 만약 `big.file`의 내용을 대표하는 읽기 가능한 스트림이 있다면, 간단히 두 개를 서로 파이프로 이어주기만 하면 거의 같은 결과를 달성할수 있을 것입니다. 물론 메모리를 400MB나 사용하지 않고도 말이죠.

노드의 `fs` 모듈은 어떠한 파일에 대해서도 `createReadStream` 메소드를 이용하면 읽기 가능한 스트림을 제공해 줍니다.

```js
const fs = require('fs');
const server = require('http').createServer();

server.on('request', (req, res) => {
  const src = fs.createReadStream('./big.file');
  src.pipe(res);
});

server.listen(8000);
```

이제 다시 서버에 접속하면 마법이 일어납니다! (아래 메모리 사용량을 보세요).

![](https://cdn-images-1.medium.com/max/1600/1*iWNNIMhF9QmD25Vho6-fRQ.gif)

_뭐가 어떻게 된거죠?_

클라이언트가 큰 파일을 요청했을 때, 우리는 이것을 한번에 한 청크씩 스트림으로 흘려 보냅니다. 이것은 모든 것을 메모리에 버퍼로 잡지 않는다는 것을 의미하죠. 메모리 사용은 25MB까지만 증가하고요.

그럼 이 예제를 좀 더 극단으로 밀어 보겠습니다. 백만줄이 아닌 오백만 줄의 `bigfile`을 다시 만들어 볼게요. 아마 이 파일은 2GB 이상일 것이고 노드의 기본 버퍼 한계치보다 큰 사이즈가 될 것입니다.

만약 `fs.readFile`로 파일을 제공한다면, 한계치를 변경하지 않고서는 파일을 제공할 수 없을 것입니다. 하지만 `fs.createReadStream`을 사용하면 요청자에게 2GB 데이터를 스트리밍할 수 있습니다. 게다가 프로세스의 메모리 사용량은 거의 차이가 없을 것이고요.

자 이제 스트림을 배울 준비가 되었나요?

## 스트림 101

Node.js에는 네 가지 기초 스트림 타입이 있습니다. Readable, Writable, Duplex, Transform 스트림입니다.

* 읽기 가능한(readable) 스트림은 소비할수 있는 데이터를 추상화한 것입니다. 예를들어 `fs.createReadStream` 메소드가 그렇죠.
* 쓰기 가능한 (writable) 스트림은 데이터를 기록할수 있는 종착점을 추상화한 것입니다. 예를 들어 `fs.createWriteStream` 메소드가 있죠.
* 듀플렉스(duplex) 스트림은 읽기/쓰기 모두 가능합니다. 예를 들어 TCP 소켓이 있죠.
* 트랜스폼(transform) 스트림은 기본적으로 듀플렉스 스트림입니다. 데이터를 읽거나 기록할 때 수정/변환될수 있는 데이터죠. 예를들어 gzip을 이용해 데이터를 압축하는  `zlib.createGzip` 스트림이 있습니다. 입력은 쓰기 가능한 스트림이고 출력은 읽기 가능한 스트림인 트랜스폼 스트림을 생각할 수 있을 겁니다. 트랜스폼 스트림이 *"스트림을 통해(through streams)"*라고 불리는 것을 들어 봤을 겁니다.

모든 스트림은 `EventEmitter`의 인스턴스 입니다. 데이터를 읽거나 쓸 때 사용할 이벤트를 방출(emit) 합니다. 하지만, `pipe` 메소드를 이용하면 더 간단하게 스트림 데이터를 사용할 수 있습니다.

### pipe 메소드

우선 마법같은 코드를 보시죠.

```
readableSrc.pipe(writableDest)
```

읽기 가능한 스트림의 출력과 쓰기 가능한 스트림의 입력을 파이프로 연결하였습니다. 소스는 읽기 가능한 스트림이 되고 목적지는 쓰기 가능한 스트림이 됩니다. 물론 둘은 듀플랙스/트랜스폼 스트림이 될 수도 있습니다. 사실, 듀플렉스 스트림으로 연결하면 리눅스에서 하는 것처럼 파이프 체인을 호출할 수 있습니다.

```
reableSrc
  .pipe(transformStream1)
  .pipe(transformStream2)
  .pipe(finalWritableDest)
```

`pipe` 메소드는 위처럼 체이닝할 수 있도록 목적지 스트림을 반환합니다. `a`(읽기 가능한) 스트림, `b`와 `c`(듀플랙스), 그리고 `d`(쓰기 가능한)를 위해 우리는 이렇게 코딩할 수 있어요.

```bash
a.pipe(b).pipe(c).pipe(d)

# 같은 코드
a.pipe(b)
b.pipe(c)
c.pipe(d)

# 리눅스에서는 이렇게 합니다
$ a | b | c | d
```

`pipe` 메소드는 스트림을 사용할수 있는 가장 쉬운 방법입니다. 보통 `pipe` 메소드를 사용하거나 이벤트와 함께 스트림을 사용하는 것을 추천하지만 두 개를 같이 쓰는 것 만큼은 피해야 합니다. `pipe` 메소드를 사용할 경우 이벤트를 사용하지 말아야 합니다. 좀 더 커스텀한 방법으로 스트림을 사용하려면 이벤트를 사용해야 합니다.

### 스트림 이벤트

읽기 가능한 스트림으로부터 읽거나 쓰기 가능한 스트림으로 쓰는 것 외에도 `pipe` 메소드는 자동으로 몇가지 작업을 관리합니다. 예를 들어, 에러 처리나 파일의 끝부분 처리, 그리고 어떤 스트림이 다른 것들에 비해 느리거나 빠를 경우를 처리합니다.

하지만 스트림은 직접 이벤트와 함께 사용할수 있습니다. 여기 `pipe` 메소드가 데이터를 읽고 쓰기위해 주로 하는 것을 나타내는 간단한 코드가 있습니다.

```js
// readable.pipe(writable)

readable.on('data', (chunk) => {
  writable.write(chunk);
});

readable.on('end', () => {
  writable.end();
});
```

주요 이벤트 그리고 스트림과 함께 사용할 수 있는 함수 목록은 다음과 같습니다.
![](https://cdn-images-1.medium.com/max/1600/1*HGXpeiF5-hJrOk_8tT2jFA.png)

<small>Pluralsight 코스로 부터 캡처함 것임 - Advanced Node.js</small>

이벤트와 함수들은 보통 함께 사용되기 때문에 서로 관련이 있습니다.

읽기 가능한 스트림에서 가장 중요한 이벤트는 다음과 같습니다.

* `data` 이벤트: 스트림이 소비자에게 데이터 청크를 전송할때 발생합니다.
* `end` 이벤트: 더 이상 소비할 데이터가 없을때 발생합니다.

쓰기 가능한 스트림에서 가장 중요한 이벤트는 다음과 같습니다.

* `drain` 이벤트: 쓰기 가능한 스트림이 더 많은 데이터를 수신할 수 있다는 신호입니다.
* `finish` 이벤트: 모든 데이터가 시스템으로 플러시 될때 생성됩니다.

이벤트와 함수는 커스터마이징된 스트림을 사용하기 위해 함께 사용할 수 있습니다. 읽기 가능한 스트림을 사용하기 위해 `pipe`/`unpipe` 메소드를 사용하거나 `read`/`unshift`/`resume` 메소드를 사용할 수 있습니다. 쓰기 가능한 스트림을 사용하기 위해 우리는 이것을 `pipe`/`unpip`의 종착점으로 만들수 있습니다. 혹은 `write` 메소드로 쓰고 끝날때 `done` 메소드를 호출하면 됩니다.

### 읽기 가능한 스트림의 일시 정지 모드와 흐름 모드

읽기 가능한 스트림은 사용할 수 있는 방법에 영향을 주는 두 가지 주요 모드가 있습니다.

* 일시 정지 (Pause) 모드
* 흐름 (flowing) 모드

두 개는 풀(pull), 푸시(push) 모드라고도 합니다.

기본적으로 모든 읽기 가능한 스트림은 일시정지 모드에서 시작하지만, 필요에 따라 흐름 모드로 변경되거나 일시 정지 모드로 되돌아갈 수도 있습니다. 가끔은 자동으로 스위칭 되기도 합니다.

읽기가능한 스트림이 일시정지 모드일 때, 스트림을 읽기 위해 `read()` 메소드를 호출할 수 있습니다. 하지만 흐름 모드일 경우에는 데이터가 연속적으로 흐르고 있고 우리는 이것을 기다려야 합니다.

흐름 모드일 때, 만약 이것을 수신할 소비자가 없으면 데이터는 사라지게 됩니다. 그렇기 때문에 흐름 모드에서 읽기 가능한 스트림이 있을 때는 `data` 이벤트 핸들러가 필요합니다. 사실, `data` 이벤트 핸들러를 추가하는 것은 일시 정지된 스트림을 흐름 모드로 바꾸는 것이고, `data` 이벤트 핸들러를 제거하는 것은 일시 정지 모드로 되돌리는 것입니다. 이 중 몇 가지는 예전 노드 스트림 인터페이스와의 호환성을 위해 수행됩니다.

수동으로 두 모드 간에 변경하려면 `resume()`, `pause()` 메도드를 사용하면 됩니다.

![](https://cdn-images-1.medium.com/max/1600/1*HI-mtispQ13qm8ib5yey3g.png)

<small>Pluralsight 코스로부터 캡처함 것임 - Advanced Node.js</small>

`pipe` 메소드로 읽기 가능한 스트림을 사용할 때는 두 모드를 신경쓰지 않아도 됩니다. `pipe` 가 자동으로 관리하기 때문이죠.

## 스트림 구현하기

노드js의 스트림을 말할때 두 가지 다른 테스트가 있습니다

* 스트림을 **구현**하는 작업
* 스트림을 **사용**하는 작업

지금까지는 스트림을 사용하는 것에 대해 말했으니까 이젠 직접 구현해 볼까요?

스트림 구현체들은 대게 `stream` 모듈을 `require` 합니다

### 쓰기 스트림 만들기

쓰기 가능한 스트림을 구현하려면 스트림 모듈의 `Writable` 생성자를 사용해야 합니다

```js
const { Writable } = require('stream')
```

많은 방법으로 쓰기 가능한 스트림을 구현할 수 있습니다. 예를들어 `Writable` 생성자를 확장하는 거죠.

```js
class myWritableStream extedns Writable {
}
```

하지만... 저는 좀 더 간단한 방법을 좋아해요. `Writable` 생성자로 객체를 생성하면서 옵션을 전달하는 거죠. 필수 옵션은 앞으로 기록될  데이터 청크(chunk)를 보내기 위한 `write` 함수 입니다.

```js
const { Writable } = require('stream');

const outStream = new Writable({
  write(chunk, encodeing, callback) {
    console.log(chunk.toString());
    callback();
  }
});

process.stdin.pipe(outStream);
```

이 메소드는 세 개의 인자가 필요해요.

* 스트림을 다르게 설정하지 않는다면 `chunk`는 보통 버퍼입니다.
* 위에서는 `encoding` 인자를 썼지만 보통은 무시할 수 있습니다.
* `callback`은 데이터 청크를 처리한 뒤에 호출되는 함수 입니다. 쓰기를 성공했지는 여부를 알리는 신호입니다. 실패를 알리려면 에러 객체와 함께 콜백을 호출하면 됩니다.

`outStream`에서는 간단하게 `console.log`로 청크 문자열을 출력했습니다. 성공을 알리기 위해 에러 없이 `callback`을 호출합니다. 매우 간단하지만 그렇게 유용한 *에코(echo)* 스트림은 아닙니다. 어떤 것이든 받으면  에코할 것입니다.

이 스트림을 소비하려면 읽기 스트림인 `process.stdin`을 `outStream`으로 연결하면 됩니다.

위 코드를 실행하면 여러분이 `process.stdin`으로 입력한 것은 `outStream`과 `console.log`를 이용해 에코되어 나올 것입니다.

이건 구현할만한 스트림은 아니에요. 이미 빌트인으로 구현되어 있기 때문이죠. `process.stdout`이랑 굉장히 비슷해요. `stdin`을 `stdout`으로 연결만 하면 거의 똑같은 에코 기능을 만들수 있습니다. 아래 한 줄로 말이죠.

```js
process.stdin.pipe(process.stdout);
```

### 읽기 스트림 만들기

읽기 가능한 스트림을 만들기 위해서는 `Readable` 인터페이스가 필요하고 이것으로 객체를 만들어야 합니다.

```js
const { Readable } = require('stream');

const inStream = new Readable({});
```

읽기 가능한 스트림을 구현하는 간단한 방법이 있습니다. 소비할 데이터를 바로 `push`할 수 있죠.

```js
const { Readable } = require('stream');

const inStream = new Readable();

inStream.push('ABCDEFGHIJKLM');
inStream.push('NOPQRSTUVWXYZ');

inStream.push(null); // 더 이상 데이터 없음

inStream.pipe(process.stdout);
```

`null` 객체를 `push` 하는 것은 더 이상 데이터가 없다는 신호입니다.

이 스트림을 소비하려면 쓰기 가능한 스트림은 `process.stdout`으로 연결만 하면 됩니다.

위 코드를 실행하면 `inStream` 으로부터 데이터를 읽을수 있고 표준 출력으로 이것을 에코할 것입니다. 정말 간단하죠? 하지만 이것도 효율적인건 아니에요.

기본적으로 `process.stdout`하기 전에 스트림에 있는 모든 데이터를 푸시해 버릴겁니다. 더 나은 방법은 **요청이 있을 때 (on demand)** 데이터를 푸시하는 겁니다. 즉 소비자가 데이터를 달라고 요청할 때 말이죠. 읽기 스트림 설정에서 `read()` 메소드를 구현하면 그렇게 할 수 있습니다.

```js
const inStrem = new Readable({
  read(size) {
    // 데이터 요구가 있고... 누군가 이것을 읽고자 함
  }
});
```

읽기 가능한 스트림에서 `read` 메소드가 호출되면, 구현부는 일부 데이터를 큐(queue)에 푸시할 수 있습니다. 예를 들어, "A"를 나타내는 문자코드 65부터 한 글자씩 여러번 푸시해서 보낼 수 있습니다.

```js
const inStream = new Readable({
  read(size) {
    this.push(String.fromCharCode(this.currentCharCode++));
    if (this.currentCharCode > 90) {
      this.push(null);
    }
  }
});

inStream.currentCharCode = 65;

inStream.pipe(process.stdout);
```

읽기 스트림을 읽는 동안 `read` 메소드는 계속 실행되고 더 많은 문자를 푸시할 것입니다. 언젠가는 이 사이클을 멈춰야하는데 이게 바로 if 문이 currentCharCode가 90("Z")보다 클때 null을 푸시하는 이유입니다.

이 코드는 먼저 만든 코드와 동일하지만 소비자가 요청할 때만 데이터를 푸시할 수 있게 되었습니다. 항상 이렇게 구현해야 합니다.

### Duplex/Transform 스트림 만들기

듀플렉스 스트림을 이용하면 한 객체로 읽기/쓰기 가능한 스트림을 만들 수 있습니다. 두 인터페이스로부터 상속한 것 처럼 말이죠.

위에 구현했던 쓰기 스트림과 읽기 스트림을 합친 듀플렉스 예제가 아래 있습니다.

```js
const { Duplex } = require('stream');

const inoutStream = new Duplex({
  write(chunk, encoding, callback) {
    console.log(chunk.toString());
    callback();
  },

  read(size) {
    this.push(String.fromCharCode(this.currentCharCode++));

    if (this.currentCharCode > 90) {
      this.push(null);
    }
  }
});

inoutStream.currentCharCode = 65;

process.stdin.pipe(inoutStream).pipe(process.stdout);
```

메쏘드를 조합해서 A에서 Z까지의 문자를 읽고 이것을 에코하는 듀플렉스 스트림을 사용할 수 있게 되었습니다. 에코 기능을 사용하기 위해 읽기 가능한 `stdin` 스트림을 이 듀플렉스 스트림으로 연결했습니다. 그리고 듀플렉스 스트림을 읽기 가능한 `stdout` 스트림으로 연결해서 A부터 Z까지 문자를 출력할 수 있습니다.

듀플렉스 스트림의 읽기/쓰기 가능한 면이 다른 것으로부터 완전히 독립적으로 동작한다는 것을 이해하는 것이 중요합니다. 이것 두 개 기능을 하나의 객체로 그룹핑한 것일 뿐입니다.

트랜스폼 스트림은 듀플렉스 스트림보다 더 재밌는데 입력으로부터 출력이 계산되기 때문입니다.

트랜스폼 스트림을 만들 때는 `read`나 `write` 메소드를 구현할 필요가 없습니다. 이 둘을 결합한 `transform` 메소드만 구현하면 됩니다. `write` 메소드의 시그니처를 가지고 있고 데이터를 `push` 할 수 있습니다.

아래는 입력한 데이터를 대문자로 변경한 뒤 에코하는 간단한 트랜스폼 스트림입니다.

```js
const { Transform } = require('stream');

const upperCaseTr = new Transform({
  transform(chunk, encoding, callback) {
    this.push(chunk.toString().toUpperCase());
    callback();
  }
});

process.stdin.pipe(upperCaseTr).pipe(process.stdout);
```

이전 듀플렉스 스트림 예제와 완전히 똑같이 동작하는 트랜스폼 스트림에서 `transform()` 메소드만 구현하면 됩니다. 이 메소드에서는  `chunk`를 대문자로 변경하고 읽기 가능한 부분으로 `push` 하고 있습니다.

### 스트림 오브젝트 모드

기본적으로 스트림은 버퍼(Buffer)와 문자열(String) 값을 기대합니다. `objectMode` 플래그가 있는데 자바스크립트 오브젝트를 허용하기  위해 사용할 수 있습니다.

이것을 설명하는 간단한 예제를 살펴 보죠. 트랜스폼 스트림의 조합은 콤마로 구분된 문자열을 자바스크립트 오브젝트로 맵(map)하기 위한 기능을 만듭니다. 그래서 `"a,b,c,d"`가 `{a: b, c: d}`가 되는 것이죠.

```js
const { Transform } = require('stream');

const commaSplitter = new Transform({
  readableObjectMode: true,

  transform(chunk, encoding, callback) {
    this.push(chunk.toString().trim().split(','));
    callback();
  }
});

const arrayToObject = new Transform({
  readableObjectMode: true,
  writableObjectMode: true,

  transform(chunk, encoding, callback) {
    const obj = {};
    for (let i=0; i < chunk.length; i+=2) {
      obj[chunk[i]] = chunk[i+1];
    }
    this.push(obj);
    callback();
  }
});

const objectToString = new Transform({
  writableObjectMode: true,

  transform(chunk, encoding, callback) {
    this.push(JSON.stringify(chunk) + '\n');
    callback();
  }
});

process.stdin
  .pipe(commaSplitter)
  .pipe(arrayToObject)
  .pipe(objectToString)
  .pipe(process.stdout)
```

`"a,b,c,d"` 같은 입력 문자열을 `commaSplitter`로 보냅니다. 이것은 읽기 가능한 데이터로 `["a", "b", "c", "d"]` 배열을 푸시 합니다. 우리는 문자열이 아닌 객체를 푸시 할거니깐 `readableObjectMode` 플래그를 추가해야 합니다.

그리고나서 배열을 얻고 `arrayToObject` 스트림으로 연결합니다. 객체를 받기 위해서 `writableObjectMode` 플래그가 필요하죠. 이건 다시 객체(배열 객체로 매핑한)를 푸시할 것이고 `readableObjectMode` 플래그가 필요한 이유입니다. 마지막 `objectToString` 스트림은 객체를 받지만 문자열을 푸시합니다. 때문에 `writableObjectMode` 플래그만 필요합니다. 읽기 가능한 부분은 일반 문자열(문자열화된 객체)입니다.

![Usage of the example avobe](https://cdn-images-1.medium.com/max/1600/1*u2kQzUD0ruPpt-xx0UOHoA.png)

### 빌트인 트랜스폼 스트림

노드는 매우 쓸만한 빌트인 트랜스폼 스트림을 가지고 있습니다. 이름하야 zip과 crypto 스트림이죠.

아래는 파일 압축 스크립트를 생성하기 위해 `fs` 읽기/쓰기 가능한 스트림과 함께 `zlib.crateGzip()` 스트림을 함께 사용하는 예제입니다.

```js
const fs = require('fs');
const zlib = require('zlib');
const file = procdss.argv[2];

fs.createReadStream(file)
  .pipe(zlib.createGzip())
  .pipe(fs.createWriteStream(file + '.gz'));
```

인자로 전달한 파일을 gzip으로 압축하기 위해 이 스크립트를 사용할 수 있습니다. 파일을 zlib 빌트인 트랜스폼 스트림으로 넣기 위해 읽기  스트림을 연결했고, 새로운 gzip 파일을 위해 읽기 스트림을 연결했습니다. 간단하죠?

파이프를 사용하는 것이 멋진 이유는 필요하다면 이벤트와 함께 구성할 수 있다는 점이죠. 예를들어, 스크립트가 돌아가는 중에는 상태를 유저에게 보이고 완료되면 "Done" 메세지를 보이게 하고 싶습니다. `pipe` 메소드가 마지막 스트림을 리턴하기 때문에 이벤트 핸들러 등록을 체인(chain) 할수 있습니다.

```js
const fs = require('fs');
const zlib = require('zlib');
const file = process.argv[2];

fs.createReadStream(file)
  .pipe(zlib.createGzip())
  .on('data', () => process.stdout.write('.'))
  .pipe(fs.createWriteStream(file + '.gz'))
  .on('finish', () => console.log('Done'));
```

`pipe` 메소드와 함께 스트림을 쉽게 소비할 수 있지만, 여전히 필요에 따라 이벤트를 이용해 스트림과의 상호작용을 더 커스터마이징 하고 싶습니다.

`pipe` 메소드의 위대한 점은 프로그램을 조각 조각 *구성(compose)*해서 읽기 쉽게 만들수 있다는 점입니다. 예를들어 위처럼 `data` 이벤트를 리스닝하는 대신, 진행 상태를 리포팅하기 위해 간단한 트랜스폼 스트림을 만들수 있습니다. 그리고 `.on()` 호출 대신에 또 다른 `.pipe()`를 호출합니다.

```js
const fs = require('fs');
const zlib = require('zlib');
const file = process.argv[2];

const { Transform } = require('stream');

const reportProgress = new Transform({
  transform(chunk, encoding, callback) {
    process.stdout.write('.');
    callback(null, chunk);
  }
});

fs.createReadStream(file)
  .pipe(zlib.createGzip())
  .pipe(reportProgress)
  .pipe(fs.createWriteStream(file + '.zz'))
  .on('finish', () => console.log('Done'));
```

이 `reportProgess` 스트림은 단순히 스트림을 그대로 통과시키기만 하지만, 진행 상태 정보를 표준 출력으로 보고해 줍니다. `transform()` 메소드 안으로 데이터를 푸시하기 위해 어떻게 `callback()` 함수의 두번째 인자를 사용하는지 주목해 보세요. 데이터를 푸시하는 것과 같습니다.

스트림을 결합하는 어플리케이션은 끝이 없습니다. 예를 들어, gzip 압축 전이나 후에 파일을 암호화해야 할 필요가 있다면, 또 다른 트랜스폼 스트림을 같은 순서로 연결하면 됩니다. 노드의 `crypto` 모듈을 그렇게 사용할 수 있습니다.

```js
const crypto = require('crypto');
// ...

fs.createReadStream(file)
  .pipe(zlib.createGzip())
  .pipe(crypto.createCipher('aes192', 'a_secret'))
  .pipe(reportProgress)
  .pipe(fs.createWriteStream(file + '.zz'))
  .on('finish', () => console.log('Done'));
```

이 스크립트는 압축 후 전달된 파일을 암호화하고 시크릿이 있는 사용자만 생성된 파일을 사용할 수 있습니다. 암호화 되었기 때문에 보통의 unzip 유틸리티로는 압축을 풀 수 없습니다.

실제로 위 스크립트로 압축된 파일을 풀기 위해서는 crypto와 zlib의 역순 스트림이 필요합니다. 간단해요.

```js
fs.createReadStream(file)
  .pipe(crypto.createDecipher('aes192', 'a_secret'))
  .pipe(zlib.createGunzip())
  .pipe(reportProgress)
  .pipe(fs.createWriteStream(file.slice(0, -3))
  .on('finish', () => console.log('Done'));
```

전달된 파일이 압축된 버전이라고 가정하면, 위 코드는 이것으로부터 읽기 스트림을 생성할 것이고, 동일한 시크릿을 이용해 `createDeciphoer()` 스트림과 연결할 것입니다. 그리고 `createGunzip()` 스트림으로 출력을 연결하고, 확장자 부분을 제거하고 파일을 출력할 것입니다.

여기까지가 이번 주제입니다. 읽어 주셔서 감사합니다! 다음에 봐요!
