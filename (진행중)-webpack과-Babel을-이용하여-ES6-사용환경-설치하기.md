**[원본링크가기](https://leanpub.com/setting-up-es6/read#sec_webpack-babel)**

#3.3 webpack과 Babel을 이용하여 ES6 사용환경 설치하기

해당 파트는 client-side ES6를 아래 열거된 기술을 이용하여 작동시키는 방법을 알아봅니다

- webpack : client-side 모듈 빌더이자 모듈 로더
- npm : 패키지 매니저
- Babel : ES6를 ES5로 변환시켜주는 트랜스파일러~코드변환기~ (우리는 ES6를 사용합니다)

## 3.3.1 webpack 설명
webpack의 특성 중 우리가 살펴볼만한 부분은 다음과 같습니다
- 지원하는 모듈 포맷: AMD, CommonJS
- 지원하는 패키지 매니저: Bower, npm
- non-code를 위한 로더: CSS, templates, ...
- 온디맨드~on demand~ 로딩(chunked transfer)
- 자동으로 브라우저를 새로고침~refresh~해주는 **hot module reloading** 를 지원하는 자체 개발 서버 지원 (개발할때 편리합니다)
- **Dead code elimination** (tree shaking 이라고도 알려져 있습니다)


(진행중)
