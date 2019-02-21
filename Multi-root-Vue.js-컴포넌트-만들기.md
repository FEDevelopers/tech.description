> 이 문서는 https://zendev.com/2018/05/07/multi-root-vue-components.html 를 번역한 글입니다.

# Multi-root Vue.js 컴포넌트 만들기

Vue.js 같은 컴포넌트 기반 프레임워크의 공통적인 제약은 각 컴포넌트가 싱글 루트 요소를 가지는 것이다. 이 의미는 특정 컴포넌트안에 모든것은 단일 요소로부터 내려와야 한다는 것이다.

```` js
<template>
  <div> <!-- The root -->
    <span></span> <!-- now we can have siblings -->
    <span></span>
  </div>
</template>
````

다음과 같이 템플릿으로 컴포넌트를 만들 수 있다.

```` js
<template>
  <span></span> <!-- two siblings at the top level of the hierarchy! -->
  <span></span>
</template>
````

그리고 당신은 무서운 에러를 볼 것이다.: `Component template should contain exactly one root element. If you are using v-if on multiple elements, use v-else-if to chain them instead.`

대다수 상황에서는 이 제약조건은 문제가 되지 않는다. 2개 요소를 가져야할 것이 있을까? DOM 계층에 다른 레이어를 추가하고, `div` 를 램핑 하면 간단하다. 문제 없다.

그러나 DOM의 구조가 매우 중요한 상황에서 간단하게 계층 레이어를 추가 할 수 없는 상황이 있다. 예를 들면 - 난 최근에 항상 각각 옆에 위치해 있는 두개의 `<td>`가 있는 프로젝트를 가졌었다. 하나를 포함하면, 다른 하나도 포함해야 했다. 논리적으로, 그것들은 단일 컴포넌트지만, 난 `<td>` 요소가 다이렉트로 `<tr>` 의 자손으로 작동해야 해서 랩핑을 할 수가 없었다. 

## 솔루션: 함수형 컴포넌트's

이 문제의 솔루션은 Vue.js 세부적인 구현에 있다. 현재 Vue가 다중 루트 컴포넌트를 못하는지에대한 주요 이유는 템플릿 렌더링 매커니즘이다. - 컴포넌트에 템플릿은 추상구문트리(AST) 로 파싱하여 AST엔 루트가 필요하다. 

> 만약 템플릿 렌더링을 피하면, 단일 루트 제한을 피할 수 있다.

일반적으로 적게 사용되지만, `render` 함수를 정의하면 템플릿 없이 Vue.js 컴포넌트를 구현 할 수 있다. 함수형 컴포넌트로 알려진 이런 컴포넌트는 단일 컴포넌트에 다중 루트를 렌더링을 포함하여 무수한 용도로 사용 할 수 있다. 

### 코드

단순하게, 난 각 짝인 단일-파일 컴포넌트로서 `<td>` 요소 를 작성했다. 그리고 그것들의 props를 전달하는 함수형 컴포넌트로 랩핑하였다. 

```` js
/* paired-cell.js */
import FirstCell from '~/components/paired-cell/first-cell';
import SecondCell from '~/components/paired-cell/second-cell';

export default {
  functional: true,
  props: ['person', 'place', 'thing'],
  render(createElement, context) {
    const first = createElement(FirstCell, { props: context.props });
    const second = createElement(SecondCell, { props: context.props });

    return [first, second];
  },
};
````

`FirstCell` 과 `SecondSell` 표준 Vue 단일 파일 컴포넌트이고, 각각 `<td>` 요소를 루트로 사용한다. 그러나 `PairedCell` 은 다르다. - 함수형 컴포넌트를 `exports` 하는 순수 자바스크립트 파일이다. 

함수형 컴포넌트와 전통적인 컴포넌트간의 2가지 주요 차이점이 있다. 

1. 함수형 컴포넌트는 **stateless** 이다. (자기 자신의 `data` 를 가지고 있지 않아서, 출력값은 전달된 `props`에 따라서 정의 된다.)
2. 함수형 컴포넌트는 **instanceless** 이다. 이 의미는 `this` 컨텍스트가 없고, 대신 `props` 와 관련된 값이 컨텍스트 객체 통해서 전달된다. 

코드가 무엇을 하는지 보면, 컴포넌트는 함수형 상태이고, props(person, place, thing)를 세팅하고 `createElement`, `context` 를 아큐먼트로 넘기는 `render` 함수를 정의한다. 

두 인자는 Vue 가 제공해 준다. `createElement`는 Vue의 가상 DOM에 요소를 세팅 할 수 있는 함수이다. 요소의 속성을 [직접 전달](https://vuejs.org/v2/guide/render-function.html#createElement-Arguments) 할 수 있다. 하지만, 이 경우 하위 컴포넌트를 렌더링 하는데 간단히 사용 할 수 있다. 

두번째 인자는 컴포넌트의 컨텍스트를 포함한다. 이 예제에선, 우리가 전달하는 prop 대해서만 신경써야하지만, 하지만 children, slots, parent등 더많은 것들이 포함되고 있다. - 컴포넌트 구현에 필요한 모든것들.

그래서 우리가 하는 것들을 무너뜨리기 위해서 - 우린 설정된 props 를 받아들이는 컴포넌트를 구현하고, 두개 자식 컴포넌트를 렌더링 하고, 배열로 그것들을 반환한다. 와우 다중 루트 컴포넌트다!