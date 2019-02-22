> 이 문서는 http://www.valuecoders.com/blog/technology-and-apps/vue-js-comparison-angular-react/ 를 번역한 내용입니다.

Vue.js는 웹 인터페이스를 만들기 위한 자바스크립트 라이브러입니다. 다른 도구를 함께 사용하면 "프레임웍"이 될수 있는 녀석이죠. 우리가 작성한 최근 블로그를 통해 여러분은 이미 Vue.js가 최고의 자바스크립트 프레임웍이고 앵귤러와 리엑트를  대체한다는 것을 알았을 겁니다. 

이전에 Vue.js를 한번도 들어보지 않았다면 아마도 이렇게 생각할지도 모르겠네요.

> "아놔~ 또 자바스크립트 프레임웤이야?"

알겠어요ㅋㅋ 하지만, Vue.js는 새로운게 아니랍니다. 2013년에 처음 발표되어서 현재는 깃헙에서 38,706개의 별을 받았고 여러번 다운로드 되었죠. 아래 캡쳐 이미지를 보시죠.

![](http://www.valuecoders.com/blog/wp-content/uploads/2016/11/Vuejs-starts-1.png)

![](http://www.valuecoders.com/blog/wp-content/uploads/2016/11/Vuejs-downloads2.png)

보셨죠? 뷰는 새롭다거나 인기가 없는 프레임웍이 아니랍니다. 이제 Vue.js가  왜 좋지 살펴볼까요?

## 뷰js는 왜 특별한가?
Vue의 가장  좋은 점은 계보가 없는 점입니다. 매우 신선하고 가볍죠. 리엑트와 앵귤러의 실패나 성공으로부터 배운 녀석입니다. 우리는 Vue를 가볍고 쉽게 배울수 있죠.

![](http://www.valuecoders.com/blog/wp-content/uploads/2016/11/Lightweight-vuejs.jpg)

Vue의 기본문서는 상당히 좋아요. 앵귤러처럼 많은 노력을 들이지 않아도 되죠. PageKit, Python China는 Vue를 사용하는 프로젝트 중 두가지 입니다. [여기 리스트](https://github.com/vuejs/awesome-vue#projects-using-vuejs) )를 확인해 보세요.  Vue는 앵귤러의 양방향 데이터 바인딩과 리엑트의 가상돔을 가지고 있습니다.

![](http://www.valuecoders.com/blog/wp-content/uploads/2016/11/Virtual-DOM-vuejs.jpg)

Vue.js의 기본 개념이 좀 확실해 졌으면 하는데...... 앵귤러, 리엑트와 비교해 보겠습니다. 앵귤러부터 시작해보죠.

관련글: [AngularJS vs ReactJs: what's better for you? -ValueCoders](http://www.valuecoders.com/blog/technology-and-apps/angularjs-vs-reactjs-suits-better/)

## 앵귤러 vs Vue.js
이 둘를 비교할 때 먼저 앵귤러(대부분 앵귤러 2 이후의)는 매머드이고 Vue.js는 거대해지고 싶은 배고픈 호랑이라고 정의합시다. 개발자들이 Vue로 전환하는 이유가 많지만 Vue의 소유자인 에반 유(Evan You)는 다음과 같이 설명합니다.

> Vue.js는 더 유연하고, (앵귤러 보다) 관점이 적은 솔루션이다. 이러한 점은 어플리케이션을 당신이 원하는 방법으로 설계할 수 있도록한다. 앵귤러는 모든 것을 앵귤러 방식으로 강제화한다. 뷰는 인터페이스 레이어이기 때문에 완벽한 SPA 보다는 개별 페이지 안에서 가벼운 기능으로 사용할 수 있다. 

이제 몇 가지 기본적인 영감을 주는 코드를 살펴 보겠습니다. 

### 모듈과 컴포넌트
```js
// 앵귤러 모듈
angular.module('myModule', [...])

// 뷰 컴포넌트 
Vue.extend({
  data: function(){ return {...} },
  created: function() { ... },
  ready: function() { ... },
  components: function() { ... },
  methods: function() { ... },
  watch: function() { ... },
});
```

앵귤러에서 모듈은 컨테이너 역할을 하는데 컨트롤러나 디렉티브같은 다른 요소들을 포함하고 있습니다. Vue에서는 모든게 컴포넌트 로직에 들어가 있는 점이 달라요.

### 디렉티브 

```js
// 앵귤러 디렉티브 
myModule.directive('directiveName', function(injectables) {
  return {
    restrict: 'A',
    template: '<div></div>',
    controller: function(){ ... },
    compile: function(){ ... },
    link: function() { ... },
  };
});

// 뷰 디렉티브
Vue.directive('my-directive', {
  bind:  function() { ... },
  update:  function(newVal, oldVal) { ... },
  unbind:  function() { ... },
});
```

Vue에서 디렉티브는 그렇게 강력하지 않고 (돔 조작에) 좀 더 집중되어 있지요. 앵귤러에서 디랙티브는 많은 것을 할 수 있었는데 마치 Vue의 컴포넌트 같습니다.

### 필터 

```js
// 앵귤러 필터
myModule.filter('reverse', function(){
  return function(value) { ... };
});

// 뷰 필터  (?)
Vue.filter('reserve', function(value) {
  return function(value) { ... };
})
```

필터는 많이 다르지 않구요, 다만 Vue에서는 read/write 옵션을 제공합니다.

### 보간자 (Interpolation)

```html
<!-- 앵귤러 보간자 -->
{{ myVar }}

<!-- 뷰 보간자 -->
{{ myVar }}
```

객체나 배열은 인터폴레이션이 안됩니다. ([Object] 가 표시될거에요)  앵귤러에서 저는 디버깅할 때 잘 사용했습니다. 뷰에서도 동일하게 빌트인 제이슨 필터가 있습니다.

### 모델 바인딩

```html
<!-- 앵귤러 -->
<input type="text" ng-model="myVar">
<p ng-bind="myVar"></p>

<!-- 뷰 -->
<input type="text" v-model="myVar">
<p v-bind="myVar"></p>
```

### 반복문

```html
<!-- 앵귤러 -->
<li ng-repeat="item in items" class="item-{{$index}}">
  {{item.myProperty}}
</li>

<!-- 뷰 -->
<li v-repeat="item in items" class="item-{{$index}}">
  {{item.myProperty}}
</li>
```

### 조건문

```html
<!-- 앵귤러 -->
<div ng-if="myVar"></div>
<div ng-show="myVar"></div>

<!-- 뷰 -->
<div v-if="myVar"></div>
<div v-show="myVar"></div>
```

### 조건 클래스 
```html
<!-- 앵귤러 -->
<div ng-class="{'active': myVar}"></div>

<!-- 뷰 -->
<div v-class="{'active': myVar}"></div>
```

### 이벤트 바인딩
```html
<!-- 앵귤러 -->
<div ng-click="myMethod($event)"></div>

<!-- 뷰 -->
<div v-on:click="myMethod($event)"></div>
```

v-on 디렉티브는 앵귤러보다 더 일관된 방법인것 같은데 이건 개인 취향인 듯 싶네요.

### Vue.js와 앵귤러의 결론
앵귤러에는 [많은 도구](http://www.valuecoders.com/blog/technology-and-apps/top-20-angularjs-developer-tools/) 와 복잡한 신택스가 있는데 가끔 헷갈립니다. 반면 Vue.js가 훨씬 간단하고 낫다고 봅니다. 만약 이 프레임웍에 대해 걱정스럽다면 그러지 않아도 되는데 앞으로 2 년간은 지속될 것이기 때문입니다.

## 리엑트 vs 뷰
리엑트와 뷰는 아래 비슷한 특징이 있어요.

1. 가상돔을 사용한다.
2. 리엑티브(reactive)하고 컴포져블(compose-able)한 뷰 컴포넌트를 제공한다.
3.  핵심 라이브러리에 집중한다. (라우팅과 상태관리를 포함)

관련글: [5 reasons to choose Facebook’s ReactJS
](http://www.valuecoders.com/blog/technology-and-apps/5-reasons-choose-facebooks-reactjs/)

리액트와 뷰는 기능면에서 상당히 비슷합니다. 그래서 우리는 개발자 관점에서 이 두 녀석을 비교해 볼거에요. 자 어떤 점이 비슷한지 살펴보도록 하죠.

### 헬로 월드 

```html
<!-- 리엑트 -->
<div id="greeting"></div>
<script type="text/babel">
var Greeting = React.createClass({
  render: function(){
    return (
      <p>Hello form React</p>
    )
  }
});
ReactDOM.render(
  <Greeting />,
  document.getElementById('greeting');
);
</script>

<!-- 뷰 -->
<div id="app">
  {{message}}
</div>
<script>
new Vue({
  el: '#app',
  data: {
    message: 'Hello from Vue'
  }
});
</script>
```

심플합니다.  아무런 학습이나 새로운 기술 없이도 뷰를 사용할 수 있습니다.

### 양방향 데이터 바인딩 
```js
// 리엑트
var Message = React.createClass({
  getInitialState() {
    return { message: '' };
  },
  handleMessageChange(e) {
    this.setSate({message: e.target.value});
  },
  render() {
    return (
      <div>
        <input type="text" onChange={this.handleMessageChange} />
        <span>{this.state.message}</span>
      </div>
    }
  }
});

// 뷰
var Message = new Vue({
  el: '#message',
  data: { message: '' }
})

<div id="message">
  <input type="text" v-model="message" />
  <span>{{message}}</span>
</div>
```

뷰에서 v-model 디렉티브를 사용하면 양방향 바인딩은 매우 간단합니다. 리엑트에서는 엄청 길죠.

### 반복문 

```js
// 리엑트
var Iteration = React.crateClass({
  getInitialState() {
    return { array: [1,2,3,4] }
  },
  render() {
    this.state.array.map(function(value) {
      return (
        <span>{value}</span>
      );
    }
  }
});
ReactDOM>render(
  <Iteration />,
  document.getElementById('array')
);
<div id="array"></div>

// 뷰
var Iterantion = new Vue({
  el: '#array',
  data: { array: [1,2,3,4] }
});
<div id="array">
  <span v-for="value in array">{{value}}</span>
</div>
```

여기서도 더 적은 라인과 간단한 코드로 뷰가 이겼네요.

### 뷰와 리엑트 결론

이러한 예제 코드는 뷰가 배우기 쉽고 바로 프로덕션에서 사용할 수 있다는 사실을 보여줍니다.  또한 대규모 코드베이스 관리를 위해 새로운 도구와 패턴을 간단하게 사용하는 방법을 제공합니다. 뷰는 당신의 지식과 함께 확장할 수 있어서 업데이트된 도구와 베스트 프랙티스를 바로 배울수 있습니다.

## 앵귤러, 리액트, 뷰 비교표 
![](http://www.valuecoders.com/blog/wp-content/uploads/2016/11/Angular-react-and-vue-comparision.png)

## 결론  
현재 뷰는 페이스북의 리액트나 구글의 앵귤러2 만큼 인기 있는건 아닙니다. 하지만 많은 개발자들이 뷰로 옮기고 있죠. 라라벨(Laravel) 커뮤니티도 그 친구들이 선호하는 프론트엔드 프레임웍으로써 뷰를 고려하고 있다고 합니다.

> 전반적으로 뷰는 리엑트와 앵귤러의 이슈들에대한 해답을 가지고 있다. 그리고 더 간단하고 쉬운 코드를 제공한다.

