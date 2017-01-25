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

