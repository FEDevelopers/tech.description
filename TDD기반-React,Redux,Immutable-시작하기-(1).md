> http://www.theodo.fr/blog/2016/03/getting-started-with-react-redux-and-immutable-a-test-driven-tutorial-part-1/ 를 번역한 글입니다.


몇 주 전에, 저는 할일 없이 [해커 뉴스](https://news.ycombinator.com/)를 보고, Redux에 관한 헤드라인을 봤습니다. 헤드라인을 읽고 Redux는 React와 잘 어울리는 다른 요소라고 이해를 했었습니다. 하지만 자바스크립트에 대해 이미 [피로도](https://medium.com/@ericclemmons/javascript-fatigue-48d4011b6fc4#.bk12bq4lc)가 쌓여있었기 때문에, 저는 Redux의 다음 특징에 대해 읽기 전까진 거의 신경 쓰지 않고 있었습니다. :

- 함수형 프로그래밍을 수행하고, 어플리케이션의 동작을 예상하고 보장합니다.
- 서버와 클라이언트 간의 코드를 공유하는 동형(*isomorphic*) 어플리케이션을 허락합니다.
- [시간 여행 디버깅](https://code-cartoons.com/hot-reloading-and-time-travel-debugging-what-are-they-3c8ed2812f35#.l69um5b92)(역자주: 시간여행 디버깅은 버그 가 나기 이전 상태로 돌아가서 테스팅을 할 수 있게 해주는 방식)이 가능한가요?

Redux는 마치 React 어플리케이션의 상태를 관리하는 우아한 솔루션인 것 처럼 보였습니다. 누가 시간 여행 디버깅을 반대하겠습니까?
그래저 저는 공식 문서와 [@teropa](https://twitter.com/teropa)의 [환상적인 튜토리얼](http://teropa.info/blog/2015/09/10/full-stack-redux-tutorial.html)을 읽었습니다.( 이 포스팅의 중요한 영감을 얻은 곳입니다.)  
코드는 우아하고, 디버거는 매우 훌륭합니다. 제말은 다음을 봐보세요.

![todo 영상](http://www.theodo.fr/uploads/blog//2016/02/time-travel.gif)

이번 첫번째 파트에서 만들 앱은 Redux가 일하는 방식에 대한 기본적인 원칙에 대한 것입니다. 다소 간결하게 하기 위해 일부러 범위를 제한적으로 두었습니다.(클라이언트측에만 존재라 동형 앱이 아닌 단순한 앱) 만약 당신이 더 깊게 알고 싶다면, 위에 환상적인 튜토리얼을 권합니다. Gihub 저장소는 [여기](https://github.com/phacks/redux-todomvc) 에 있으며, 최종 앱의 모습을 담고 있습니다. 코드나 튜토리얼에 대한 질문이나 제안은 코멘트를 남기거나 Pull Request를 하십시오.  

#앱
이번 튜토리얼의 목적을 위해 우리는 전통적인 `TodoMVC`앱을 만들 것입니다. 이를 위해 다음과 같은 요구사항이 있습니다.

- 각 할일은 활성화 또는 완료 할 수 있습니다.
- 할일은 추가,수정,삭제가 가능합니다.
- 할일들은 상태에 따라 필터링 할 수 있습니다.
- 활성화된 할일 카운팅은 하단에 배치합니다.
- 완료된 할일들은 한꺼번에 삭제가 가능합니다.

당신은 [여기](http://todomvc.com/examples/react/#/) <- 서 앱의 예를 보실 수 있습니다.

#Redux와 Immutable: 함수형 프로그래밍
몇달전, 저는 대쉬보드 웹앱을 개발하고 있었습니다. 웹앱이 커져감에 따라, 점점 더 찾기 힘든 많은 버그들을 발견하게 되었습니다. "이 페이지로 이동해서 버튼을 클릭하고, 다시 메인 페이지로 돌아가서 커피를 잡고, 아까 그 페이지로 이동하고 다시 클릭하면 무언가 기묘한 일이 발생" 과 같은 버그였습니다. 이 모든 버그의 근원은 우리 모든 코드와 로직에 영향을 미쳤습니다. 이 행동은 우리가 알지 못했던 다른 곳에 원치 핞는 영향을 미칠 수 있었습니다.  
  
앱의 모든 상태를 가지고 있는 단일 자료구조인 상태트리야 말로 Redux의 힘입니다. 이 의미는 매순간마다, 사용자에게 보여지는 데이터는 상태 트리 안에 있는 결과이며, 단일 출처를 제공하는 것입니다. 우리 앱의 모든 액션은 상태트리에서 가져와서 해당 수정사항을 반영하고(예를들어 할일을 추가 하는것), 업데이트된 상태 트리를 사용자에게 렌더링 합니다. 애매한 부작용은 없으며, 실수로 수정한 변수 참조는 더이상 없습니다. 이는 관심사의 명확한 분리와 좋은 앱 구조를 만들고, 더 나은 디버깅을 할 수 있게 됩니다.  

[Immutable](https://facebook.github.io/immutable-js/)은 불변 데이터 구조를 조작하거나 생성하게 도와주는 툴이며, 페이스북에서 개발한 라이브러리입니다. 비록 Redux와 함께 사용토록 강제하진 않지만, 객체 수정을 막음으로써 함수적 접근을 하도록 강제 합니다. Immutable을 사용해서 객체를 수정 할땐, 사실 수정된 새로운 객체를 생성하고, 원래의 객체로 유지 하게 합니다.  
다음은 문서에서 발췌한 예제입니다.

```javascript
var map1 = Immutable.Map({a:1, b:2, c:3});
var map2 = map1.set('b', 2);
assert(map1 === map2); // no change
var map3 = map1.set('b', 50);
assert(map1 !== map3); // change
```

`map1`의 값을 수정하면, `map1` 객체 자신은 동일하게 유지하고, 새로운 객체인 똑같은 `map3`이 생성됩니다. Immutable은 우리 앱에 상태트리를 저장하는데 사용될 것이며, 효율적이고 간결하게 조작할 수 있는 간단한 함수들을 제공합니다.

# 프로젝트 세팅
> 주의 사항 : 여기 나온 많은 설정들은 앞서 얘기한 @teropa 튜토리얼에서 영감을 얻었습니다.  
참고 사항
- 이 프로젝트 NodeJS 버전은 >= 4.0.0 을 추천합니다. nvm을 통해서 Node버전을 쉽게 관리 하실 수 있습니다.
- [여기](https://github.com/phacks/redux-todomvc/commit/9e2d23ca16980566d9fcaeebbf198031ec55d42f)에 동료들의 커밋이 있습니다.

이제 프로젝트 세팅 할 차례입니다.

```
mkdir redux-todomvc
cd redux-todomvc
npm init -y
```

프로젝트 디렉토리 구조는 다음과 같습니다.

```
├── dist
│   ├── bundle.js
│   └── index.html
├── node_modules
├── package.json
├── src
├── test
└── webpack.config.js
```

먼저 어플리케이션을 실행할 간단한 HTML 파일을 작성해봅니다.

```
dist/index.html
```
````html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>React TodoMVC</title>
</head>
<body>
  <div id="app"></div>
  <script src="bundle.js"></script>
</body>
</html>
```

모든 패키징이 잘 되었다 라고 알려주기 위한 간단한 스크립트를 작성해보겠습니다.

```
src/index.js
```
```javascript
console.log('Hello World!');
```

**Webpack**을 사용하여 패키징된 `bundle.js` 파일을 빌드 할 것 입니다. **Webpack** 기능의 장점은 속도, 쉬운 구성이고, 그리고 핫로드 즉 웹페이지가 새로고침 되지 않고도, 최신 변경사항으로 리로드 되는 장점 입니다.  
Webpack을 설치해보독 하겠습니다.

```
npm install --save-dev webpack webpack-dev-server
```

이 앱은 ES2015 문법을 사용하며, 새로운 기능과 나이스하게 통합된 문법 설탕을 사용합니다. 만약 당신이 ES2015에 대해 더 알고 싶다면, 이 [요약본](https://github.com/DrkSephy/es6-cheatsheet)이 좋은 자료가 될 것 입니다.  
Babel은 ES2015 구문을 common JS 로 변환해줍니다.

```javascript
npm install --save-dev babel-core babel-loader babel-preset-es2015
```

또한 우리는 React 컴포넌트를 작성하기 위해 JSX 문법을 사용 할 것입니다. 그러므로 *Babel React* 패키지를 설치해봅시다.

```javascript
npm install --save-dev babel-preset-react
```

여기서는 우리 소스파일을 빌드 하기 위해 webpack을 설정합니다.

```
package.json
```
```javascript
"babel": {
  "presets": ["es2015", "react"]
}
```  

```
webpack.config.js
```
```javascript
module.exports = {
  entry: [
    './src/index.js'
  ],
  module: {
    loaders: [{
      test: /\.jsx?$/,
      exclude: /node_modules/,
      loader: 'babel'
    }]
  },
  resolve: {
    extensions: ['', '.js', '.jsx']
  },
  output: {
    path: __dirname + '/dist',
    publicPath: '/',
    filename: 'bundle.js'
  },
  devServer: {
    contentBase: './dist'
  }
};
```

이제 React와 React Hot Loader를 프로젝트에 추가해봅시다.

```
npm install --save react react-dom
npm install --save-dev react-hot-loader
```

핫 리로딩을 사용하려면, webpack 설정파일을 조금 수정 해야 합니다.:

```
webpack.config.js
```
```javascript
var webpack = require('webpack'); // Requiring the webpack lib

module.exports = {
  entry: [
    'webpack-dev-server/client?http://localhost:8080', // Setting the URL for the hot reload
    'webpack/hot/only-dev-server', // Reload only the dev server
    './src/index.js'
  ],
  module: {
    loaders: [{
      test: /\.jsx?$/,
      exclude: /node_modules/,
      loader: 'react-hot!babel' // Include the react-hot loader
    }]
  },
  resolve: {
    extensions: ['', '.js', '.jsx']
  },
  output: {
    path: __dirname + '/dist',
    publicPath: '/',
    filename: 'bundle.js'
  },
  devServer: {
    contentBase: './dist',
    hot: true // Activate hot loading
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin() // Wire in the hot loading plugin
  ]
};
```

##유닛 테스팅 프레임워크 세팅
우리는 테스트 프레임워크로 Mocah와 Chai를 사용할 것입니다. Mocah와 Chai는 폭넓게 사용되고 있으며, 그것 들의 산출물은(예상결과와 실제 결과 차이 비교) 테스트-주도-개발 하기엔 최고입니다. Chai-Immutable은 불변 자료 구조를 처리 하는 chai 플러그인 입니다.

```
npm install --save immutable
npm install --save-dev mocha chai chai-immutable
```

우리의 경우엔 Karma같은 브라우저 기반 테스크 러너에 의존하지 않습니다. 대신에 jsdom 라이브러리는 순수 자바스크립트로 가짜(mock) DOM을 설정하고, 테스트를 더 빠르게 수행 할 수 있게 해줍니다.

```
npm install --save-dev jsdom
```

