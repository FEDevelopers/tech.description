> https://www.sitepoint.com/beginners-guide-to-webpack-2-and-module-bundling/?utm_source=frontendfocus&utm_medium=email 를 번역한 글입니다.

> Webpack 은 모듈 번들러 입니다.

모던 웹 개발에 있어서 [Webpack](https://webpack.js.org/)은 매우 중요한 도구중에 하나로 자리 잡았습니다. 주로 자바스크립트를 위한 모듈 번들러이지만, HTML, CSS 심지어 이미지들 같은 프론트엔드 assets 들을 변환시켜 주는 법을 배울 수 있습니다. 여러분의 앱에 HTTP 요청 갯수를 더많이 컨트롤 할 수 있게 만들 수 있으며, 다른 종류의 assets(Jade, Sass, ES6등)를 사용 할 수 있게 도와줍니다. 또한 Webpack을 사용하면 npm 패키지를 쉽게 사용할 수 있습니다.  

이번 포스팅은 webpack을 처음 사용하는 사람을 대상으로 하며, 초기 설치, configuration, modules, loaders, plugins, code splitting, hot module replacement에 대해 설명합니다. 만약 여러분이 비디오 튜토리얼이 좋다면, Glen Maddern’s의 [Webpack from First Principles](https://www.youtube.com/watch?v=WQue1AN93YU)을 시작점으로 삼고 webpack의 특별한점을 이해하는것이 좋을것입니다.  
집에서 다음을 따라가기 위해서 [NodeJS](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/) 설치해야하며, 또한 Gihub 저장소에서 [데모앱](https://github.com/sitepoint-editors/webpack-demo)을 다운 받을 수 있습니다.

#Setup

이제 npm으로 새로운 프로젝트를 초기화하고, webpack을 설치합시다.

```
mkdir webpack-demo
cd webpack-demo
npm init -y
npm install webpack@beta --save-dev
mkdir src
touch index.html src/app.js webpack.config.js
```

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>hello webpack</title>
  </head>
  <body>
    <div id="root"></div>
    <script src="dist/bundle.js"></script>
  </body>
</html>
```

```javascript
// src/app.js
const root = document.querySelector('#root');
root.innerHTML = `<p>Hello webpack</p>`;
```

```javascript
// webpack.config.js

const webpack = require('webpack')
const path = require('path')

const extractCommons = new webpack.optimize.CommonsChunkPlugin({
  name: 'commons',
  filename: 'common.js'
})

const ExtractTextPlugin = require('extract-text-webpack-plugin')
const extractCSS = new ExtractTextPlugin('[name].bundle.css')
const config = {
  context: path.resolve(__dirname, 'src'),
  entry: './app.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  },
  module: {
    rules: [{
      test: /\.js$/,
      include: path.resolve(__dirname, 'src'),
      use: [{
        loader: 'babel-loader',
        options: {
          presets: [
            ['es2015', {moduels: false}]
          ]
        }
      }]
    }]
  }
}

module.exports = config
```

위 설정은 흔한 시작 포인트입니다. 그리고 webpack에게 entry point는 `src/app.js`로 하고, output 은 `/dist/bundle.js`로 하라고 지시하고, 바벨을 이용하여 모든 `.js` 파일들을 *ES2015* 에서 *ES5* 로 변환 하라고 합니다.  
위 작업을 진행 하기 위해 `babel-core`, webpack loader 인 `babel-loader`, 그리고 `babel-preset-es2015` 3개의 패키지를 설치하여야 합니다. `{ modules: false }`는 Tree Shaking 사용하여, bundle로 부터 나온 사용하지 않는 것들을 삭제하여 파일 크기를 줄입니다.

```
npm install babel-core babel-loader babel-preset-es2015 --save-dev
```

마지막으로 `package.json` 파일에 `scripts` 부분을 다음과 같이 교체합니다.

```
"scripts": {
  "start": "webpack --watch",
  "build": "webpack -p"
},
```

커맨드라인에서 `npm start` 를 실행하면, webpack은 `watch` 모드로 실행 될 것이며, `src` 디렉토리에 `.js` 파일들이 변경되면, 번들파일이 다시 컴파일 됩니다. 콘솔 결과는 번들 생성 정보를 알려주며, 결과 나온 번들의 갯수와 사이즈에 대해 주의깊게 살펴봐야 합니다.

![webpack result](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2017/01/1484692629bundle-1.png)

이제 당신은 브라우저에서 `index.html` 을 실행 할 수 있으며, `Hello webpack` 을 볼 수 있을겁니다.

```
open index.html
```

`dist/bundle.js` 파일을 열어 webpack이 한 일을 보면, 맨위에 webpack 모듈 부트스트래핑 코드와 오른쪽 맨 아래는 우리의 모듈을 볼 수 있습니다. 아직까진 인상깊진 않겠지만, 여러분은 ES6모듈을 사용하여 개발 할 수 있고, webpack이 모든 브라우저에서 실행 할 수 있는 번들을 생성시켜 줄 것 입니다.  
`Ctrl+C`로 webpack을 멈추고, 프로덕션 모드에서 번들을 컴파일 하기 위해 `npm run build`를 실행해 보겠습니다.  
번들 사이즈가 **2.61KB** 에서 **585bytes** 로 줄어든 것을 볼 수 있습니다.  
`dist/bundle.js` 를 다시 한번 보면, 복잡하고 이상한 코드를 보게 됩니다. 번들파일은 `UglifyJS`를 사용하여 축소되어, 코드 실행은 정확히 동일하지만, 적은 문자를 이용하여 완료되어집니다.

# Modules

webpack은 다양한 포맷의 자바스크립트 모듈을 사용하는 방법을 알고 있으며, 가장 주목 할만한 2가지는 다음과 같습니다.

- ES2015 `import` 구문
- CommonJS `require()` 구문

우리는 [lodash](https://lodash.com/)를 설치하고 `app.js`로 import 하여 위에 것을 테스트 할 수 있습니다.

```
npm install lodash --save
```
```javascript
// src/app.js
import {groupBy} from 'lodash/collection'

const people = [{
  manager: 'Jen',
  name: 'Bob'
},{
  manager: 'Jen',
  name: 'Sue'
},{
  manager: 'Bob',
  name: 'Shirley'
},{
  manager: 'Bob',
  name: 'Terrence'
}]
const managerGroups = groupBy(people, 'manager')

const root = document.querySelector('#root')
root.innerHTML = `<pre>${JSON.stringify(managerGroups,null,2)}</pre>`
```

`npm start` 실행하여 webpack을 시작하고, `index.html` 을 새로고침합니다. 당신은 **manager** 에 의해 그룹화된 `people` 배열을 볼 수 있습니다.

`people.js` 모듈로 `people` 배열을 이동하도록 하겠습니다.

```javascript
// src/people.js
const people = [{
  manager: 'Jen',
  name: 'Bob'
}, {
  manager: 'Jen',
  name: 'Sue'
}, {
  manager: 'Bob',
  name: 'Shirley'
}, {
  manager: 'Bob',
  name: 'Terrence'
}]

export default people
```

상대 경로로 `app.js` 로 부터 위 파일을 간단히 import 할 수 있습니다.

```javascript
// src/app.js
import {groupBy} from 'lodash/collection'
import people from './people'

const managerGroups = groupBy(people, 'manager')

const root = document.querySelector('#root')
root.innerHTML = `<pre>${JSON.stringify(managerGroups,null,2)}</pre>`
```

참고 : *lodash/collection* 같이 상대경로 없는 import는 npm으로 인스톨된 `/node_modules` 에 있는 모듈입니다. 여러분 자신의 모듈은 항상 *./people* 와 같은 상대경로가 필요합니다.

#Loaders
우리는 `babel-loader`에 대해 이미 소개한적이 있습니다. `babel-loader`는 여러 다양한 타입 파일을 import 할 때 webpack에 어떤 작업을 할 것인지 설정할 수 있는 많은 loaders중 하나입니다. loaders를 일련의 변형으로 묶을 수 있으며, 자바스크립트에서 *Sass* 가 어떻게 가져오는지 작동법을 알 수 있는 좋은 방법입니다.

##Sass
이 변환을 하기 위해선 3가지 분리된 loaders와 `node-sass` 라이브러리를 포함시켜야 합니다.

```
npm install css-loader style-loader sass-loader node-sass --save-dev
```

`.scss` 파일에 대한 새로운 룰을 설정해야합니다.

```javascript
// webpack.config.js
rules: [{
  test: '/\.scss$/',
  use: [
    'style-loader',
    'css-loader',
    'sass-loader'
  ]
},{
  // ...
}]
```

참고 : 만약 `webpack.config.js` 안에 룰을 변경하였으면, `Ctrl+C` 커맨드를 실행하여 webpack 종료 후 `npm start` 실행하여 빌드를 재시작 해야합니다.

`loaders` 배열은 역순으로 처리 됩니다.

- `sass-loader` Sass -> CSS 로 변환.
- `css-loader`  자바스크립트 안에 CSS를 해석하고, 모든 의존성 해결.
- `style-loader` CSS를 `document`의 `<style>` 태그에 출력합니다.

당신은 `loaders`가 함수 호출로써 실행 된다고 생각할 수 있습니다. 하나의 `loader` 출력이 다음 `loader`의 입력으로 주게 됩니다.

```
styleLoader(cssLoader(sassLoader('source')))
```

Sass 소스파일을 추가해봅시다.

```css
/* src/style.scss */
$bluegrey: #2B3A42;

pre{
  padding: 20px;
  background: $bluegrey;
  color: #dedede;
  text-shadow: 0 1px 1px rgba(#000, .5);
}
```

이제 자바스크립트에서 위 파일을 직접적으로 `require` 할 수 있고, `app.js` 맨위에 import를 사용하여 가져올 수 있습니다.

```javascript
// src/app.js
import './style.scss'

// ...
```

`index.html`을 새로고침 하면 약간의 스타일링된 상태를 볼 수 있습니다.

##CSS in JS
우리는 모듈로써 자바스크립트에서 Sass파일을 가져왔습니다.

`dist/bundle.js`을 열어서 “pre {“ 을 검색해보겠습니다. 사실 Sass는 CSS 문자열로 컴파일되어 번들내에 모듈로 저장되어졌습니다. 자바스크립트에서 이 모듈을 가져올 때, `style-loader`는 문자열이 포함된 `<style>` 태그로 삽입 합니다.

나는 여러분이 무슨 생각하는지 알고있습니다. 왜?

여기서 이 토픽에 대해 너무 깊이 파고들진 않겠지만, 몇가지 고려해야할 이유가 있습니다.:

- 여러분의 프로젝트에 포함하길 원하는 자바스크립트 컴포넌트는 적절히 작동하기 위해 다른 *assets* (HTML, CSS, 이미지, SVG)에 의존합니다. 만약 모두 함께 번들링 할 수 있으면, 쉽게 import하고 쉽게 사용 할 수 있습니다.
- Dead code elimination: 여러분의 코드에서 더이상 자바스크립트 컴포넌트를 가져오지 않는다면, CSS 또한 더이상 가져올 수 없습니다.
- CSS Modules: CSS의 전역 네임스페이스를 만들면 여러분의 CSS를 변경 해도 부작용이 없다라고 확신하기가 어렵습니다. CSS 모듈은 기본적으로 로컬로 만들고, 여러분의 자바스크립트에서 참조할 수 있는 고유 클래스 이름을 표시함으로써 이를 변경합니다.
- 코드를 분할 / 번들링하는 영리한 방법으로 HTTP 요청 갯수를 줄입니다.

##Images
*loaders* 의 마지막 예제로 `url-loader`로 이미지를 처리하는 법을 보겠습니다.  
표준 HTML 문서에서 브라우저는 `<img>` 태그나 배경-이미지 속성이 있는 요소를 발견하면 이미지를 가져옵니다. webpack은 이미지 소스를 자바스크립트 내부에 문자열로 저장하여 작은 이미지같은 경우 최적화 할 수 있습니다. 이러면 미리 로드하여 브라우저는 별도의 요청으로 나중에 가져올 필요가 없습니다.

```
npm install file-loader url-loader --save-dev
```

이미지를 로드하기 위하여 룰을 추가합니다.

```javascript
// webpack.config.js
rules: [{
  test: /\.(png|jpg)$/,
  use: [{
    loader: 'url-loader',
    options: { limit: 10000 } // 10k 이하 이미지는 base64 문자열로 변환
  }]
},{
  // ...
}]
```

`Ctrl+C` 와 `npm start` 커맨드로 재빌드 재시작 해보겠습니다.

아래 커맨드로 [테스트이미지](https://raw.githubusercontent.com/sitepoint-editors/webpack-demo/master/src/code.png)를 다운로드 받겠습니다.

```
curl https://raw.githubusercontent.com/sitepoint-editors/webpack-demo/master/src/code.png --output src/code.png
```

이제 `app.js` 맨 위에서 이미지 소스를 가져올 수 있습니다.

```javascript
// src/app.js
import codeURL from './code.png'
const img = document.createElement('img')
img.src = codeURL
img.style = "background: #2B3A42; padding: 20px"
img.width = 32
document.body.appendChild(img)

// ..
```

여기에는 `src`속성에 이미지 자체 데이터 URI를 포함하고 있습니다.

```html
<img src="data:image/png;base64,iVBO..." style="background: #2B3A42; padding: 20px" width="32">
```

또한, `url()` 에 의해 참조되는 `css-loader` 이미지때문에 `url-loader` 통해서 CSS 에서 직접적으로 이미지를 인라인 할 수 있습니다.

```css
/* src/style.scss */
pre {
  background: $bluegrey url('code.png') no-repeat center center / 32px 32px;
}
```

다음과 같이 컴파일 됩니다.

```css
pre {
    background: #2b3a42 url("data:image/png;base64,iVBO...") no-repeat scroll center center / 32px 32px;
}
```

##Modules to Static Assets
여러분은 loaders가 *assets* 간에 의존성 트리를 어떻게 빌드하는지 알아야 합니다. 아래 이미지는 webpack 홈페이지에서 보여주는 것입니다.

![webpack 구조](https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2017/01/1484692838webpack-dependency-tree.png)

비록 자바스크립트가 시작지점이지만, HTML, CSS, SVG와 같은 각각 다른 asset 타입마다 빌드 프로세스의 일부분으로 간주되어야 하는 그들 자신만의 의존성을 가지고 있는점에 감사드립니다.

#Plugins
우리는 이미 webpack 플러그인이 내장된 예제를 보았습니다. `npm run build` 스크립트를 통해 호출된 `webpack -p`는 webpack과 함께 제공되는 [UglifyJsPlugin](https://webpack.js.org/api/cli/#shortcuts)을 사용하여 `production`을 위한 번들 파일을 `minify` 합니다.  
loaders가 하나의 파일을 변환하는 동안 플러그인은 더 큰 코드 청크 에서 작동합니다.

##Common code
[commons-chunk-plugin](https://webpack.js.org/plugins/commons-chunk-plugin/) 은 webpack에서 제공하는 또다른 핵심 플러그인으로, 여러 진입점 사이에 공유되는 코드로 구성되는 별도의 모듈을 생성하는데 사용할 수 있습니다. 지금까지는 하나의 진입점과 하나의 번들을 사용하였습니다. 다중 진입과 출력 파일 분할을 통해서 많은 이득을 얻을 수 있는 [실제 시나리오](https://webpack.js.org/concepts/entry-points/#scenarios)가 많이 있습니다.  
공용 앱인 `app.js`와 관리영역인 `admin.js`와 같이 모듈을 공유하는 두가지 다른 영역의 어플리케이션을 가지고 있는 경우 다음과 같이 별도의 진입점을 생성할 수 있습니다.

```javascript
// webpack.config.js
const webpack = require('webpack')
const path = require('path')

const extractCommons = new webpack.optimize.CommonsChunkPlugin({
  name: 'commons',
  filename: 'commons.js'
})

const config = {
  context: path.resolve(__dirname, 'src'),
  entry: {
    app: './app.js',
    admin: './admin.js'
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].bundle.js'
  },
  module: {
    // ...
  },
  plugins: [
    extractCommons
  ]
}

module.exports = config
```

`[name]`을 포함한 `output.filename`의 변경을 확인 하십시오. 위 설정을 통해서 청크 네임으로 변경된 `app.bundle.js`와 `admin.bundle.js`라는 2개의 번들파일을 예상 할 수 있습니다.  
`commonschunk` 플러그인은 진입점의 공유 모듈을 포함하는 3번째 파일인 `commons.js` 을 생성합니다.

```javascript
// src/app.js
import './style.scss'
import {groupBy} from 'lodash/collection'
import people from './people'

const managerGroups = groupBy(people, 'manager')

const root = document.querySelector('#root')
root.innerHTML = `<pre>${JSON.stringify(managerGroups, null, 2)}</pre>`
```
```javascript
// src/admin.js
import people from './people'

const root = document.querySelector('#root')
root.innerHTML = `<p>There are ${people.length} people.</p>`
```
