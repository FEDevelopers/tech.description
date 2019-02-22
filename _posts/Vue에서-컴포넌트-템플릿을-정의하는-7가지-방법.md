> 이 문서는 [https://vuejsdevelopers.com/2017/03/24/vue-js-component-templates/](https://vuejsdevelopers.com/2017/03/24/vue-js-component-templates/)의 내용을 변역한 문서입니다.


Vue에서는 컴포넌트 템플릿을 정의할 때 꽤 많은 선택권이 있습니다. 제가 알기로는 적어도 7가지 방법이 있습니다.


 * String 
 * Template literal 
 * X-Templates 
 * Inline 
 * Render functions 
 * JSX 
 * Single page components 
 * 아마 몇개 더 있을거에요

이 글에서는 어느 상황에서 어느 방법이 가장 좋은지 알 수 있도록 각각의 경우에 대해서 장단점을 살펴보겠습니다.

### 1. Strings
템플릿은 JS 파일의 string으로 정의됩니다. 저는 string 템플릿은 가독성이 꽤 떨어진다고 생각하며, 아마 모두들 인정하는 부분일 겁니다. 이 방법은 많은 브라우저 지원 외에는 딱히 큰 장점은 없습니다.
```javascript
Vue.component('my-checkbox', {
  template: `<div class="checkbox-wrapper" @click="check"><div :class="{ checkbox: true, checked: checked }"></div><div class="title"></div></div>`,
  data() {
    return { checked: false, title: 'Check me' }
  },
  methods: {
    check() { this.checked = !this.checked; }
  }
});
```
### 2. Template literals
ES6 템플릿 문자열(“백틱”)은 일반적인 자바스크립트 string과 달리 템플릿 문자열 내에서 줄바꿈을 사용하며 템플릿을 정의할 수 있습니다. 이 방법은 훨씬 가독성이 좋으며 최신 브라우저에서 대부분 지원하고 있지만, 그래도 ES5 문법으로 변환하는 게 안전합니다.

하지만 이 방법도 완벽하지는 않습니다. 대부분의 IDE에서 아직까지는 구문 강조, 탭이나 줄바꿈 등등 다양한 부분에 문제가 있기 때문에 빡침을 유발할 수 있습니다.

```javascript
Vue.component('my-checkbox', {
  template: `<div class="checkbox-wrapper" @click="check">
               <div :class="{ checkbox: true, checked: checked }"></div>
               <div class="title"></div>
             </div>`,
  data() {
    return { checked: false, title: 'Check me' }
  },
  methods: {
    check() { this.checked = !this.checked; }
  }
});
```
### 3. X-Templates
이 방법을 사용하면 `index.html`파일의 `<script>`태그 안에 템플릿이 정의됩니다. `<script>` 태그는 `text/x-template`로 표시되고 컴포넌트 정의 부분에서 지정한 id로 참조됩니다.

이 방법은 템플릿을 HTML 마크업으로 작성하는 점에서는 좋으나, 템플릿과 컴포넌트의 나머지 부분이 분리된다는 단점이 있습니다.

```javascript
Vue.component('my-checkbox', {
  template: '#checkbox-template',
  data() {
    return { checked: false, title: 'Check me' }
  },
  methods: {
    check() { this.checked = !this.checked; }
  }
});
```
```html
<!-- index.html -->
<script type="text/x-template" id="checkbox-template">
  <div class="checkbox-wrapper" @click="check">
    <div :class="{ checkbox: true, checked: checked }"></div>
    <div class="title"></div>
  </div>
</script>
```
### 4. Inline Templates
컴포넌트에 `inline-template` 속성을 추가하면 Vue는 내부 콘텐츠를 템플릿으로 인식합니다. `slot`같은 분산된 콘텐츠와는 다르게 말이죠.

x-templates와 같은 단점이 있지만, HTML 템플릿 안의 내용이 정확한 위치에 있기 때문에 자바스크립트 실행을 기다리는 것보다 페이지 로드 단계에서 렌더링이 된다는 장점이 있습니다.

```javascript
Vue.component('my-checkbox', {
  data() {
    return { checked: false, title: 'Check me' }
  },
  methods: {
    check() { this.checked = !this.checked; }
  }
});
```
```html
<my-checkbox inline-template>
  <div class="checkbox-wrapper" @click="check">
    <div :class="{ checkbox: true, checked: checked }"></div>
    <div class="title"></div>
  </div>
</my-checkbox>
```
### 5. Render functions
Render 함수 방식을 사용하려면 템플릿을 자바스크립트 객체로 정의해주어야 합니다. 이 방법은 템플릿 옵션 중에서 가장 장황하고 추상적입니다.

그러나, 디렉티브(v-)에서 제공하는 기능만이 아닌, 자바스크립트의 전체적인 기능을 사용할 수 있다는 점과 컴파일러에 훨씬 더 가깝다는 점에서는 장점이라고 볼 수 있습니다.

```javascript
Vue.component('my-checkbox', {
  data() {
    return { checked: false, title: 'Check me' }
  },
  methods: {
    check() { this.checked = !this.checked; }
  },
  render(createElement) {
    return createElement(
      'div',
        {
          attrs: {
            'class': 'checkbox-wrapper'
          },
          on: {
            click: this.check
          }
        },
        [
          createElement(
            'div',
	    {
              'class': {
                checkbox: true,
                checked: this.checked
              }
	    }
          ),
          createElement(
            'div',
            {
              attrs: {
                'class': 'title'
              }
	    },
	    [ this.title ]
          )
        ]
      );
    }
  }
);
```
### 6. JSX
Vue에서 가장 논란이 되는 템플릿 옵션인 JSX입니다. 몇몇 개발자는 JSX가 Vue의 정신에 반대된다며 극혐하고 있습니다.
JSX는 브라우저에서 읽을 수 없어서 트랜스파일을 먼저 해야 합니다. 하지만 render 함수를 사용해야 하는 경우, JSX는 확실히 덜 추상적인 템플릿 정의 방법입니다.

```jsx
Vue.component('my-checkbox', {
  data() {
    return { checked: false, title: 'Check me' }
  },
  methods: {
    check() { this.checked = !this.checked; }
  },
  render() {
    return <div class="checkbox-wrapper" onClick={ this.check }>
             <div class={{ checkbox: true, checked: this.checked }}></div>
             <div class="title">{ this.title }</div>
           </div>
  }
});
```
### 7. Single File Components
빌드 툴을 다루는데 익숙하다면 Single File Components가 진리입니다. 싱글 파일 컴포넌트는 컴포넌트 정의와 마크업을 한 파일 내에서 작성할 수 있다는 장점이 있습니다. 이 방법은 트랜스파일 과정을 거쳐야 하며 몇몇 IDE에서는 이 파일 타입에 대해서 구문 강조를 지원하지 않는다는 단점이 있긴 하지만 그래도 짱입니다.

```vue
<template>
  <div class="checkbox-wrapper" @click="check">
    <div :class="{ checkbox: true, checked: checked }"></div>
    <div class="title"></div>
  </div>
</template>
<script>
  export default {
    data() {
      return { checked: false, title: 'Check me' }
    },
    methods: {
      check() { this.checked = !this.checked; }
    }
  }
</script>
```

Pug 같은 템플릿 전처리기를 사용함으로써 템플릿 정의 가능성이 더욱 많아질 수 있을 것입니다.

### 어떤 방법이 짱인가?
물론 완벽한 방법은 없으며 각각의 사용되는 케이스를 잘 판단해야 합니다. 최고의 개발자라면 모든 경우를 인지하고 상황에 알맞은 템플릿 정의 방법을 사용할 것입니다.