> 이 글은 https://itnext.io/whats-the-deal-with-functional-components-in-vue-js-513a31eb72b0 를 번역한 글입니다.

# Vue.js에서 functional component는 무엇일까?
> Vue.js 함수형 컴포넌트의 무엇을, 왜, 언제, 어떻게 하는지 알아보자.

나는 Vue.js 에서 함수형 컴포넌트를 둘러보는데 몇일을 소비했다. 아마도, React, Angular 배경을 가진 사람들은 친숙한 패턴이다. 하지만 난 다음과 같은 기본적인 질문에 대한 답을 찾았다.

- 함수형 컴포넌트는 무엇인가?
- 왜 써야 하나?
- 언제 사용 해야 하나?

좀 애매한 것으로 보인다. 그래서 난 [Vue 문서 페이지](https://vuejs.org/v2/guide/render-function.html) 보는데 많은 시간을 보냈고, 다른 흩어진 몇몇 리소스를 보았다.

## 함수형 컴포넌트는 무엇인가?

초기에 함수형 컴포넌트라고 이름이 붙었다. 나의 생각으로 컴포넌트는 UI의 일부분이라기 보단 어떤 무엇을 할 수있다는 의미로 들렸다. 함수형 컴포넌트의 하나의 유형이 있지만(*higher order component*, *wrapper component*, *delegates*, *decorates*), 또다른 것으로 단순한 다양성이 있다: dumb component. 난 그것들을 dumb 라고 부른다. 그것들은 자신만의 data(stateless이다.) 가 없고, 메서드도 없고, 인스턴스도 없다(*this*, *lifecycle*이 없다.).

## 왜 써야 하나?

함수형 컴포넌트는 조직화, 컴포넌트 장점인 DRY 를 Vue's 반응형 시스템 오버헤드 없이 얻을 수 있다. 앱 퍼포먼스를 향상 시킬 수 있다.

## 언제 사용 해야 하나?

언제든지 앱 일부는 자신만의 리액티브 데이터나 메서드들을 필요로 하지않는다. 하지만 재사용이 편리하길 원하고, 컴포넌트가 제공하는 코드가 독립적이길 원할 때 함수형 컴포넌트를 고려하자.

그래서 기본적인 질문에 답하고, 난 더 복잡한 질문으로 넘어간다. 

## 어떻게 함수형 컴포넌트를 만드는가?

이것을 파헤치기 위해, 난 같은 컴포넌트를 4가지 방법으로 만들기로 결심 했다.

- 표준 단일 파일 컴포넌트 (SFC)
- 템플릿이 아닌 `render` 함수로 만든 표준 컴포넌트(RF)
- `render` 함수와 함께하는 함수형 컴포넌트
- 함수형 단일 컴포넌트 (템플릿에 `functional` 키워드 사용)

내 예제는 디음을 포함한 다양한 함수형 공통 컴포넌트를 탐색하기 위해 약간 꾸몄다.

- triggering events
- slots
- props
- v-if , v-for

각 컴포넌트는 다음과 같이 할 것이다.

- `img`를 포함한 `figure` 그리고 선택적으로 `figcaption` 와 tag 집합
- class/styling 과 마크업을 결정짓는 *type* prop 받는다.
- 컴포넌트 노드에 특별한 메서드를 트리거 하는 클릭 이벤트 전달

[결과](http://norabrowndesign.com/vue/functional/)는 다음과 같다.

![](https://cdn-images-1.medium.com/max/1000/1*wEG0aWQ1nWNPooZ0CAST3A.jpeg)

아래는 App.vue 파일이다.

```` js
<template>
  <div id="app">
    <FigureStandardSFC
      src="cat-basking.jpg"
      type="post-it"
      :tags="['cat','feline','fuzzy','gray']"
      @click="alertHello">Cat basking in the sun.</FigureStandardSFC>

    <FigureStandardRF
      src="red-tailed-hawk.jpg"
      type="modern"
      :tags="['bird','hawk','raptor']"
      @click="alertHello">Red-tailed hawk.</FigureStandardRF>

    <FigureFunctionalRF
      src="orange-blue-abstract.jpg"
      type="eighties"
      :tags="['orange','blue','abstract', 'metal']"
      @click="alertGoodbye">
        Orange circle, blue square.
      </FigureFunctionalRF>

    <FigureFunctionalSFC 
      src="bloodroot-leaf.jpg" 
      type="framed" 
      :tags="['leaf','plant','forest','green']"
      @click="alertGoodbye">Bloodroot leaf.</FigureFunctionalSFC>
  </div>
</template>

<script>
import FigureStandardSFC from "./components/FigureStandardSFC.vue";
import FigureFunctionalSFC from "./components/FigureFunctionalSFC.vue";
import FigureStandardRF from "./components/FigureStandardRF.js";
import FigureFunctionalRF from "./components/FigureFunctionalRF.js";
export default {
  name: "app",
  components: {
    FigureStandardSFC,
    FigureFunctionalSFC,
    FigureStandardRF,
    FigureFunctionalRF
  },
  methods: {
    alertHello(){
      alert("HELLO!")
    },
    alertGoodbye(){
      alert("Bye.")
    }
  }
};
</script>

<style>
 /* see https://github.com/nabrown/vue-functional-examples/blob/master/src/App.vue for all styles */ 
 ...
</style>
````

다음과 같이 상당히 표준적이다.

- 템플릿은 4개 컴포넌트를 사용한다.
- 컴포넌트를 import 하는 스크립트 블록을 만들고, App Vue 인스턴스를 몇몇 메서드와 컴포넌트들로 인스턴스화 한다.
- style 블록 (난 style 배를 타고 갔다?. [github 전체 소스](https://github.com/nabrown/vue-functional-examples/blob/master/src/App.vue) 확인)

### 첫번째: 표준, 단일 파일 컴포넌트

컴포넌트 첫번째 버전은 표준 [단일 파일 컴포넌트](https://vuejs.org/v2/guide/single-file-components.html) 이다.

```` js
<template>
  <figure :class="type" v-on:click="$emit('click')">
    <img :src="src" />
    <figcaption v-if="$slots.default">
      <span><slot></slot></span>
    </figcaption>
    <div class="tags" v-if="tags && type != 'framed'">
      <span v-for="tag in tags"> {{ tag }}</span>
    </div>
  </figure>
</template>

<script>
  export default {
    name: 'FigureStandardSFC',
    props: {
      src: {
        required: true,
        type: String
      },
      type: {
        required: true,
        type: String
      },
      tags: {
        required: false,
        type: Array
      }
    }
  }
</script>
````

우린 우리가 원하는 마크업을 작성하고, 유효성 검사를 정의 한 `props`를 전달한다. 주목할만한 항목은 `v-on:click="$emit('click’)"` 사용하여 클릭 이벤트를 전달 하는것 이며(왜냐하면 트리거 되어질 메소드가 자식 컴포넌트가 아닌 App컴포넌트 위에 있어야 하므로.) `v-if="$slots.default"` 를 사용하여 기본 `slot`([`vm.$slots`](https://vuejs.org/v2/api/#vm-slots) 문서를 봐라)을 채우기 위해 전달 되어진 것(이 경우 `caption`)을 감지 한다. 그렇지 않으면, 기본 `slot` 으로 랩핑된 `figcaption` 마크업을 보여주지 않는다.

### 다음: render 함수를 사용한 표준 컴포넌트

다음 접근방식은 템플릿 대신에 render 함수를 사용하여 표준 컴포넌트를 만드는 것이다.

함수형 컴포넌트와 render 함수의 개념은 실제로 완전히 별개이지만, Vue.js 문서에선 관련이 있다.(함수형 컴포넌트 설명은 **[Render Functions & JSX](https://vuejs.org/v2/guide/render-function.html)** 페이지 에 있다.) 그리고 나는 전통적인 템플릿 으로 함수형 컴포넌트를 한 예제를 거의 본적이 없다. 그래서 우린 render 함수도 잘 파악 할 수 있다.

여기 코드가 있다.

```` js
export default {

  props: {
    src: {
      required: true,
      type: String
    },
    type: {
      required: true,
      type: String
    },
    tags: {
      required: false,
      type: Array
    }
  },
  render(createElement){

    const img = createElement(
      'img',
      {
        'attrs': {
          'src': this.src
        }
      }
    )

    const caption = this.$slots.default ? createElement(
      'figcaption',
      [ createElement('span', this.$slots.default) ]
    ) : ''

    const tags = this.tags && (this.type != 'framed') ? createElement(
      'div',
      {'class' : 'tags'},
      this.tags.map(function (tag) {
        return createElement('span', tag)
      })
    ) : ''
    
    return createElement(
      'figure', 
      {
        'class': this.type,
        'on': {
          'click' : () => {
            this.$emit('click')
          }
        }
      },
      [img, caption, tags]
    )
  }
}
````

가장 큰 변화는 분명하게 템플릿이 사라지고, 대신에 render 속성을 가지고 있다는 것이다. 그 값은 `render(createElement){}` 함수 이다. `createElement` 는 render 함수 내에서 가상 DOM 노트(VNodes) 트리를 만들게 해주는 함수다. Vue가 실제 DOM에 추가 되어진다.

[`createElement` 함수는 3가지 인자](https://vuejs.org/v2/guide/render-function.html#createElement-Arguments)가 있다.

1. 만들어질 html 요소의 `string`
2. VNode의 다양한 속성을 지정한 `data` 객체
3. 문자열 배열(텍스트 노드) 또는 생성된 요소로 랩핑 될 다른 VNodes

위 render 함수에서 볼 수 있듯이, 상대적인 작은 트리를 위해 만드는건 매우 고통스런 작업이다. 이 예제에서는 우린:

1. `img` 요소 생
2. `figcaption` 요소 생성 만약 기본 slot 컨텐츠가 있다면,
3. tags 마크업 생성, prop 통해 tags가 전달 된다면
4. `figure` 요소 랩핑한 VNode를 반환하는 `createElement` 마지막 호출의 3번째 인자로 위 모든것을 배열로 전달한다.

휴.

왜냐하면, 이건은 단지 표준 컴포넌트이다. 인스턴스가 있고, props와 $slots 에 접근하여 사용 할 수 있다. 템플릿에서 `v-if` 의 동작을 얻기 위해선, 우린 `this.$slots.default`로 수동으로 확인하고, 없다면 빈 문자열을 반환한다. 템플릿내 `v-for`가 사용된 곳에서 우린 `Array.map`을 사용하여, 각 태그를 위한 분리된 span VNode 를 반환한다.

JSX를 사용하여 템플릿 문법같은 것을 다시 사용 할 수 있다. 조금 이상한 것처럼 보이지만, 몇몇 경우엔 적절 하다.

### 세번째: render 함수를 사용한 함수형 컴포넌트

세번째 버전은 함수형 컴포넌트 이다. `functional: true` 속성으로 사용 할 수 있다.

```` js
export default {
  functional: true,
  props: {
    src: {
      required: true,
      type: String
    },
    type: {
      required: true,
      type: String
    },
    tags: {
      required: false,
      type: Array
    }
  },
  render(createElement, {props, listeners, slots}){

    const img = createElement(
      'img',
      {
        'attrs': {
          'src': props.src
        }
      }
    )

    const caption = slots().default ? createElement(
      'figcaption',
      [ createElement('span', slots().default) ]
    ) : ''

    const tags = props.tags && (props.type != 'framed') ? createElement(
      'div',
      {'class' : 'tags'},
      props.tags.map(function (tag) {
        return createElement('span', tag)
      })
    ) : ''

    return createElement( 
      'figure', 
      {
        'class': props.type,
        'on' : {
          'click': listeners.click
        }
      },
      [img, caption, tags]
    )
  }
}
````

이 컴포넌트도 render 함수를 사용하고, 이전 컴포넌트와 비슷해보이지만, 몇가지 중요한 차이점이 있다. `this`를 사용하는 대신에, 우린 render 함수에서 제공하는 context인 2번째 인자를 사용한다. [object destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Unpacking_fields_from_objects_passed_as_function_parameter) 을 사용하여 context에서 필요로 하는 `props`, `listeners`, `slots`을 추출 할 수 있다.

`Slots`은 다양한 `slots` 를 가진 객체를 반환하는 함수이다. 그래서 이전에 `$slots.default` 를 사용했던 곳에서 `slots().default` 를 사용 할 수 있다. 

`context` 객체에 몇가지 주의 해야할 2가지 속성이 있다. 하나는 `children` 과 `slots()` 은 비슷하지만 약간 다른 것을 제공한다. [다른점은 여기 를 읽어보아라](https://vuejs.org/v2/guide/render-function.html#slots-vs-children) 두번째는, `listener` 는 `data.on` 의 별칭이다.

### 마지막 베스트: 함수형, 단일 파일 컴포넌트

마지막 버전은 다시 단일 파일 컴포넌트이다. 하지만 이번엔 `functional` 키워드를 사용 한다. 정보를 파해치는건 어려운 일이였지만, 결국 해냈다. `functional` 키워드는 문서에 언급됬지만, 오로지 전달 하는것 뿐이지 상세하게 나와있지 않다. (문자 그대로) **vue-loader** [문서](https://vue-loader.vuejs.org/guide/functional.html)에 소개 되어있다. 

첫번째 표준 컴포넌트와 비교해 보자.

```` js
<template functional>
  <figure :class="props.type" v-on:click="listeners.click">
    <img :src="props.src" />
    <figcaption v-if="slots().default">
      <span><slot></slot></span>
    </figcaption>
    <div class="tags" v-if="props.tags && props.type != 'framed'">
      <span v-for="tag in props.tags"> {{ tag }}</span>
    </div>
  </figure>
</template>

<script>
  export default {
    name: 'FigureFunctionalSFC',
    props: {
      src: {
        required: true,
        type: String
      },
      type: {
        required: true,
        type: String
      },
      tags: {
        required: false,
        type: Array
      }
    }
  }
</script>
````

여기 다른점을 비교해 볼 수 있다. 

![](https://cdn-images-1.medium.com/max/2000/1*FF1riu2_4VNBVDSOIJEThQ.png)

차이점은 인스턴스가 없는 함수형 컴포넌트 때문이다. 인스턴스가 없으면, 인스터스 속성인 `prop` 이름 또는 `$slots` 를 사용 할 수 없다. 하지만 위의 버전3 처럼, 우린 전에 봤듯이, `props` 객체, `listener` 객체 그리고 `slot()` 함수를 가지고 있는 render context 객체에 접근할 수 있다.

대부분 멍청한(dumb) 컴포넌트는 이 문법으로 쉽고 이해하기 쉽게 작성 할 수 있다. 당신은 여전히 쉽게 html 과 같은 템플릿을 사용 할 수 있고, 만약 좀더 길다면, 위에 `functional` 키워드는 즉시 컴포넌트로 식별 할 수 있다. 따라서 Vue 컴포넌트 오버헤드 없이 단일 컴포넌트 장점을 얻을 수 있다. #승리

## 결론

내가 했던 앱은 멍청한(dumb) 컴포넌트로 꽉찬것으로 작동되었었다: 작고 자주 반복되는 버튼, 폼 입력, 뱃지. 어느 부분이든 재사용 되길 원하지만, reactvie 데이터가 필요 없는 거라면, `functional` 키워드와 함께 단일 파일 컴포넌트는 좋은 선택이다. 함수형 컴포넌트를 사용하면, 앱 성능을 향상 시킬 수 있고, 단일 파일 컴포넌트로 작성하면 읽기 쉽고 다른 컴포넌트와 함께 일관성을 유지 할 수 있다. 

하지만 만약 일반 템플릿에서 제어가 필요하다면, `render` 함수가 해결책이 될 수 있다. 대부분의 예제는 만읂 조건부 로직 기반에 html 출력을 포함 하는것을 봐왔지만, 반복적이고, 읽기 어려운 템플릿 형식일 것이다.

유용한 링크
- [All the code, on Github](https://github.com/nabrown/vue-functional-examples)
- [The finished result](http://norabrowndesign.com/vue/functional/)
- [Video from Dan Aprahamian on render functions.](https://www.youtube.com/watch?v=KS4eizPXRCQ)
- [Another one from Erik Hanchett](https://www.youtube.com/watch?v=8vp5OXcbM34)
- [Render functions on Vue.js](https://vuejs.org/v2/guide/render-function.html)
- [Functional components on Vue.js (same page as above)](https://vuejs.org/v2/guide/render-function.html#Functional-Components)
- [Alligator.io article on functional components](https://alligator.io/vuejs/functional-components/)