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

## 노드JS 종료, libuv 시작

우리는 노드 C++ 계층에서 떠나 libuv C 코드로 이동한다. libuv는 독립적인 워크 쓰레드 풀로 워크를 떠넘기면서 비동기 파일시스템 작업을 제공할수 있다. 가능한한 플랫폼 독립적으로 유지하기 위해 libuv는 유닉스와 윈도우 양쪽에 같은 기능을 구현해야 한다. 이 글의 목적을 위해 리눅스에서 돌아간다고 가정하고 `uv_fs_stat()`의 적절한 구현으로 넘어가겠다. 만약 윈도우에 환경이라면 [Windows `uv_fs_stat()` 구현](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/win/fs.c#L2252)을 시작하면서 비슷한 방법으로 따라오길 바란다.

비(non) 윈도우에서 아래 코드에 있는 `uv_fs_stat()`는 [`INIT`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L63), [`PATH`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L78), [`POST`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L115) 3개의 매크로로 구성된다. `PATH`는 파일 경로를 설정하고 `INIT`은 파일 시스템 요청를 초기화한 뒤 [콜백을 설정](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L74)한다. `POST` 매크로는 [쓰레드 풀로 요청을 전달](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L118)하거나 [동기 요청일 경우 태스크를 실행](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L122-L123)한다. 우리는 [`uv__work_submit()`](https://github.com/nodejs/node/blob/db1087c9757c31a82c50a1eba368d8cba95b57d0/deps/uv/src/threadpool.c#L180)으로 파일시스템 요청을 전달하는 비동기 코드 경로를 따른다. [`uv__fs_work()`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L923) 함수는 요청을 처리하기 위해 전달 된다. 추가로 [`uv__fs_done()`](https://github.com/nodejs/node/blob/24a3d0e71b46bdf79041eb71e46662c891ab7694/deps/uv/src/unix/fs.c#L986) 함수는 요청이 완료될때 실행되는 콜백으로 전달된다.

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