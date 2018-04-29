> 이 문서는 https://reactjs.org/blog/2017/09/08/dom-attributes-in-react-16.html 를 번역한 내용입니다.


과거 알 수 없는 DOM 속성의 경우 리액트에서는 렌더링이 무시됐었다. 만약 당신이 JSX로 속성을 썼었다면 리액트는 인지하지 못하고 그 부분을 지나쳤었다. 예를 들면 이렇다.

``` javascript
// Your code:
<div mycustomattribute="something" />
```

리액트 15 버전에서는 빈 div로 렌더링 된다.

``` javascript
// React 15 output:
<div />
```

리액트 16 버전에서는 이 부분에 변화가 생긴다. 이제부터는 알려지지 않은 어떠한 속성이건 DOM에 나타난다.

``` javascript
// React 16 output:
<div mycustomattribute="something" />
```

## 우리가 이 부분을 변경한 이유


리액트는 항상 자바스크립트 중심의 API를 DOM에 제공했었다. 리액트 컴포넌트는 커스텀 속성과 및 DOM 관련 속성을 모두 사용하기 때문에 리액트가 DOM API와 마찬가지로 `CamelCase` 규칙을 사용하는 것이 좋다.

``` javascript
<div tabIndex="-1" />
```

이 부분은 변하지 않는다. 하지만, 우리가 과거에 유지했던 방식은 번들 산출물에 존재하는 모든 유효한 리액트 DOM 속성 화이트리스트를 유지하게 만들었다. 

``` javascript
// ...
summary: 'summary',
tabIndex: 'tabindex'
target: 'target',
title: 'title',
// ...
```

여기(리액트 15버전 이하)에는 2가지 단점이 있었다.

