> 이 문서는 http://moduscreate.com/webpack-2-tree-shaking-configuration/ 를 번역한 내용입니다.


![tree](http://moduscreate.com/wp-content/uploads/2016/02/webpacktreeshaking.jpeg)


ECMAScript 2015에서 사용하지 않는 코드를 배제하는 알고리즘인 트리 쉐이킹은 webpack2에서 가장 기대되는 기능 중 하나다. ES6의 exports 중 사용되지 않는 것을 배제함으로써 webpack은 번들링을 이전보다 훨씬 작은 사이즈로 만들어준다.


궁극적으로 트리 쉐이킹은 스크립트에서 실제로 import 하는 모듈만 번들링한다. import 하지 않는 모듈은 자바스크립트 최종 빌드 파일에는 포함되지 않는다.


이미 최적화된 webpack1 프로젝트를 새로운 webpack2 설정으로 코드의 28%가 줄어드는 것을 아래 샘플 코드로 확인해보자.


2017/04/25 수정: 샘플 코드가 함수형, 그리고 재사용 가능한 리액트 어플리케이션으로 수정되었다. webpack2, react router4 그리고 import() 문법이 사용되었고, 성능이 뛰어난 PRPL 패턴으로 설정되었다. 이 글의 링크는 이전 버전을 가리키므로 꼭 [version2](https://github.com/ModusCreateOrg/budgeting-sample-app-webpack2)를 확인하자.


2016/11/14 수정: [webpack2 샘플 코드](https://github.com/ModusCreateOrg/budgeting-sample-app-webpack2/tree/v1)는 최신 webpack(v2.2), React(v15.4), Babel 버전에 맞춰 수정되었다.


### CommonJS Out, Native Imports In


이제 webpack2는 CommonJS 모듈 방식이 아닌 native import로 동작된다. CommonJS에서의 exports는 코드에 참조가 남아있기 때문에 실제로 구동에 사용하지 않는 코드일지라도 최종 빌드 산출물에 포함되었기 때문에 webpack2의 방식은 이전에 비해 매우 중요해졌다.


긴 코드를 짧게 줄이기 위해 ```.babelrc``` 설정을 조금 바꿔야한다. ```es2015``` preset 대신에 ```babel-preset-es2015-native-modules```라는 이름의 새로운 모듈로 바꿔야한다. 이 값은 Babel이 CommonJS 모듈 방식으로 트랜스파일링하는 것을 skip하게끔 해준다.


새로운 ```.babelrc```은 아래와 같다.


``` javascript
{
  "presets": ["es2015-native-modules"]
}

혹은, 리액트를 사용한다면,

{
  "presets": ["es2015-native-modules", "react"]
}

```


당신의 Babel 설정은 이제 준비가 되었다. 본질적으로 webpack2에서 트리 쉐이킹을 위한 설정은 이게 전부다.


이제 v1.x의 webpack에서 webpack2로 옮기고 싶은 사람들을 위해 설정 값이 변경된 포인트를 짚어보자.


### webpack1에서 webpack2로 옮기기


트리 쉐이킹은 webpack2에서 즉시 작동한다. 만약 webpack을 사용하고 있는 프로젝트가 있다면 기존 구성을 업그레이드 할 가능성이 있다. 또는 webpack 1.x에서 작동하도록 하는 시드 프로젝트를 사용할 수도 있다. 이제 일부 설정을 수정해야하는 몇 가지 주요 변경 사항을 살펴보자.


1. Loaders 설정


loader 설정하는 방법이 약간 개선되었습니다. 예전의 GET 유사 구문은 JSON 스타일 표기법으로 대체 되었습니다.


webpack 1.x의 예제를 살펴봅시다: 


``` javascript
loaders: [
      {
        test: /\.html$/,
        loader: 'file?name=[name].[ext]'
      }
]
```


같은 동작을 하는 webpack 2.x 버전의 코드를 살펴봅시다: 


``` javascript
loaders: [
      {
        test: /\.html$/,
        loader: 'file',
        query: {
          name: '[name].[ext]'
        }
      }
]
```


query 매개변수가 key-value 쌍으로 깔끔하게 정리된 것을 확인할 수 있습니다.


2. Resolvers


webpack2에서 ```root```, ```modulesDirectories```, ```fallback``` 설정에서의 resolve는 ```modules```로 합쳐졌다. 다음은 webpack2에서 파일 및 모듈 위치를 확인할 수 있는 방법이다.


``` javascript
resolve: {
    modules: [
      path.resolve('./client'),
      'node_modules'
    ]
  },
```


```modules```에 여러개의 디렉토리를 지정할 수 있고, node_modules 혹은 npm 패키지 의존성을 빼먹지 않도록 꼭 기억하자.


3. Uglify 플러그인


UglifyJsPlugin은 더이상 로더를 압축하지 않으며 debug 옵션은 더이상 사용되지 않는다. 이 옵션들은 관심사 분리를 위해 LoaderOptionsPlugin이라는 새로운 플러그인으로 이동되었다. 사용할 땐 아래와 같이 하자.


```javascript
   new webpack.LoaderOptionsPlugin({
      minimize: true,
      debug: false
    }),
```


4. 다른 변경들


Sokra라고도 불리우는 Tobias Koppers가 내놓은 [webpack2의 새로운 것들](https://gist.github.com/sokra/27b24881210b56bbaff7)을 참고하자. 훑어보는 것을 강추한다.


### 수행 예제


빌드 산출물을 더욱 작게 만들 수 있는 모든 설정이 적용된 React + Redux + Webpack2 샘플을 살펴보자. webpack 1.x 버전이 적용된 이전 버전과 비교했을 때 크기가 무려 28%나 감소했다.


![webpack2improvement.png](http://moduscreate.com/wp-content/uploads/2016/02/webpack2improvement.png)



직접 해보고, 그 결과를 온라인에 공유해보자. 누군가는 반드시 트리 쉐이킹 기술을 이용해서 webpack 빌드를 최적화하고 싶어할 것이다.