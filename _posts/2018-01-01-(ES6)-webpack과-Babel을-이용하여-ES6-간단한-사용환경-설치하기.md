---
layout: post
title: Node.js Stream 당신이 알아야할 모든 것
translater: jeonghwan_kim
---

**[원본링크가기](https://leanpub.com/setting-up-es6/read#sec_webpack-babel)**

역자: ES6를 로컬에서 간단하게 사용하기위한 방법이 잘 정리되어 있는거 같아 번역해보았습니다 :)

#3.3 webpack과 Babel을 이용하여 ES6 사용하기

해당 파트(#3.3)는 client-side ES6를 아래 열거된 기술을 이용하여 작동시키는 방법을 알아봅니다

- [webpack](http://webpack.github.io/) : client-side 모듈 빌더이자 모듈 로더
- [npm](https://www.npmjs.com/) : 패키지 매니저
- [Babel](http://babeljs.io/) : ES6를 ES5로 변환시켜주는 트랜스파일러~코드변환기~ (우리는 ES6를 사용합니다)

## 3.3.1 webpack 설명
webpack의 특성 중 우리가 살펴볼만한 부분은 다음과 같습니다
- 지원하는 모듈 포맷: AMD, CommonJS
- 지원하는 패키지 매니저: Bower, npm
- non-code를 위한 로더: CSS, templates, ...
- 온디맨드~on demand~ 로딩(chunked transfer)
- 자동으로 브라우저를 새로고침~refresh~해주는 **hot module reloading** 를 지원하는 자체 개발 서버 지원 (개발할때 편리합니다)
- **Dead code elimination** (tree shaking 이라고도 알려져 있습니다)

## 3.3.2 데모 프로젝트 설치하기

데모 프로젝트를 분석해보겠습니다. 원하신다면 아래와 같이 직접 설치하실 수도 있습니다.
- 다음 깃헙 repository로 가서 다운로드 또는 clone 하세요 [webpack-babel-demo](https://github.com/rauschma/webpack-babel-demo)
- `cd webpack-babel-demo/`
- `npm install`

모든 건 로컬에 설치됩니다.

## 3.3.3 데모 프로젝트의 구조
```
build/
    bundle.js
    bundle.js.map
    index.html
html/
    index.html
js/
    main.js
    world.js
node_modules/
package.json
webpack.config.js
```
설정 파일과 라이브러리 :
- `package.json` npm을 설정하는 역할을 합니다. 이를 통해 모든 부품이 설치되어 돌아갈 수 있습니다. 이 프로젝르는 webpack과 Babel 만 필요합니다.
- `node-modules/` npm이 package들을 설치하는 디렉토리~폴더~입니다.
- `webpack.config.js` webpack을 설정합니다. 해당 프로젝트의 경우, 트랜스파일러~transpiler~이자 빌드툴~build tool~의 역할을 맡습니다.

Input 디렉토리 :
- `js/` ES6 코드를 지니는 디렉토리이며, webpack이 ES5로 트랜스파일할 파일이 위치합니다.
- `html/` 수정되지 않은 체 output으로 내보낼 파일들을 지닌 디렉토리입니다.

Output 디렉토리 :
- `build/` weback의 결과물이 위치할 디렉토리입니다. 결과물은 webpack, Babel, npm에 기대지 않는~NOT dependent~ **plain HTML, ES5 코드**입니다. 다음 리스트는 빌드 순서~build step에 따라 만들어집니다.
	- `bundle.js` 모든 자바스크립트의 묶음입니다.
	- `bundle.js,map` 브라우저가 ES5 코드를 작동시킬 수 있도록 가능하게 해주는  source map 입니다. 디버그는 ES6로 합니다. 
	- `index.html` 는 `html/`에서 복사해 가져온 것입니다.

## 3.3.4 파일 패키지 package.json
package.json은 npm 설정을 관리합니다.
```
{
  "devDependencies": {
    "babel-loader": "^6.1.0",
    "babel-preset-es2015": "^6.1.18",
    "babel-polyfill": "^6.3.14",
    "webpack": "^1.12.6",
    "webpack-dev-server": "^1.12.1",
    "copy-webpack-plugin": "^0.2.0"
  },
  "scripts": {
    "build": "webpack",
    "watch": "webpack --watch",
    "start": "webpack-dev-server --hot --inline"
  },
  "babel": {
    "presets": ["es2015"]
  }
}
```

`devDependencies`는 개발에 필요한 npm package를 의미합니다
- `webpack`은 webpack을 로컬에 설치합니다
- `webpack-dev-server`는 webpack에 hot-reloading 개발 웹서버를 추가합니다
- `copy-webpack-plugin`은 build 디렉토리에 파일을 복사하는 webpack 플러그인입니다
- `babel-loader`는 webpack이 Babel을 이용하여 자바스크립트를 트랜스파일할 수 있도록 기능을 제공합니다. 이 패키지는 내부적으로 몇 가지 핵심적인 Babel 패키지를 가지고 옵니다~import~
- `babel-preset-es2015`은 ES6를 ES5로 컴파일하기 위해 쓰이는 Babel 프리셋~preset~입니다.

`scripts`는 webpack을 작동하는 다양한 방법을 적어놓았습니다
- 한번만 Build
	- `npm run build`
	- 웹 브라우저에서 `build/index.html`을 연다
- 파일(들)을 감시~watch~한다. 그 중 하나가 바뀌면 rebuild한다.
	- `npm run watch`
	- 웹 브라우저에서 `build/index.html`을 연다. 변경사항이 있으면 웹페이지를 새로고침한다.
- webpack 개발 서버를 이용한 hot reloading
	- `npm start` (`npm run start` 의 줄임 )
	- `http://loacalhost:8080/` 을 연다. 변경사항이 있으면 자동으로 새로고침된다.

`babel`은 Babel이 프리셋 중에서`es2015`를 사용하도록 알려줍니다. 

## 3.3.5 Directory js/
두 개의 작은 파일이 모든 자바스크립트 코드를 지니고 있습니다.

먼저, `js/main.js`
```
import 'babel-polyfill';
import world from './world';

document.getElementById('output').innerHTML = `Hello ${world}!`;
```
`babel-polyfill`을 가져오는 첫번째 줄을 통하여 (역자:  ES6를 쓰기 위해 필요한) ES6 기본 library를 global 변수로 가지고 옵니다.  (`Object.assign()`, 새로운 ES6 string 메소드 등)

둘째로는 `js/world.js`
```
export defualt 'WORLD';
```
build 과정 중에 web-pack은 이 파일들을 모두 모아 하나의 파일, ` build/bundle.js`로 묶습니다.  그리고 그 파일을 위한 source map을 `build.js.map`이라는 파일로 만듭니다.

## 3.3.6 File html/index.html

HTML파일은 `<script>` 엘레멘트를 통하여 bundle을 실행합니다.
```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>webpack Babel demo</title>
</head>
<body>

<h1>webpack Babel demo</h1>

<div id="output"></div>

<script src="bundle.js"></script>
</body>
</html>
```

##3.3.7 webpack.config.js

다음은 webpack을 위한 설정 파일에서 발췌한 것입니다. (몇가지 import와 세부사항을 제외하였습니다)
```
var dir_js = path.resolve(__dirname, 'js');
var dir_html = path.resolve(__dirname, 'html');
var dir_build = path.resolve(__dirname, 'build');

module.exports = {
    entry: path.resolve(dir_js, 'main.js'),
    output: {
        path: dir_build,
        filename: 'bundle.js'
    },
    module: {
        loaders: [
            {
                loader: 'babel-loader',
                test: dir_js,
            }
        ]
    },
    plugins: [
        // Simply copies the files over
        new CopyWebpackPlugin([
            { from: dir_html } // to: output.path
        ]),
    ],
    // Create Sourcemaps for the bundle
    devtool: 'source-map',
    devServer: {
        contentBase: dir_build,
    },
};
```
해당 파일은 native Node.js 모듈로서 설정에 맞춰서 object을 export하는 역할을 맡습니다. 파일은 `__dirname` 이라는 독특한 Node.js 변수를 사용하는데, 이는 현재 작동되는 module을 담고 있는 parent 디렉토리의 주소~path~를 지니고 있습니다. 설정 파일은 다음과 같은 properties를 갖고 있습니다.

- `entry` :  자바스크립트의 코드가 시작하는 부분을 의미합니다. webpack은 이곳을 시작으로 dependency~의존성~을 컴파일 해 나갑니다.
- `output` : webpack은 모든 entry file과 그 depency를 묶은다음에 `bundle.js`라는 output파일로 출력합니다.
- `module.loaders` : 는 preprocessor, 즉 webpack 전에 미리 하는 예비 프로세서입니다. ES6를 위한 준비는 `bable-loader`라 하는 모듈로더~module.loader~를 통해 제공됩니다.
	- `test` property는 loader가 어떤 파일을 트랜스파일해야하는가를 지정해줍니다. 한 가지 또는 여러개의 test를 지정할 수 있습니다.
	- Multiple tests : 개별 test의 배열("and")
- `plugins` : webpack의 기능을 연장~extend~해줍니다. 본인은 `CopyWebpackPlugin`을 이용하여 `html/`하에 있는 모든 파일을 `build/`로 복사합니다. 이것은 기존에 `grunt`나 `gulp`가 수행하던 일을 webpack이 여기서(플러그인을 통해) 수행한다는 뜻이기도 합니다.
- `devtool` 옵션은 'source-map' 옵션을 키고, 이를 설정합니다.
- `devServer`는 webpack이 개발서버가 어떤 파일을 맡아야 하는지 알려줍니다.

## 3.3.8 클라이언트용 라이브러리를 npm으로 설치하기

클라이언트용 ES6를 위해 필요한 패키지를  npm을 통해 끊김없이 부드럽게~seamlessly~ 설치할 수 있습니다. 먼저 [lodash](https://lodash.com) 를 설치해보고 이를 런타임 dependency로 저장해봅니다. `--save`를 사용합니다.
```
npm install --save lodash
```
이후 ES6코드 어디에서건 사용합니다
```
import { zip } from 'lodash';
console.log(zip(['1', '2'], ['a', 'b']));
```








