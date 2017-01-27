> 이 문서는 http://www.nearform.com/nodecrunch/developer-initiates-io-operation-wont-believe-happens-next/를 번역한 내용입니다.

_코어 팀의 [Saúl Ibarra Corretgé님](https://github.com/saghul), 이 포스트를 리뷰해 주셔서 고맙습니다._


노드JS 개발자로서 우리는 매우 행복하다. 자바스크립트로 제공되는 추상화는 개발자가 쓰레드나 동기화 같은 저수준 시스템 개념과 씨름하지 않고 즐거운 어플리케이션 개발에 집중할 수 있도록 하기 때문이다. 하지만 어떻게 생각하든지간에 자바스크립트 코드는 아래 그림처럼 대부분 C/C++로 작성된 수많은 저수준 레벨 코드 위에 위치한다. 이 포스트는 아래 그림처럼 다양한 계층을 탐색할 때 자바스크립트 함수 호출을 추척할 것이다. 이벤트 루프에 대한 기본적인 이해가 있다고 가정하고 진행하겠다. 

![](http://www.nearform.com/wp-content/uploads/2017/01/application-code-1.png)

## 자바스크립트 파일 시스템 요청

노드JS의 이해를 위해 아래 코드에서 `fs.stat()` 실행사 무슨 일이 일어나는지 살펴보겠다. 우리는 노드 코어와 비동기 I/O 라이브러리인 [libuv](http://libuv.org/)를 통해 코드의 실행을 추적할 것이다. 포스트 전반에 많은 깃헙 코드 링크가 있고 각 링크는 노드 7.3.0 릴리즈 커밋에 고정되어 있으므로 노드 코드베이스가 변경되더라도 링크는 유지될 것이다.

```javascript
'use strict'; 
const fs = require('fs'); 
fs.stat(__filename, function statCb (err, stats) { 
  if (err) { 
    return console.error(err); 
  } 
  console.log(stats); 
});
```

`fs.stat()`를 호출하면 먼저 `fs` 노드 코어 모듈로 들어간다. 아래 [`fs.stae()` 소스 코드](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/lib/fs.js#L887-L893)는 매우 간단하다. 처음 두 줄은 [`callback`이 함수인지 확인](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/lib/fs.js#L101)하고, path가 [어떤 null 바이트도 포함하지 않은지 확인](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/lib/fs.js#L115)한다. 다음 `FSReqWrap` 객체를 만들고 `callback`을 `oncomplete` 속성에 연결한다. `FSReqWrap`은 [노드 바인딩 계층을 통해 자바스크립트에서 접근할수 있는](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/lib/fs.js#L15) [C++ 클래스](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/src/node_file.cc#L51)다. `FSReqWrap`은 "file system request wrap"의 약자로 libuv로 파일 시스템 작업 요청을 캡슐화한다. `oncomplete` 필드는 작업이 완료될 때 호출되는 콜백 함수와 연결된다. 한번 요청이 설정되면 `binding.state()`, `State()`와 관련된 [C++ 함수](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/src/node_file.cc#L606)가 호출된다.

```javascript
fs.stat = function(path, callback) { 
  callback = makeCallback(callback); 
  if (!nullCheck(path, callback)) return; 
  var req = new FSReqWrap(); 
  req.oncomplete = callback; 
  binding.stat(pathModule._makeLong(path), req); 
};
```

## 노드JS 바인딩 계층으로 들어가기 

이 시점에서 우리는 자바스크립트 계층을 떠나 C/C++ 계층으로 들어온다. Here be dragons unicorn velociraptors. `Stat()` 함수의 재밌는 부분은 아래 `if` 문장이다. 이 조건은 호출이 동기인지(`fs.statSync()`) 비동기인지(`fs.stat()`)지를 결정한다. 여기서는 `FSReqWrap` 객체를 두 번째 파라매터로 하여 `binding.stat()`가 호출되었다. 따라서 `args[1]->IsObject()`는 참이 되고 `ASYNC_CALL` 경로를 사용하게 된다. 

```c
if (args[1]->IsObject()) { 
  ASYNC_CALL(stat, args[1], UTF8, *path) 
} else { 
  SYNC_CALL(stat, *path, *path) 
  args.GetReturnValue().Set( 
    BuildStatsObject(env, static_cast(SYNC_REQ.ptr))); 
}
```

`ASYNC_CALL`[C++ 매크로](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/src/node_file.cc#L360-L361)이며 함수가 아니다. 게다가 `ASYNC_DEST_CALL`를 사용하여 `ASYNC_CALL`을 더 확장한다. 컴파일 시간에 ASYNC_CALL 매크로는 아래 C++ 코드로 확장된다. 이 코드의 가장 중요한 부분이 `uv_fs_sate()` 호출 부분이다. 이 함수는 노드 어플리케이션 이벤트 루프에 묶여있던 stat 요청을 libuv에게 전달한다. 작업이 끝나면 After 함수가 호출된다.

```c++
Environment* env = Environment::GetCurrent(args);
CHECK(request->IsObject());
FSReqWrap* req_wrap = FSReqWrap::New(env, request.As Object>;(),
                                     "stat", dest, encoding);
int err = uv_fs_stat(env->event_loop(),
                     req_wrap->req(),
                     __VA_ARGS__,
                     After);
req_wrap->Dispatched();
if (err < 0) {   uv_fs_t* uv_req = req_wrap->req();
  uv_req->result = err;
  uv_req->path = nullptr;
  After(uv_req);
  req_wrap = nullptr;
} else {
  args.GetReturnValue().Set(req_wrap->persistent());
}
```

## 노드JS 종료, libuv 시작

우리는 노드 C++ 계층에서 떠나 libuv C 코드로 이동한다. libuv는 독립적인 워크 쓰레드 풀로 워크를 떠넘기면서 비동기 파일시스템 작업을 제공할수 있다. 가능한한 플랫폼 독립적으로 유지하기 위해 libuv는 유닉스와 윈도우 양쪽에 같은 기능을 구현해야 한다. 이 글의 목적을 위해 리눅스에서 돌아간다고 가정하고 [`uv_fs_stat()`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/win/fs.c#L2252)의 적절한 구현으로 넘어가겠다. 만약 윈도우에 환경이라면 [Windows `uv_fs_stat()` 구현](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/win/fs.c#L2252)을 시작하면서 비슷한 방법으로 따라오길 바란다.

비(non) 윈도우에서 아래 코드에 있는 `uv_fs_stat()`는 [`INIT`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L63), [`PATH`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L78), [`POST`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L115) 3개의 매크로로 구성된다. `INIT`은 파일 시스템 요청를 초기화한 뒤 적절하게 [콜백을 설정](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L74)하고, `PATH`는 파일 경로를 설정한다. `POST` 매크로는 [쓰레드 풀로 요청을 전달](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L118)하거나 [동기 요청일 경우 태스크를 실행](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L122-L123)한다. 우리는 [`uv__work_submit()`](https://github.com/nodejs/node/blob/db1087c9757c31a82c50a1eba368d8cba95b57d0/deps/uv/src/threadpool.c#L180)으로 파일시스템 요청을 전달하는 비동기 코드 경로를 따른다. [`uv__fs_work()`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L923) 함수는 요청을 처리하기 위해 전달 된다. 추가로 [`uv__fs_done()`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L986) 함수는 요청이 완료될때 실행되는 콜백으로 전달된다.

```c
int uv_fs_stat(uv_loop_t* loop, 
               uv_fs_t* req, 
               const char* path, 
               uv_fs_cb cb) { 
  INIT(STAT); 
  PATH; 
  POST; 
}
```

 `un__work_submit()` 내부에서, 요청은 [`post()`](https://github.com/nodejs/node/blob/db1087c9757c31a82c50a1eba368d8cba95b57d0/deps/uv/src/threadpool.c#L107) 함수를 통해 쓰레드 풀의 작업 큐([여기](https://github.com/nodejs/node/blob/db1087c9757c31a82c50a1eba368d8cba95b57d0/deps/uv/src/queue.h)서 구현된)에 [전달](https://github.com/nodejs/node/blob/db1087c9757c31a82c50a1eba368d8cba95b57d0/deps/uv/src/threadpool.c#L188)된다. `post()`는 뮤텍스를 사용해서 작업 큐(`wq`)에 대한 접근을 동기화한다. 이것을 통해 메인 쓰레드와 작업 쓰레드가 안전한 방법으로 하나의 큐를 공유할 수 있다. 큐에서 요청을 제거하는 것은 워커 쓰레드에 달려있다. 

기본적으로 작업 큐의 항목을 처리할수 있는 [`worker()`](https://github.com/nodejs/node/blob/db1087c9757c31a82c50a1eba368d8cba95b57d0/deps/uv/src/threadpool.c#L64) 함수로 구현된 4개의 쓰레드 풀 워커가 있다. 만약 작업이 없으면 쓰레드는 그냥 대기할 것이다. 하지만 일단 워커가 작업을 큐에서 빼내면 [`work()`](https://github.com/nodejs/node/blob/db1087c9757c31a82c50a1eba368d8cba95b57d0/deps/uv/src/threadpool.c#L95) 메소드를 실행한다. `uv__work_submit()`로 돌아가서 보면 사실 [`work()`](https://github.com/nodejs/node/blob/db1087c9757c31a82c50a1eba368d8cba95b57d0/deps/uv/src/threadpool.c#L186) 메소드는  `uv__fs_work(https://github.com/nodejs/node/blob/db1087c9757c31a82c50a1eba368d8cba95b57d0/deps/uv/src/threadpool.c#L186)`다.

`uv__fs_work()` 내부를 보면 파일시스템 요청 타입을 확인한다. 우리의 경우 [`STAT`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L963) 타입이다. 아래 보이는 것처럼 이건 C의 [`stat(2)`](http://man7.org/linux/man-pages/man2/stat.2.html) 함수를 호출하는 [`uv__fs_sate()`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L841)를 호출한다. 더 이상 저수준 코드를 추적하지는 않겠다. 하지만 이 요청은 C 기본 라이브러리와 운영체제로 간다. [`stat()`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L845-L847) 호출에 성공하면 [uv__to_stat()](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L766)가 호출되는데 결과를 libuv uv_stat_t 자료 구조로 복사하기 위해서다. libuv는 자체 stat 구조를 사용하여 플랫폼간에 보다 일관적인 인터페이스를 제공할 수 있다. [uv__fs_stae()의 결과는 원래 파일 시스템 요청에 덧붙여진다](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L973-L982). 

```c
static int uv__fs_stat(const char *path, uv_stat_t *buf) { 
  struct stat pbuf;
  int ret;
  ret = stat(path, &pbuf); 
  if (ret == 0) 
    uv__to_stat(&pbuf, buf); 
  return ret; 
}
```

## 결과를 가지고 돌아가기 

여기서 `stat()` 작업이 완료되면 그 결과를 자바스크립트 계층으로로 되돌리기 시작한다. 제어 흐름은 [`uv_async_send()`](https://github.com/nodejs/node/blob/db1087c9757c31a82c50a1eba368d8cba95b57d0/deps/uv/src/threadpool.c#L101)가 호출된 쓰레드의 `worker()` 함수로 이동한다. [`uv_async_send()`](https://github.com/nodejs/node/blob/db1087c9757c31a82c50a1eba368d8cba95b57d0/deps/uv/src/unix/async.c#L60)는 노드의 메인 쓰레드에서 돌아가는 이벤트 루프를 깨울 책임이 있다. 이것은 메인 쓰레드가 보고 있는 파일 디스크립터에 대한 쓰기를 통해 [`uv__async_send()`](https://github.com/nodejs/node/blob/db1087c9757c31a82c50a1eba368d8cba95b57d0/deps/uv/src/unix/async.c#L147)에서 완료된다. 

메인 쓰레드에서 [`uv__io_poll()`](https://github.com/nodejs/node/blob/db1087c9757c31a82c50a1eba368d8cba95b57d0/deps/uv/src/unix/kqueue.c#L69)은 작업이 완료됨을 확인하고 IO 이벤트의 콜백을 호출한다. 결과적으로 [비동기 이벤트 콜백](https://github.com/nodejs/node/blob/db1087c9757c31a82c50a1eba368d8cba95b57d0/deps/uv/src/unix/async.c#L143)인 [`uv__async_io()`](https://github.com/nodejs/node/blob/db1087c9757c31a82c50a1eba368d8cba95b57d0/deps/uv/src/unix/async.c#L78)를 호출한다.  이것은 워커의 [`done()`](https://github.com/nodejs/node/blob/db1087c9757c31a82c50a1eba368d8cba95b57d0/deps/uv/src/threadpool.c#L236)을 호출하는 [`uv__work_done()`](https://github.com/nodejs/node/blob/db1087c9757c31a82c50a1eba368d8cba95b57d0/deps/uv/src/threadpool.c#L218)을 호출한다. 사실 done() 메소드는 [`POST`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L118) 매크로와 `uv__work_wubmit()`을 통한  [uv__fs_done() 임](https://github.com/nodejs/node/blob/db1087c9757c31a82c50a1eba368d8cba95b57d0/deps/uv/src/threadpool.c#L187)을 떠올려 봐라. 

[`uv__fs_done()`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L986) 콜백은 [이벤트 루프에서 요청을 제거](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L990)하고 [요청 콜백을 호출](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L997)한다. [`INIT`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L63) 매크로가 [`uv_fs_stat()`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L1274)로 [전달된 요청 콜백 함수를 세팅한다는 것](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L74)을 기억하는가?  이 콜백은 우리를 노드JS 바인딩 레이어로 되돌려준다. [`uv_fs_stat()`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/src/node_file.cc#L345)는 콜백인 [`After()`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/src/node_file.cc#L348)와 함께 호출된다는 것을 기억하자.

`After()` 내부에서 작업에 성공했다고 가정하면 [`uv_fs_stat()` 결과를 포함하는 `uv_stat_t` 구조체는 `BuildStatsObject()` 함수로 전달된다](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/src/node_file.cc#L204-L209). 이것은 `uv_stat_t` 구조체로부터 정보를 포함하는 자바스크립트 객체를 만든다. 이 자바스크립트 객체가 빌드되면 [`MakeCallback()`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/src/node_file.cc#L321)이 호출되고 요청 객체를 정리할 수 있다. [`MakeCallback()`](https://github.com/nodejs/node/blob/833294f681db34dda78276f1f65dc9eb3badcd9e/src/async-wrap.cc#L295)은 `fs.stat()`의 결과를 가지고 자바스크립트 런타임을 다시 호출하는 역할을 한다. 이 시점에서 우리의 자바스크립트 코드는 계속 실행할 수 있게 되는 것이다.

## 결론 

여기까지 노드 자바스크립트 계층으로부터 C++ 바인딩 레이어 그리고 libuv의 쓰레드 풀까지 파일 시스템 요청을 추적해 봤다. 동기와 비동기 호출이 같은 코드 경로를 공유할 수 있는 방법 뿐만 아니라 어떻게 다양한 계층이 상호작용하는지 살펴봤다. 노드의 자바스크립트 계층 아래 세상을 경험해 보지 않았다면 (이러한) 압도감을 느낄수도 없었을 것이다.
