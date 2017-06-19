> http://www.theodo.fr/blog/2016/03/getting-started-with-react-redux-and-immutable-a-test-driven-tutorial-part-1/ 를 번역한 글입니다.


몇 주 전, 저는 할일 없이 [해커 뉴스](https://news.ycombinator.com/)를 보다가, Redux에 관한 헤드라인을 봤습니다. 헤드라인만 읽고 Redux는 React와 잘 어울리는 다른 요소라고 이해를 했었습니다. 저는 자바스크립트에 대해 이미 [피로도](https://medium.com/@ericclemmons/javascript-fatigue-48d4011b6fc4#.bk12bq4lc)가 쌓여있었기 때문에, Redux의 다음 특징에 대해 읽기 전까진 거의 신경 쓰지 않고 있었습니다. :

- 함수형 프로그래밍을 수행하고, 어플리케이션의 동작을 예상하고 보장합니다.
- 서버와 클라이언트 간의 코드를 공유하는 동형(*isomorphic*) 어플리케이션을 허락합니다.
- [시간 여행 디버깅](https://code-cartoons.com/hot-reloading-and-time-travel-debugging-what-are-they-3c8ed2812f35#.l69um5b92)(역자주: 시간여행 디버깅은 버그 가 나기 이전 상태로 돌아가서 테스팅을 할 수 있게 해주는 방식)의 가능성

Redux는 마치 React 어플리케이션의 상태를 관리하는 우아한 솔루션인 것 처럼 보였습니다. 누가 시간 여행 디버깅을 반대하겠습니까?
그래저 저는 공식 문서와 [@teropa](https://twitter.com/teropa)의 [환상적인 튜토리얼](http://teropa.info/blog/2015/09/10/full-stack-redux-tutorial.html)을 읽었습니다.( 이 포스팅의 중요한 영감을 얻은 곳입니다.)  
코드는 우아하고, 디버거는 매우 훌륭했습니다.

![todo 영상](http://www.theodo.fr/uploads/blog//2016/02/time-travel.gif)

이번 첫번째 파트에서 만들 앱은 Redux가 일하는 방식에 대한 기본적인 원칙에 관한 것입니다. 조금 간결하려고 일부러 범위를 제한하였습니다.(클라이언트측에만 있어서 동형 앱이 아닌 단순한 앱) 만약 당신이 더 깊게 알고 싶다면, 위에 환상적인 튜토리얼을 권합니다. Gihub 저장소는 [여기](https://github.com/phacks/redux-todomvc) 에 있으며, 최종 앱의 모습을 담고 있습니다. 코드나 튜토리얼에 대한 질문이나 제안은 코멘트를 남기거나 Pull Request를 하십시오.  

# 앱
이번 튜토리얼의 목적을 위해 우리는 전통적인 `TodoMVC`앱을 만들 것입니다. 이를 위해 다음과 같은 요구사항이 있습니다.

- 각 할일은 활성화 또는 완료 할 수 있습니다.
- 할일은 추가,수정,삭제가 가능합니다.
- 할일들은 상태에 따라 필터링 할 수 있습니다.
- 활성화된 할일 카운팅은 하단에 배치합니다.
- 완료된 할일들은 한꺼번에 삭제가 가능합니다.

당신은 [여기](http://todomvc.com/examples/react/#/)서 앱의 실제 예를 보실 수 있습니다.

# Redux와 Immutable: 함수형 프로그래밍
몇달전, 저는 대쉬보드 웹앱을 개발하고 있었습니다. 웹앱이 커져감에 따라, 점점 더 찾기 힘든 많은 버그들을 발견하게 되었습니다. "이 페이지로 이동해서 버튼을 클릭하고, 다시 메인 페이지로 돌아가서 잠깐 커피를 들고나서, 아까 그 페이지로 이동하고 다시 클릭하면 무언가 기묘한 일이 발생" 과 같은 버그였습니다. 이 버그는 우리의 모든 코드와 로직에 영향을 미치고, 우리가 알지 못했던 다른 곳에 원치 않는 영향을 미칠 수 있었습니다.  
  
앱의 모든 상태를 가지고 있는 단일 자료구조인 상태트리야 말로 Redux의 힘입니다. 이 의미는 매순간마다, 사용자에게 보여지는 데이터는 상태 트리 안에 있는 결과이며, 단일 출처를 제공하는 것입니다. 우리 앱의 모든 액션은 상태트리에서 가져와서 해당 수정사항을 반영하고(예를들어 할일을 추가 하는것), 업데이트된 상태 트리를 사용자에게 렌더링 합니다. 애매한 부작용은 없으며, 실수로 수정해버린 변수 참조 같은 일은 더이상 없을 것입니다. 이는 관심사의 명확한 분리와 좋은 앱 구조를 만들고, 더 나은 디버깅을 할 수 있게 됩니다.  

[Immutable](https://facebook.github.io/immutable-js/)은 불변 데이터 구조를 조작하거나 생성하게 도와주는 툴이며, 페이스북에서 개발한 라이브러리입니다. 비록 Redux와 함께 사용토록 강제하진 않지만, 객체 수정을 막음으로써 함수적 접근을 하도록 유도 합니다. Immutable을 사용해서 객체를 수정 하는 건 사실 수정된 새로운 객체를 생성하고 원래의 객체로 유지 하게 하는 것입니다.  
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
- [여기](https://github.com/phacks/redux-todomvc/commit/9e2d23ca16980566d9fcaeebbf198031ec55d42f)에 동료의 커밋이 있습니다.

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

```html
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

모든 패키징이 잘 됬는지 알기 위한 간단한 스크립트를 작성해보겠습니다.

```
src/index.js
```

```javascript
console.log('Hello World!');
```

**Webpack**을 사용하여 패키징된 `bundle.js` 파일을 빌드 할 것 입니다. **Webpack** 기능의 장점은 속도, 쉬운 구성이고, 그리고 핫로드 즉 웹페이지가 새로고침 되지 않고도, 최신 변경사항으로 리로드 되는 것이 장점 입니다.  
Webpack을 설치해보도록 하겠습니다.

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

소스파일을 빌드 하기 위해 webpack을 설정합니다.

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
var webpack = require('webpack'); // webpack 라이브러리 로드

module.exports = {
  entry: [
    'webpack-dev-server/client?http://localhost:8080', // 핫리로드 위한 URL 세팅
    'webpack/hot/only-dev-server', // 오로지 dev서버에서만 작동
    './src/index.js'
  ],
  module: {
    loaders: [{
      test: /\.jsx?$/,
      exclude: /node_modules/,
      loader: 'react-hot!babel' // react-hot loader 추가
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
    hot: true // 핫리로드 활성화
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin() // 핫리로드 플러그인 추가
  ]
};
```

## 유닛 테스팅 프레임워크 세팅
우리는 테스트 프레임워크로 Mocha와 Chai를 사용할 것입니다. Mocha와 Chai는 폭넓게 사용되고 있으며, 그것 들의 산출물은(예상결과와 실제 결과 차이 비교) 테스트-주도-개발 하기엔 최고입니다. Chai-Immutable은 불변 자료 구조를 처리 하는 chai 플러그인 입니다.

```
npm install --save immutable
npm install --save-dev mocha chai chai-immutable
```

우리의 경우엔 Karma같은 브라우저 기반 테스크 러너에 의존하지 않습니다. 대신에 jsdom 라이브러리는 순수 자바스크립트로 가짜(mock) DOM을 설정하고, 테스트를 더 빠르게 수행 할 수 있게 해줍니다.

```
npm install --save-dev jsdom
```

또한 우리는 다음 아래 항목을 처리하는 테스트용 bootstrapping 스크립트를 작성해야 합니다.:

- 브라우저가 일반적으로 제공하는 가짜(Mock) `document` 와 `window` 객체
- *chai-immutable* 패키지와 함꼐 불변 자료 구조를 사용한다고 *chai* 에게 알림

```
test/setup.js
```

``` javascript
import jsdom from 'jsdom';
import chai from 'chai';
import chaiImmutable from 'chai-immutable';

const doc = jsdom.jsdom('<!doctype html><html><body></body></html>');
const win = doc.defaultView;

global.document = doc;
global.window = win;

Object.keys(window).forEach( (key) => {
  if (!(key in global)) {
    global[key] = window[key];
  }
});

chai.use(chaiImmutable);
```

우리 `package.json` 설정을 고려하여 `npm test script`를 수정해봅시다.

```
pacakge.json
```

```
"scripts": {
  "test": "mocha --compilers js:babel-core/register --require ./test/setup.js 'test/**/*.@(js|jsx)'",
  "test:watch": "npm run test -- --watch --watch-extensions jsx"
},
```

수정: `npm run test:watch` 명령어가 `Windows` 에서는 작동하지 않는 것 같다면, GitHub 저장소 [이슈](https://github.com/phacks/redux-todomvc/issues/1)를 참고 하시기 바랍니다.    

지금 만약 `npm run test:watch` 가 실행되면, test 디렉토리에 있는 모든 `.js` 또는 `.jsx` 파일이 우리의 소스 파일들이 수정될 때마다 `mocha` 테스팅이 실행 될 것입니다.  
이제 설정이 완료됬습니다.: 터미널에서 `npm run test:watch`나  `webpack-dev-server` 를 실행하고, 브라우저에서 `localhost:8080/`으로 이동하면 콘솔창에 `Hello World!`가 나타나게 됩니다.

# 상태 트리 구성
앞서 말했듯이, 상태 트리는 우리 어플리케이션(상태)을 포함하여 모든 정보를 가진 자료 구조입니다. 우리는 실제로 앱을 개발하기 전에 이 자료 구조에 대해 잘 생각해야 합니다. 왜냐하면 많은 코드 구조와 상호작용 하기 때문입니다.  
아래 그림을 보면, 우리 앱은 **todo** 리스트에 여러 아이템들로 구성되어 있습니다.

![todo list item](http://www.theodo.fr/uploads/blog//2016/02/state_tree1.png)

각 아이템들은 텍스트와 쉽게 수정하기 위한 아이디(id)를 가지고 있습니다. 또한, 각 아이템은 2개의 상태(활성,완료) 중 하나를 가질 수 있습니다. 마지막으로 아이템이 편집 상태(사용자가 텍스트를 수정하길 원할 때)일 수 있습니다. 그래서 우리는 각 상태를 지속적으로 추적 해야 합니다.:

![item status](http://www.theodo.fr/uploads/blog//2016/02/state_tree2.png)

그리고 각 아이템들의 상태에 따라 아이템들을 필터링 할 수 있습니다. 그래서 우리는 `filter` 속성을 상태 트리에 추가 해야 합니다. 아래는 최종 상태트리 입니다.

![status tree](http://www.theodo.fr/uploads/blog//2016/02/state_tree3.png)

# 앱 UI 작성

![UI](http://www.theodo.fr/uploads/blog//2016/02/todo-app-structure.png)

우선, 컴포넌트별로 앱을 나눌 것 입니다.

- 새로운 *todos* 를 입력하는 *TodoHeader* 컴포넌트
- *todos* 리스트인 *TodoList* 컴포넌트
- 1개 *todo* 인 *TodoItem* 컴포넌트
- *todo* 를 수정하는 *TextInput* 컴포넌트
- 활성 카운터를 표시하고, 필터, "완료 지우기" 버튼 인 *TodoTools* 컴포넌트
- 로직이 없는, footer 정보를 표시하는 *Footer* 컴포넌트

마지막으로 위 컴포넌트들을 모두 가지고 있는 *TodoApp* 컴포넌트를 생성할 것입니다.

# 첫번째 컴포넌트 bootstrapping
> 참고 : [여기](https://github.com/phacks/redux-todomvc/commit/d1d2a56a8d2b4f898ed8fdf20f55e7f7f11ad6ad)에 동료의 관련 커밋 저장소가 있습니다.

우리가 하나의 *TodoApp* 컴포넌트에 위에 작성한 모든 컴포넌트들을 넣을 것 입니다. *TodoApp* 컴포넌트를 index.html 안에 `#app div`에 작성하여 시작해봅시다.:

```
src/index.jsx
```
```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import {List, Map} from 'immutable';

import TodoApp from './components/TodoApp';

const todos = List.of(
  Map({id: 1, text: 'React', status: 'active', editing: false}),
  Map({id: 2, text: 'Redux', status: 'active', editing: false}),
  Map({id: 3, text: 'immutable', status: 'completed', editing: false})
);

ReactDOM.render(
  <TodoApp todos={todos} />,
  document.getElementById('app')
);
```

우리는 위에서 src/index.js 파일에 `JSX` 문법을 사용했기 때문에, 확장자를 `.jsx`로 변경 해야하고 webpack 설정파일에서도 바꿔줘야 합니다.

```
webpack.config.js
```
```javascript
enrty: [
  'webpack-dev-server/client?http://localhost:8080',
  'webpack/hot/only-dev-server',
  './src/index.jsx' // index file 확장자를 jsx로 변경
],
```

# todo 리스트 UI작성
이제 *todo* 아이템들 리스트를 표시하는 `TodoApp` 컴포넌트의 첫번째 버전을 작성할 것 입니다.

```
src/components/TodoApp.jsx
```
```javascript
import React from  'react';

export default class TodoApp extends React.Component{
  getItems(){
    return this.props.todos || [];
  }

  render(){
    return <div>
      <section className="todoapp">
        <section className="main">
          <ul className="todo-list">
            {this.getItems().map(item =>
              <li className="active" key={item.get('text')}>
                <div className="view">
                  <input type="checkbox"
                          className="toggle"/>
                  <label htmlFor="todo">
                    {item.get('text')}
                  </label>
                  <button className="destroy"></button>
                </div>
              </li>
            )}
          </ul>
        </section>
      </section>
    </div>
  }
};
```

두가지가 갑자기 생각이 납니다.  

첫번째, 브라우저에서 위 결과를 본다면, 그다지 매력적이지 않을 것 같습니다. 이를 위해, [tobomvc-app-css](https://github.com/tastejs/todomvc-app-css) 패키지를 사용해서, 조금 더 재미있게 만드는데 필요한 모든 스타일 속성을 제공 할 것입니다.

```
npm install --save todomvc-app-css
npm install style-loader css-loader --save-dev
```

그리고 webpack에  css 스타일시트를 로드한다고 알려줍니다.:

```
webpack.config.js
```
```javascript
module: {
  loaders: [{
    test: /\.jsx?$/,
    exclude: /node_modules/,
    loader: 'react-hot!babel'
  }, {
      test: /\.css$/,
      loader: 'style!css' // css loader를 추가
  }]
},
```

이제 `index.jsx` 파일에 style을 포함시킵니다.

```
src/index.jsx
```
```javascript
// .... 위와동일
require('../node_modules/todomvc-app-css/index.css');

ReactDOM.render(
  <TodoApp todos={todos} />,
  document.getElementById('app')
);
```

두번째로, 코드가 복잡해 보이는 것 같습니다. 그래서 모든 아이템을 리스팅하고 단일 아이템을 표현하는데 사용되는 `TodoList`와 `TodoItem` 컴포넌트를 생성할 것 입니다.

> 참고 : [여기](https://github.com/phacks/redux-todomvc/commit/90fe2cc5f8e1c20546c702b91230369c896b9b81)에 동료의 관련 커밋 저장소가 있습니다.

```
src/components/TodoApp.jsx
```
```javascript
import React from 'react';
import TodoList from './TodoList';

export default class TodoApp extends React.Component{
  render(){
    return <div>
      <section className="todoapp">
        <TodoList todos={this.props.todos} />
      </section>
    </div>
  }
};
```

`TodoList` 컴포넌트는 `props`통해 받은 각 아이템을 위한 `TodoItem` 컴포넌트를 표시합니다.

```
src/components/TodoList.jsx
```
```javascript
import React from 'react';
import TodoItem from './TodoItem';

export default class TodoList extends React.Component{
  render(){
    return <section className="main">
      <ul className="todo-list">
        {this.props.todos.map(item=>
          <TodoItem key={item.get('text')}
                    text={item.get('text')} />
        )}
      </ul>
    </section>
  }
}
```

```
src/components/TodoItem.jsx
```
```javascript
import React from 'react';

export default class TodoItem extends React.Component{
  render(){
    return <li className="todo">
      <div className="view">
        <input type="checkbox"
              className="toggle" />
        <label htmlFor="todo">
          {this.props.text}
        </label>
      </div>
    </li>
  }
}
```

사용자 액션과 어떻게 앱에서 이것 들을 통합 할지 방법을 생각 해보기전에, 일단 `TodoItem` 컴포넌트에 아이템을 수정할 수있게 해주는 `input` 을 추가 해보겠습니다.

```
src/components/TodoItem.jsx
```
```javascript
import React from 'react';
import TextInput from './TextInput';

export default class TodoItem extends React.Component{
  render(){
    return <li className="todo">
      <div className="view">
        <input type="checkbox"
               className="toggle" />
        <label htmlFor="todo">
          {this.props.text}
        </label>
        <button className="destroy"></button>
      </div>
      <TextInput /> // TextInput 컴포넌트 추가
    </li>
  }
};
```

`TextInput` 컴포넌트는 다음과 같이 작성합니다.

```
src/components/TextInput.js
```
```javascript
import React from 'react';

export default class TextInput extends React.Component {
  render() {
    return <input className="edit"
                  autoFocus={true}
                  type="text" />
  }
};
```

# 리스트 컴포넌트에서 사용자 액션 처리
이제 리스트 컴포넌트의 UI가 설정되었습니다. 그러나 아직 우리는 사용자 액션과 그것에 따른 앱이 어떻게 응답하는지에 대해 고려하지 않았습니다.

## props의 힘
React에서 `props` 객체는 컨테이너가 인스턴스화 될 때 설정 속성에 의해 전달됩니다.  
예를 들어, 만약 `TodoItem` 을 아래와 같이 인스턴스화 하면:

```javascript
<TodoItem text={'Text of item'} />
```

우리는 `this.props.text` 변수로 `TodoItem` 컴포넌트에 접근 할 수 있습니다.

```javascript
// TodoItem.jsx 에서
console.log(this.props.text);
// 결과  : 'Text of item'
```

Redux 아키텍쳐는 집중적으로 `props`를 사용하게끔 만들어 졌습니다. 기본 원칙은 모든 요소의 상태는 그 `props` 에만 존재하야 합니다. 다르게 말하면: 같은 `props` 집합에 대해 두 인스턴스가 정확하게 동일한 결과를 출력해야 합니다. 전에 봤듯이, 앱의 전체 상태는 상태트리에 포함 되어 있습니다. 이는 `props`로 컴포넌트에 전달된 상태트리가 앱의 시각적인 출력을 전적으로 예상하는 방향으로 결정한다는 걸 의미합니다.

## TodoList 컴포넌트
> 참고 : [여기](https://github.com/phacks/redux-todomvc/commit/69707f07b6e9cbca7558cb85fcabff54615c1737)에 동료의 관련 커밋이 있습니다.

이번 섹션에서 우리는 테스트-우선 접근법으로 따라갈 것입니다.  
React 라이브러리는 우리 컴포넌트 테스트를 도와주기 위하여, 아래와 같은 메소드를 제공하는 *TestUtils* 애드온을 제공합니다.

- `renderIntoDocument`, 분리된 DOM 노트에 컴포넌트를 렌더링합니다.
- `scryRenderedDOMComponentsWithTag`, 제공하는 태그(li, input..)로 DOM에 모든 컴포넌트 인스턴스를 찾습니다.
- `scryRenderedDOMComponentsWithClass`, 제공되는 class로 DOM에 모든 컴포넌트 인스턴스를 찾습니다.
- `Simulate`, 사용자 액션을 시뮬레이션합니다.(클릭, 키 입력, 텍스트 입력…)

`react` 패키지에는 `TestUtils` 애드온이 포함되어져 있지 않으므로, 개별적으로 설치해야 합니다.

```
npm install --save-dev react-addons-test-utils
```

우리의 첫번째 테스트로 `filter` props 가 `active`인 상태에서 모든 `active` 아이템들을 리스팅 하는 `TodoList` 컴포넌트를 테스트 할 것 입니다.

```
test/components/TodoList_spec.jsx
```
```javascript
import React from 'react';
import TestUtils from 'react-addons-test-utils';
import TodoList from '../../src/components/TodoList';
import {expect} from 'chai';
import {List, Map} from 'immutable';

const {renderIntoDocument,
      scryRenderedDOMComponentsWithTag} = TestUtils;

describe('TodoList', ()=>{
  it('renders a list with only the active items if the filter is active',() => {
    const todos = List.of(
      Map({id: 1, text: 'React', status: 'active'}),
      Map({id: 2, text: 'Redux', status: 'active'}),
      Map({id: 3, text: 'Immutable', status: 'completed'})
    );

    const filter = 'active';
    const component = renderIntoDocument(
      <TodoList filter={filter} todos={todos} />
    );
    const items = scryRenderedDOMComponentsWithTag(component, 'li');

    expect(items.length).to.equal(2);
    expect(items[0].textContent).to.contain('React');
    expect(items[1].textContent).to.contain('Redux');
  });
});
```

 첫번째 테스트는 실패 하게됩니다. 우리는 테스트에서 표시하는 2가지 활성화된 아이템 말고, 3가지 아이템이 있습니다. 실제로 테스트는 아이템을 필터링 하는 로직을 작성하지 않았으므로, 테스트 실패는 정상입니다.

```
src/components/TodoList.jsx
```
```javascript
// ...
export default class TodoList extends React.Component {
  // 각 상태에 따라 아이템 필터링
  getItems() {
    if (this.props.todos) {
      return this.props.todos.filter(
        (item) => item.get('status') === this.props.filter
      );
    }
    return [];
  }
  render() {
    return <section className="main">
      <ul className="todo-list">
        // 필터링된 아이템만 표시
        {this.getItems().map(item =>
          <TodoItem key={item.get('text')}
                    text={item.get('text')} />
        )}
      </ul>
    </section>
  }
};
```

이제 첫번째 테스트는 통과하였습니다. 여기서 멈추지 말고, `all`과 `completed` 필터를 추가하여 테스트 해봅시다.

```
test/components/TodoList_spec.js
```
```javascript
// ...
describe('TodoList', () => {
  // ...

  it('renders a list with only completed items if the filter is completed', () => {
    const todos = List.of(
      Map({id: 1, text: 'React', status: 'active'}),
      Map({id: 2, text: 'Redux', status: 'active'}),
      Map({id: 3, text: 'Immutable', status: 'completed'})
    );
    const filter = 'completed';
    const component = renderIntoDocument(
      <TodoList filter={filter} todos={todos} />
    );
    const items = scryRenderedDOMComponentsWithTag(component, 'li');

    expect(items.length).to.equal(1);
    expect(items[0].textContent).to.contain('Immutable');
  });

  it('renders a list with all the items', () => {
    const todos = List.of(
      Map({id: 1, text: 'React', status: 'active'}),
      Map({id: 2, text: 'Redux', status: 'active'}),
      Map({id: 3, text: 'Immutable', status: 'completed'})
    );
    const filter = 'all';
    const component = renderIntoDocument(
      <TodoList filter={filter} todos={todos} />
    );
    const items = scryRenderedDOMComponentsWithTag(component, 'li');

    expect(items.length).to.equal(3);
    expect(items[0].textContent).to.contain('React');
    expect(items[1].textContent).to.contain('Redux');
    expect(items[2].textContent).to.contain('Immutable');
  });
});
```

위 3번째 테스트는 실패합니다. `all` 필터를 위한 로직이 약간 다르기 때문에, 컴포넌트 로직을 수정 해보도록 하겠습니다.

```
src/components/TodoList.jsx
```
```javascript
// ...
export default React.Component {
  // 각 상태에 따른 필터링
  getItems() {
    if (this.props.todos) {
      return this.props.todos.filter(
        (item) => this.props.filter === 'all' || item.get('status') === this.props.filter
      );
    }
    return [];
  }
  // ...
});
```

이 시점에서 필터 속성에 의해 앱에 표시되는 리스트가 필터링된다는 것을 알게 되었습니다. 브라우저에서 앱을 보면, 우리가 아직 설정하지 않은 아이템들은 표시되지 않는 것을 볼수 있습니다.

```
src/index.jsx
```
```javascript
// ...
const todos = List.of(
  Map({id: 1, text: 'React', status: 'active', editing: false}),
  Map({id: 2, text: 'Redux', status: 'active', editing: false}),
  Map({id: 3, text: 'Immutable', status: 'completed', editing: false})
);
const filter = 'all';

require('../node_modules/todomvc-app-css/index.css');

ReactDOM.render(
  <TodoList todos={todos} filter={filter} />
  document.getElementById('app')
);
```
```
src/components/TodoApp.jsx
```
```javascript
// ...
export default class TodoApp extends React.Component{
  render(){
    return <div>
      <section className="todoapp">
        // TodoList 컴포넌트로 filter props를 전달
        <TodoList todos={this.props.todos} filter={this.props.filter} />
      </section>
    </div>
  }
}
```

이제 아이템들이 다시 나타나고, `index.jsx`에 선언한 필터 상태로 아이템들이 필터링 됩니다.

## TodoItem 컴포넌트
> 참고 : [여기](https://github.com/phacks/redux-todomvc/commit/71d2835620f4ba6f3fc3665327f13ec4fba62eee) 동료의 관련 커밋이 있습니다.

이제 `TodoItem` 컴포넌트를 다뤄보겠습니다. 첫번째로, 우리는 `TodoItem` 컴포넌트가 실제로 아이템을 렌더링 하는지 확인 하겠습니다. 그리고 아직 완료되지 않은 기능을 테스트 하겠습니다.

```
test/components/TodoItem_spec.js
```
```javascript
import React from 'react';
import TestUtils from 'react-addons-test-utils';
import TodoItem from '../../src/components/TodoItem';
import {expect} from 'chai';

const {renderIntoDocument,
      scryRenderedDOMComponentsWithTag} = TestUtils;

describe('TodoItem' , ()=>{
  it('renders an item', ()=>{
    const text = 'React';
    const component = renderIntoDocument(
      <TodoItem text={text} />
    );
    const todo = scryRenderedDOMComponentsWithTag(component, 'li');

    expect(todo.length).to.equal(1);
    expect(todo[0].textContent).to.contain('React');
  });

  it('strikes through the item if it is completed', ()=>{
    const text = 'React';
    const component = renderIntoDocument(
      <TodoItem text={text} isCompleted={true} />
    );
    const todo = scryRenderedDOMComponentsWithTag(component, 'li');

    expect(todo[0].classList.contains('completed')).to.equal(true);
  });
});
```

테스트를 통과하기 위하여, 우리는 `props` 에 전달되는 상태가 `completed` 로 설정되면, class 에 `completed` 를 추가 해야 합니다. DOM class들을 조작하는데 조금 복잡니, `classnames` 패키지를 사용하겠습니다.

```
npm install --save classnames
```
```javascript
import React from 'react';
import classnames from 'classnames';
import TextInput from './TextInput';

export default class TodoItem extends React.Component{
  render(){
    var itemClass = classnames({
      'todo' : true,
      'completed' : this.props.isCompleted
    });
    return <li classnames={itemClass}>
      // ...
    </li>
  }
};
```

또한 아이템은 `isEditing` props로 캡슐화된 것을 수정할 때, 특정한 모양을 가져야 합니다.

```
test/components/TodoItem_spec.js
```
```javascript
// ..
describe('TodoItem', ()=>{
  // ...
  it('should look different when editing', ()=>{
    const text = 'React';
    const component = renderIntoDocument(
      <TodoItem text={text} isEditing={true} />
    );
    const todo = scryRenderedDOMComponentsWithTag(component, 'li');

    expect(todo[0].classList.contains('editing')).to.equal(true);
  });
});
```

테스트를 통과하기 위하여, 우리는 `itemObject` 객체를 수정해야 합니다.

```
src/components/TodoItem.jsx
```
```javascript
// ...
export default class TodoItem extends React.Component{
  render(){
    var itemClass = classnames({
      'todo' : true,
      'completed' : this.props.isCompleted,
      'editing' : this.props.isEditing
    });
    return <li className={itemClass}>
      // ..
    </li>
  }
};
```


만약에 아이템이 완료 상태가 되면, 아이템 왼쪽에 있는 체크박스는 체크되야 합니다.

```
test/components/TodoItem_spec.js
```
```javascript
// ...
describe('TodoItem', ()=>{
  // ..
  it('should be checked if the item is completed', () =>{
    const text = 'React';
    const text2 = 'Redux';
    const component = renderIntoDocument(
      <TodoItem text={text} isCompleted={true} />
      <TodoItem text={text2} isCompleted={false} />
    );

    const input = scryRenderedDOMComponentsWithTag(component, 'input');
    expect(input[0].checked).to.equal(true);
    expect(input[1].checked).to.equal(false);
  });
});
```

React는 input 체크 박스를 상태를 설정 하는 메소드가 있습니다. : `defaultChecked`

```
src/components/TodoItem.jsx
```
```javascript
// ..
export default class TodoItem extends React.Component{
  render(){
    // ...
    return <li className={itemClass}>
      <div className="view">
        <input type="checkbox"
              className="toggle"
              defaultChecked={this.props.isCompleted} />
        // ...
    </li>
  }
};
```

또한 `TodoList` 컴포넌트에서 `isCompleted` 와 `isEditing` props 를 전달 해야합니다.

```
src/components/TodoList.jsx
```
```javascript
// ..
export default class TodoList extends React.Component{
  // ..
  // 아이템이 체크 됬는지 확인 하는 함수
  isCompleted(item){
    reurn item.get('status') === 'completed';
  }

  render(){
    return <section className="main">
      <ul className="todo-list">
        {this.getItems().map( item=>
          <TodoItem key={item.get('text')}
                    text={item.get('text')}
                    // 완료와 수정에 관한 정보를 전달합니다.
                    isCompleted={this.isCompleted(item)}
                    isEditing={item.get('editing')} />
        )}
      </ul>
    </section>
  }
};
```

현재 우리는 컴포넌트에 앱의 상태를 반영 할 수 있습니다. : 예를들어, 완료 된 아이템은 줄이 그어지며, 웹앱은 버튼 클릭과 같은 사용자 액션도 처리해야 합니다. Redux 모델에서는 이 작업을 props를 이용하고, 특히나 props 로 콜백을 전달하여 처리합니다.  
이럼으로써 우리는 앱 로직으로부터 UI를 분리 시키게 됩니다. : 컴포넌트는 특정한 액션이 클릭으로부터 왔다는 것을 알 필요 없습니다. 그저 클릭은 트리거 될 뿐입니다.  
이 원리를 설명하기 위해, 사용자가 삭제 버튼을 클릭하면, `deleteItem` 이 호출되는지 테스트 할 것 입니다.

> 참고 : [여기](https://github.com/phacks/redux-todomvc/commit/b3a6851e8a9f65f1c44e66046bedd1db18c19a48) 동료의 커밋 관련 저장소입니다.

```
test/components/TodoItem_spec.jsx
```
```javascript
// ..
// Simulate 헬퍼로 사용자 클릭 시뮬레이션 합니다.
const {
  renderIntoDocument,
  scryRenderedDOMComponentsWithTag,
  Simulate
} = TestUtils;

describe('TodoItem' , ()=>{
  // ...
  it('invokes callback when the delete button is clicked', () => {
    const text = 'React';
    var deleted = false;
    // 가짜 deleteItem 함수 정의
    const deleteItem = () => deleted = true;
    const component = renderIntoDocument(
      <TodoItem text={text} deleteItem={deleteItem} />
    );
    const buttons = scryRenderedDOMComponentsWithTag(component, 'button');
    Simulate.click(buttons[0]);
    // deleteItem 함수 호출 검증
    expect(deleted).to.equal(true);
  });
});
```

위 테스트를 통과하기 위하여, props에서 전달된 `deleteItem` 함수가 호출되게 해주는 `delete` 버튼 `onClick` 핸들러를 선언 해야 합니다.

```
src/components/TodoItem.jsx
```
```javascript
// ..
export default class TodoItem extends React.Component{
  render(){
    // ...
    return <li className={itemClass}>
      <div className="view">
        // ...
        // props에서 전달한 deleteItem 함수를 호출
        <button className="destroy"
                onClick={()=>this.props.deleteItem(this.props.id)}></button>
      </div>
      <TextInput />
    </li>
  };
}
```

아직 아이템을 삭제하기 위한 실제 로직은 구현하지 않았습니다. 이 부분은 Redux의 역할이 될 것 입니다.  
동일한 모델에서 우리는 다음과 같은 기능을 구현하고 테스트 할 수 있습니다.

- 체크박스 클릭은 `toggleComplete`콜백을 호출
- 아이템 라벨 더블클릭은 `editItem` 콜백 호출

```
test/components/TodoItem_spec.js
```
```javascript
// ...
describe('TodoItem',() => {
  // ...
  it('invokes callback when checkbox is clicked', ()=>{
    const text = 'React';
    var isChecked = false;
    const toggleComplete = () => isChecked = true;
    const component = renderIntoDocument(
      <TodoItem text={text} toggleComplete={toggleComplete} />
    );
    const checkboxes = scryRenderedDOMComponentsWithTag(component, 'input');
    Simulate.click(checkboxes[0]);

    expect(isChecked).to.equal(true);
  });

  it('calls a callback when text is double clicked', () => {
    var text = 'React';
    const editItem = () => text = 'Redux';
    const component = renderIntoDocument(
      <TodoItem text={text} editItem={editItem} />
    );
    const label = component.refs.text;
    Simulate.doubleClick(label);
    expect(text).to.equal('Redux');
  });
});
```

```
src/components/TodoItem.jsx
```
```javascript
// ..
render(){
  // ...
  return <li className={itemClass}>
    <div className="view">
      // 체크박스 onClick핸들러 추가
      <input type="checkbox"
              className="toggle"
              defaultChecked={this.props.isCompleted}
              onClick={()=> this.props.toggleComplete(this.props.id)} />
      // 테스트를 용이하게 하기 위해 라벨에 ref속성 추가
      // onDoubleClick 핸들러는 놀랍게도 두번 클릭 하면 호출 됩니다.
      <label htmlFor="todo"
              ref="text"
              onDoubleClick={()=>this.props.editItem(this.props.id)}>
        {this.props.text}
      </label>
      <button className="destroy"
              onClick={()=>this.props.deleteItem(this.props.id)}></button>
    </div>
    <TextInput />
  </li>
}
```

또한 우리는 `TodoList` 에서 props로 `editItem`,`deleteItem`,`toggleComplete` 함수를 전달 합니다.

```
src/components/TodoList.jsx
```
```javascript
// ...
export default class TodoList extends React.Component {
  // ...
  render() {
    return <section className="main">
      <ul className="todo-list">
        {this.getItems().map(item =>
          <TodoItem key={item.get('text')}
                    text={item.get('text')}
                    isCompleted={this.isCompleted(item)}
                    isEditing={item.get('editing')}
                    toggleComplete={this.props.toggleComplete}
                    deleteItem={this.props.deleteItem}
                    editItem={this.props.editItem} />
        )}
      </ul>
    </section>
  }
};
```

이제 프로세스에 대해 조금 익숙해졌습니다. 그러면 이 포스팅의 길이를 적당하게 조절하기 위해 여기까지만 작성하고 `TextInput`([관련 커밋](https://github.com/phacks/redux-todomvc/commit/8550a95fc589ecaa184367bb907c8dfeffc29d2f)), `TodoHeader`([관련 커밋](https://github.com/phacks/redux-todomvc/commit/cc97354bab0a0369f0c39b34ff24b44084a75ebb)), `TodoTools` , `Footer`([관련 커밋](https://github.com/phacks/redux-todomvc/commit/237dbc36135427f3b5398f19fcc09ecb1e26d895)) 코드 저장소를 살펴보도록 권유드립니다. 해당 저장소에 대한 질문이나 이슈가 있으시면, 여기에 의견을 남기시거나 저장소에 이슈를 남겨주세요.  

`editItem`이나 `toggleComplete` 같은 함수는 아직 정의되지 않았습니다. 이들은 Redux 액션 관련 다음 챕터 에서 다뤄질 것이므로, 당신이 콘솔에 몇몇 에러를 발견하더라도 걱정하지 마시기 바랍니다.  

이번 포스팅에서는 React, Redux, Immutable 웹 앱을 위한 초석을 다졌습니다. 우리 UI 모듈은 완벽하게 테스트 되었으며, 실제 앱로직과 연결될 준비가 되었습니다.
과연 이것들이 어떻게 작동할까요? 어떻게 할지 모르는 멍청한 컴포넌트들이 시간여행-디버깅을 가능하게 해주는 앱으로 사용하게 해줄까요?  
다음 파트 에서 기대하시기 바랍니다.
