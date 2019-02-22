> [How To Build Vue Components That Play Nice](https://vuejsdevelopers.com/2018/06/18/vue-components-play-nicely/) 를 번역한 글입니다.

Vue 컴포넌트를 처음부터 오픈소스로 사용하려고 만드는 사람은 많지 않습니다.

대부분의 사람은 본인들을 위해서 컴포넌트를 만듭니다. 문제가 생기면, 컴포넌트를 만들어서 해결하려고 하죠.
가끔 우리는 각기 다른 코드들에서 같은 문제를 해결하기 원하는 경우가 있습니다. 그럴 때 우리는 컴포넌트를 리팩토링해서 재사용 할 수 있도록 만듭니다.
그런 다음, 이 컴포넌트를 다른 프로젝트에서 사용하고 싶어집니다. 그러면 이제 독립된 패키지로 만들어버리죠. 그런 다음 우리는 "음, 그냥 이걸 모든 사람과 공유하는 건 어떨까?" 하고는 오픈소스로 올려버립니다.

이건 멋진 일입니다. Vue 개발자들을 위한 오픈 소스 컴포넌트의 큰 성장 가능성을 의미하죠. ([npmjs.com](https://www.npmjs.com/search?q=vue) 에서 vue로 검색하면 14,000개 이상의 패키지가 나옵니다.)
반면에, 대부분의 컴포넌트는 특정 상황에서 만들어졌고, 다양한 환경에서 재사용할 수 있도록 설계하지 않았기 때문에 많은 컴포넌트가 '멋지게' 작동하지 않습니다.

'멋진' 컴포넌트란 무엇일까요? 
Vue 개발자들에게는 자연스럽게 작동하는 것을 의미하며, 모든 종류의 어플리케이션에서 통합하고 확장하기 쉬운 컴포넌트를 말합니다.


오픈 소스 컴포넌트를 살펴본 결과 '멋진' Vue 컴포넌트를 만드는 방법은:

1. `v-model` 호환성 구현
2. 알기 쉬운 이벤트
3. 엘리먼트에 정확하게 속성 등록하기
4. 브라우저 표준 채택
5. 콜백보다는 이벤트를 사용하기
6. 제한적인 내부 스타일

## `v-model` 호환성 구현

사용자가 데이터를 지정할 수 있으며 추가 기능이 붙어있는 단일 필드, 예를 들면 자동완성 검색이나 달력 날짜와 같이 기본적인 Form 형태인 컴포넌트의 경우, '멋진' 컴포넌트가 되기 위해 가장 중요한 것은 `v-model`을 지원하는 것입니다.

[Vue 컴포넌트 가이드](https://kr.vuejs.org/v2/guide/components.html#%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EC%9D%98-%EC%9D%B4%EB%B2%A4%ED%8A%B8%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EC%97%AC-%ED%8F%BC-%EC%9E%85%EB%A0%A5-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-%EB%A7%8C%EB%93%A4%EA%B8%B0)에 따르면 컴포넌트의 `v-model`은 기본적으로 `value` prop을 전달하고, `input` 이벤트 핸들러를 등록하여 작동합니다.

예를 들어, 만약 우리가 input에 date picker를 구현하려고 한다면, 우리는 datepicker에 `value` prop을 넘겨주고 날짜 선택 시 `input` 이벤트를 날려줍니다. 아래 코드를 봅시다.

```javascript
import datepicker from 'my-magic-datepicker';

export default {
  props: ['value'],
  mounted() {
    datepicker(this.$el, {
      date: this.value,
      onDateSelected: (date) => {
        this.$emit('input', date);
      },
    });
  }
}
```

## 알기 쉬운 이벤트
`v-model`을 구현하기 위해서 컴포넌트는 `input` 이벤트가 있어야 합니다. 그러나 클릭 이벤트나 키보드 이벤트처럼 다른 이벤트의 경우는 어떨까요? 네이티브 이벤트가 HTML에서 버블링 되지만, Vue의 이벤트는 버블링 되지 않습니다.
예를 들어, 아래 예제의 이벤트는 작동되지 않습니다.

```vue
<my-textarea-wrapper @focus="showFocus">
```

컴포넌트에서 `focus` 이벤트를 날려주는 코드를 작성하지 않았다면, `showFocus` 이벤트 핸들러는 절대 호출되지 않습니다. 하지만 Vue에서는 컴포넌트에 등록된 이벤트 리스너에 프로그래밍 방식으로 접근할 수 있도록 해줍니다. 바로 `$listeners` 객체입니다.

다시 생각해보면, 이유는 명백합니다. 이 방법은 이벤트 리스너를 컴포넌트의 정확한 위치에 전달해주거든요.
예를 들어서 textarea를 감싼 컴포넌트를 봅시다.

```vue
<div class="my-textarea-wrapper">
  <textarea v-on="$listeners" ></textarea>
</div>
```

이제 컴포넌트에 전달된 이벤트 리스너가 textarea에 잘 할당되어 작동합니다.

## 엘리먼트에 정확하게 속성 등록하기
textarea의 `rows` 속성이나 간단한 툴팁을 추가하기 위한 `title` 속성의 경우를 생각해 봅시다.

기본적으로 Vue는 컴포넌트에 등록된 속성들을 컴포넌트의 최상위 엘리먼트에 할당합니다. 이 방식은 자주 쓰이지만, 여러분이 항상 원하는 방식은 아닐 거에요. 
다시 위로 올라가서 textarea 컴포넌트를 살펴봅시다. 저 경우에는 `div` 태그보다는 `textarea` 태그에 속성이 할당되는 게 맞는 방식이죠.
루트 엘리먼트가 아닌, 특정 엘리먼트에 속성을 할당하기 위해서는 자동으로 속성을 할당하는 옵션을 꺼야 합니다. 그리고 `$attrs` 객체를 사용해서 엘리먼트에 직접 할당을 해주면 됩니다.

컴포넌트의 자바스크립트 부분

```javascript
export default {
  inheritAttrs: false,
}
```

템플릿 부분

```vue
<div class="my-textarea-wrapper">
  <textarea v-bind="$attrs"></textarea>
</div>
```

## 브라우저 표준 채택
접근성과 키보드 탐색은 웹 개발 과정에서 가장 깜빡하기 쉬운 부분이기도 하지만 '멋진' 컴포넌트를 만들기 위해서는 가장 중요한 부분이기도 합니다.
예를 들어 탭 키로 form 필드들을 선택하거나 enter 키로 버튼이나 링크를 활성화 하는 것들, 다시 말해서 접근성과 키보드 탐색은 여러분의 컴포넌트가 브라우저 표준을 준수하는지 확인하는 것을 의미합니다.

키보드 탐색 권장 사항 목록은 [W3C 웹사이트](https://www.w3.org/TR/wai-aria-practices/#aria_ex)에서 전부 확인할 수 있습니다.
이 권장 사항들을 따른다면 여러분의 컴포넌트는 접근성에 관심이 없는 사용자뿐 아니라 모든 어플리케이션에서 사용할 수 있습니다.

## 콜백보다는 이벤트를 사용하기

여러분의 컴포넌트에서 컴포넌트의 부모로의 데이터나 사용자 인터렉션에 대한 통신은 일반적으로 두 가지 옵션이 있습니다: 콜백 함수를 prop으로 넘겨주기, 그리고 이벤트입니다. Vue의 커스텀 이벤트는 네이티브 브라우저 이벤트처럼 버블링이 되지 않기 때문에 기능적으로는 비슷합니다. 하지만 재사용을 하기 위한 컴포넌트에서는 콜백을 넘겨주는 것보다는 이벤트 사용을 강력하게 권장합니다. 왜냐구요?

[Fullstack Radio의 에피소드](http://www.fullstackradio.com/87)중에서 Vue 핵심 멤버인 Chris Fritz가 얘기한 내용입니다.

1. 이벤트를 사용하는 것은 부모 컴포넌트가 확실하게 알 수 있도록 합니다. `부모로부터 받는 것`과 `부모로 보내는 것`을 완벽하게 분리할 수 있습니다.
2. 여러분은 표현식을 이벤트 핸들러 부분에서 바로 사용할 수 있습니다. 단순한 핸들링의 경우에는 아주 간단하게 처리할 수 있습니다.
3. 훨씬 자연스럽습니다. Vue 예제나 문서에서도 이벤트를 부모 간의 통신 목적으로 사용하는 경우가 있습니다.
다행히도 여러분이 현재 콜백 방식을 사용하고 있다면, 이벤트 방식으로 변경하는 것은 어렵지 않습니다.

콜백 방식을 사용하는 컴포넌트의 구조는 이렇습니다.

```javascript
// my-custom-component.vue
export default {
  props: ['onActionHappened', ...]
  methods() {
    handleAction() {
      ... // your custom code
      if (typeof this.onActionHappened === 'function') {
        this.onActionHappened(data);
      }
    }
  }
}
```

그리고 위 컴포넌트를 사용하는 방법

```vue
<my-custom-component :onActionHappened="actionHandler" />
```

이벤트 방식으로 변경하면 이렇습니다.

```javascript
// my-custom-component.vue
export default {
  methods() {
    handleAction() {
      ... // your custom code
      this.$emit('action-happened', data);
    }
  }
}
```

이벤트 방식 컴포넌트를 사용하는 방법

```vue
<my-custom-component @action-happened="actionHandler" />
```

## 제한적인 내부 스타일
Vue의 싱글 파일 컴포넌트의 구조는 컴포넌트 안에 직접 스타일을 적용할 수 있습니다. 특히 scoped 스타일은 어플리케이션의 다른 부분에 영향을 전혀 끼치지 않도록 완벽한 패키지를 만들 수 있도록 해줍니다. 

이 시스템의 장점 덕분에 컴포넌트의 모든 스타일을 style 태그에 넣으려고 할 수 있습니다. 문제는, 모든 어플리케이션 스타일이 같지 않다는 것이고, 어플리케이션에서 컴포넌트를 깔끔하게 만들기 위해서 했던 바로 그 점이 다른 사람의 어플리케이션에서는 혼자 두드러져 보일 수 있다는 것입니다.

이 부분을 방지하기 위해서 구조적으로 필요한 CSS 이외의 속성(색상, 테두리, 그림자 등)은 제거하거나 끌 수 있어야 합니다. 대신, 사용자가 원하는 대로 수정할 수 있도록 커스터마이징이 가능한 SCSS 부분을 포함하는 것을 고려해보는 것도 좋습니다.

SCSS만 포함하는 것의 단점은 사용자의 전체 스타일시트로 SCSS를 가져와야 한다는 것입니다. 그렇지 않으면 스타일이 빠져있는 구린 컴포넌트만 보일 테니까요. 양쪽 모두의 장점을 활용하려면, 스타일 커스터마이징을 원하는 사용자가 prop으로 클래스 명을 넘겨주어 컴포넌트 내부에서 스타일을 바꿔줄 수 있습니다.
만약 당신이 SCSS를 믹신으로 구조화했다면 사용자는 더 많은 커스텀 스타일에 사용할 수 있는 SCSS 믹신을 가져다 사용할 수 있습니다.

```vue
<template>
  <div :class="isStyledClass">
    <!-- my component -->
  </div>
</template>
```

컴포넌트의 자바스크립트 부분

```javascript
export default {
  props: {
    disableStyles: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    isStyledClass() {
    if (!this.disableStyles) {
      return 'is-styled';
    }
  },
}
```

CSS에서는 이렇게 사용하면 됩니다.

```css
@import 'my-component-styles';
.is-styled {
  @include my-component-styles();
}
```

이렇게 하면 즉각적인 스타일링을 원하는 대로 할 수 있지만, 커스터마이즈를 원하는 사용자는 높은 명시도의 css선택자를 오버리이딩 할 필요가 없어집니다. 그냥 `disableStyles` prop으로 스타일을 해제하고 다른 mixin을 사용하거나 아예 처음부터 스타일을 다시 만들 수 있습니다.