- 당신은 [커스텀 속성을 전달](https://github.com/facebook/react/issues/140)할 수 없었다. 이것은 브라우저 별 비표준 속성을 제공하고, 새로운 DOM API를 시도하고, 써드파티 라이브러리와 통합 할 때 유용하다.


- 속성 목록(화이트리스트)은 시간이 지남에 따라 계속 증가했지만, 대부분 대부분의 리액트 표준 속성 이름은 이미 DOM에서 유효하다. 화이트리스트의 대부분을 제거하는 것은 번들 산출물의 크기를 줄이는데 약간 도움이 되었다.


새로운 접근 방식으로 이 2가지의 문제가 해결되었다. 리액트 16 버전을 사용하게 되면 당신은 모든 HTML, SVG 요소에 사용자 속성을 전달할 수 있게된다. 그리고 리액트는 더이상 production 버전에 전체 화이트리스트를 가질 필요가 없게된다.


#### 기존에 이미 알려진 속성에 대해서는 표준 리액트 네이밍을 사용해야한다.

``` javascript
// Yes, please
<div tabIndex="-1" />

// Warning: Invalid DOM property `tabindex`. Did you mean `tabIndex`?
<div tabindex="-1" />
```

즉, 리액트에서 DOM 구성 요소를 사용하는 방식은 변경되지 않았지만, 이제 새로운 기능이 추가되었다.


## 데이터를 커스텀 속성에 보관해야 합니까?


아니오. 우리는 DOM 속성에 데이터를 유지하는 방식을 권장하지 않는다. data 속성이 아마도 더 나은 방식임에도 불구하고, 대부분의 경우 데이터는 리액트 컴포넌트의 state 혹은 외부 스토어에 보관되어야한다.


그러나 당신이 비표준 혹은 새로운 DOM 속성을 사용해야하는 경우, 혹은 그 속성에 의존하는 써드파티 라이브러리와 통합해야하는 경우에는 이번 새로운 기능이 편리하다.

## Data, ARIA 속성

이전과 마찬가지로 리액트는 `data-`와 `aria-` 속성을 자유롭게 사용하게 해준다.

``` javascript
<div data-foo="42" />
<button aria-label="Close" onClick={onClose} />
```

이 부분은 변하지 않는다.


[접근성](https://reactjs.org/docs/accessibility.html)은 매우 중요하다. 그러므로 리액트 16 버전이 어떠한 속성을 전달할지라도 `aria-` 속성이 개발모드에서 정확한 이름을 가지고 있는지 여전히 검증한다. 마치 리액트 15 버전에서 해왔던 것과 동일하다.

## Migration Path

우리는 1년 전에 나온 리액트 15.2.0 버전부터 알 수 없는 속성 사용에 대한 경고를 포함했었다. 대다수의 써드 파티 라이브러리는 이미 그들의 코드를 업데이트 했다. 만약 리액트 15.2.0 버전 혹은 그 이상을 쓰고 있는 당신의 프로그램에서 경고가 뜨지 않는다면 당신의 프로그램 코드를 수정하지 않아도 된다는 의미이다.


만약 당신이 실수로 DOM 요소가 아닌 속성을 DOM 컴포넌트에 전달한 경우, 리액트 16 버전에서는 DOM에서 이러한 속성이 보일 것이다. 예를 들면,

``` javascript
<div myData='[Object object]' />
```

이것은 브라우저가 무시할 것이기 때문에 안전하긴 하지만 수정하는 것을 추천한다. 한 가지 잠재적인 위험은 만약 커스텀 `toString()` 혹은 `valueOf()` 메소드를 통해 객체를 전달했을 때이다. 또다른 가능성 있는 문제로 `align`이나 `valign`과 같은 레거시 HTML 속성들이 이제는 DOM에 전달될 수 있다는 것이다. 과거 그것들은 리액트에서 지원되지 않았기 때문에 제거된 상태로 렌더링 되었다.


이러한 문제들을 피하기 위해, 리액트 15버전에서 보이는 경고를 리액트 16 버전으로 업그레이드 하기 전에 수정하길 추천한다. 


## 자세한 변경사항들


우리는 문제를 보다 예측 가능하게 만들고, 실수하지 않도록 몇 가지 다른 사항을 변경했다. 우리는 이 변경으로 실제 운영 중인 프로그램에 오류가 발생할 것이라는 생각은 하지 않는다.

### 이 변경은 개발 컴포넌트가 아닌, 오직 div와 같은 DOM에만 영향을 미친다.


다음은 그것들의 상세한 목록입니다.


- 알 수 없는 속성에 대한 문자열, 숫자, 객체 값이 들어올 경우:
``` javascript
<div mycustomattribute="value" />
<div mycustomattribute={42} />
<div mycustomattribute={myObject} />
```

리액트 15 버전: 경고 후 무시.<br>
리액트 16 버전: 문자열로 값 변경 후 DOM에 전달.<br>
참고: on으로 시작하는 속성은 잠재적인 보안 문제가 될 수 있으므로 예외 처리 후 전달되지 않습니다.


- 리액트에 정의된 것과 다른 이름으로 사용된 알려진 속성일 경우:
``` javascript
<div tabindex="-1" />
<div class="hi" />
```
리액트 15 버전: 경고 후 무시.<br>
리액트 16 버전: 경고하지만, 문자열로 값 변경 후 DOM에 전달.<br>
참고: 지원되는 모든 속성에 대해 항상 리액트 네이밍을 사용.


- 불리언을 가지지 않는 속성에 불리언 값이 들어갔을 경우: 
``` javascript
<div className={false} />
```
리액트 15 버전: 문자열로 값 변경 후 DOM에 전달.<br>
리액트 16 버전: 경고 후 무시.<br>

- 이벤트가 아닌 속성에 함수 값이 들어갔을 경우:
``` javascript
<div className={function() {}} />
```
리액트 15 버전: 함수를 문자열로 변경 후 DOM에 전달.<br>
리액트 16 버전: 경고 후 무시.<br>


- 속성에 심볼 값이 들어갔을 경우:
``` javascript
<div className={Symbol('foo')} />
```
리액트 15 버전: 오류(크래쉬).<br>
리액트 16 버전: 경고 후 무시.<br>

- 속성에 NaN 값이 들어갔을 경우:
``` javascript
<div tabIndex={0 / 0} />
```
리액트 15 버전: NaNs를 문자열로 변경 후 DOM에 전달.<br>
React 16: 경고 후 NaNs를 문자열로 변경 후 DOM에 전달. <br>


이번 배포를 테스트하는 동안 잠재적 회귀를 추적하기 위해 [모든 알려진 속성에 대해 자동으로 생성된 테이블](https://github.com/facebook/react/blob/master/fixtures/attribute-behavior/AttributeTableSnapshot.md)을 만들었습니다.
