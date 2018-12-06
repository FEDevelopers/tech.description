> 이 문서는 https://www.smashingmagazine.com/2018/11/interactive-infographic-vue-js/ 를 번역한 글입니다.

# Building An Interactive Infographic With Vue.js

이 문서는 인터랙티브 인포그래픽을 만들기 위한 모던한 접근방식을 제공합니다. 사용자 인터렉션 없이 미리 모든 정보를 이용 할 수 있는 단순한 인포그래픽을 가질 수 있다. 인터랙티브 경험을 구축하기 위한 생각은 우리가 선택했던 기술 환경을 변화시킨다. 그러므로 첫번째 이해해야 할 것은 왜 Vue.js인지? 그리고 GSAP 와 SVG가 분명하게 선택되어졌는지 알 수 있을 것이다.

Vue.js는 어디서든 DOM을 파워풀한 방법으로 처리하고 다룰 수 있게 할 수 있는 동적 사용자 인터페이스를 컴포넌트 기반으로 구축하기 위한 실용적인 방법을 제공한다. 이경우엔 SVG일 것이다. 당신은 Vue.js에서 이용할 수 있는 기능 중 일부를 사용하 다른 SVG 요소를 동적으로 쉽게 다루고 업데이트 할 수 있다. 여기 주요 기능중 일부는 데이터 바인딩, 리스트 렌더링, 동적 클래스 바인딩등의 몇가지 가 있습니다. 또한 관련된 SVG요소를 함께 그룹화하고, 컴포넌트화 할 수 있다.

Vue.js 는 영광을 잃지 않기 위해 외부 라이브러리가 나이스하게 작동한다. 즉 GSAP 이다. Vue.js 를 사용하면 다른 많은 이점이 있다. 그 중 하나는, Vue.js 는 각 컴포넌트 관련 템플릿, 스크립트, 스타일을 독립 시킬 수 있다. 이렇게 하면, Vue.js 가 모듈화 어플리케이션 구조로 되어집니다. 

Vue.js는 강력한 라이프 사이클 훅과 함께 제공된다. 어플리케이션의 행위를 수정하기 위한 다양한 어플리케이션 단계를 활용 할 수 있다. Vue.js 어플리케이션을 유지하고 셋업 하는데 큰 노력은 필요로 하지 않는다. 단계적으로 프로젝트를 스케일업 할 수 있다.

인포그래픽은 매우 가벼운 시청각 요소다. 이 기사의 목표는 데이터 관점에서 생각하는 방법과 시각 요소, 그리고 물론 Vue.js또한, 이 프레임워크는 모든 인터렉티브한 것을 가능하게 만든다.

자 이제 우린 다음과 같이 시작할 것이다.

1. 인포그래픽을 위한 데이터 개요
2. SVG 이미지 준비
3. SVG 작업 컨텍스트에서 Vue 컴포넌트 개요
4. 주요 인터랙티브의 다이어그램과 코드 샘플

우리는 프랑스에서 열리는 연례 행사인 Tour De France 인포그래픽을 만들 것이다.

![Tour De France  —  Interactive bicycle listing game stages (rear-wheel) and participating teams (front-wheel). (Large preview)](https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/73d018bd-2319-4fdd-a352-b41599588c67/1-feature-image.png)
Tour De France  —  Interactive bicycle listing game stages (rear-wheel) and participating teams (front-wheel). (Large preview)

## Tour De France 데이터 개요

인포그래픽에서 데이터는 인포그래픽 디자인을 주도한다. 그래서 주어진 주제 대한 모든 데이터, 정보, 통계를 가지는 것은 좋은 생각이다. 2017 Tour De France에서 하는 가장 큰 사이클 이벤트에 대해 배웠었고, 7월 21일 경기에서 나는 그 주제에 대해 많이 친숙해져었다.

나의 디자인에서 사용할 경주 기본 요소를 결정 한것들은 다음과 같다.

- 스테이지
- 팀
- 경로
- 승자
- 각 경로의 길이 와 분류

이 다음 파트는 생각하는 스타일에 따르게 되는 과정이라 당신의 창의력을 발휘 할 수 있다.

나는 두 셋트 데이터를 만들었다, 하나는 스테이지, 다른 하나는 팀을 위한 것이다.

이 두 데이터셋은 자전거의 2개 휠과 각 활대와 매칭되는 다중 행(제한적인)을 가지고 있다. 그리고 디자인의 주요 요소를 정의 했고, 당신이 처음에 봤던 자전거 그림에서 각 활대는 화면 보이는 정보 따라 인터렉티브하고 유동적일 것이다.

난 위에서 **제한적**이라는 걸 언급했었다.왜냐면, 우리가 목표로 하는 것은 빅데이터 컨텍스트에서 본격적인 데이터 시각화가 아니고, 고수준 데이터로 인포그래픽 하는 것이다. 그러므로, 데이터와 양질의 시간을 보내고, 당신의 시각 스토리를 전달 하기 위해 유사점, 차이점, 계층 또는 트렌드를 찾아야 한다. 그리고 데이터 요소(SVG 아트웍), 인터렉티브(Vue.js), 정보(데이터) 간의 올바른 균현을 도와주는 Vue.js와 SVG의 어메이징한 조합을 잊지 말아야 한다.

여기 스테이지 데이터 객체 스니펫이 있다.

````
{
    "ID": 1,
    "NAME": "STAGE 01",
    "DISTANCE": "14",
    "ROUTE": "KMDÜSSELDORF / DÜSSELDORF",
    "WINNER": "THOMAS G.",
    "UCI_CODE": "SKY",
    "TYPE": "Individual Time Trial",
    "DATE": "Saturday July 1st",
    "KEY_MOMENT": " Geraint Thomas takes his first win at 32"
}
````

그리고 팀 데이터 객체 스니펫 이다.

````
{
    "ID": 1,
    "UCI_CODE": "SKY",
    "NAME": " TEAM SKY",
    "COUNTRY": "Great Britain",
    "STAGE_VICTORIES": 1,
    "RIDERS": 8
}
````

이 인포그래픽은 간단한 로직으로 작동 된다.

`UNI_CODE` 는 각 스테이지와 팀 객체에 키로 연결 되어져있다. 스테이지를 클릭하면 첫번째로 스테이지가 활성화 되고, `UCI_CODE` 로 승리한 해당 팀을 활성화 한다.

## SVG 준비

2개 데이터셋와 러프한 자전거 그림이 준비되었고, 인포그래픽의 정적 SVG CodePen 이 다음과 같다.

<iframe height='291' scrolling='no' title='Static Bicycle SVG' src='//codepen.io/krutie/embed/QZMVOX/?height=291&theme-id=0&default-tab=html,result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/krutie/pen/QZMVOX/'>Static Bicycle SVG</a> by Krutie (<a href='https://codepen.io/krutie'>@krutie</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

우리는 데이터셋에서 레코드당 나머지 활대를 동적으로 생성하고 *GreenSock* 라이브러리로 애니메이션 하기 때문에, 휠당 하나의 활대만 만들었습니다. 

SVG코드를 만드는 작업도 매우 간단하다. 어도비 일러스트레이터에서 인포그래픽 작업을 하고 SVG로 저장하면 된다. 각 일러스트레이터 작업할 때 각 그룹과 레이어의 이름을 지정해야한다. Vue 컴포넌트의 `<template>` 영역에 둘 SVG 코드 부분들을 위해 `Id` 가 필요하기 때문이다. 일러스트레이터에 준 레이어 이름을 기억하여 SVG 마크업 요소 ID에 지정하라. 또한 [SVGOMG](https://jakearchibald.github.io/svgomg/) 사용할 수 있고, 어도비 일러스트레이터에서 추출한 SVG 코드를 더욱더 최적화 할 수 있다.

중요 사항: 만약 당신이 SVG 마크업을 최적하하려고 SVGOMG 를 사용한다면, 코드는 확실히 깔끔하게 보이지만, 모든 `<rect>` 요소를 `d` 속성 으로 `<path>` 로 변환하게 될 것이다. 나중에 몇픽셀을 수동으로 조정 할 경우에, 사각형의 x, y 값을 잃어버리게 됩니다.

두번째로 `Clear Id` 옵션 체크를 해제 해야 한다.(SVGOMG 인터페이스의 오른쪽 옵션) 그러면 일러스트레이터에서 생성된 모든 `group`과 `ids` 를 유지 할 수 있다.

## Vue 컴포넌트 개요

인포그래픽 프로젝트에서 인터랙티브와 데이터 흐름이 간단하더라도 항상 컴포넌트의 트리 다이어그램 그리는데 시간이 걸린다. 당신은 항상 컴포넌트의 다이어그램 그리는데 잠시 시간을 써야 한다.

이는 당신이 자식 컴포넌트가 부모 컴포넌트(props을 통해서) 또는 그 반대로(`this.$emit` 이벤트)에서 보낸 값에 의존하는 공유-데이터 메커니즘을 사용하지 않는 경우 도움이 될것이다. props값, `emit` 이벤트와 지역 데이터를 *brainstorm* 하고 코드를 작성하기전에 문서화 할 수 있는 기회이다.

![vue component tree](https://res.cloudinary.com/indysigner/image/fetch/f_auto,q_auto/w_2000/https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/5e20506d-a3e6-4a37-8c19-a54f734baefd/2-vue-component-tree-large.jpeg)

위 다이어그램은 인터렉티브 요구사항으로 부터 부분적으로 파생된 그리고 부분적으로 SVG 마크업 기반인 Vue 컴포넌트 스냅샷이다. 당신은 트리 구조 기반으로 어떻게 SVG 마크업을 분리 할 수있는지 볼 수 있어야 한다. 이것은 계층 구조 관점에서 상당히 자명하다.

1. 체인 휠은 활대 회전을 따라 할 것이다.
2. 스테이지 컴포넌트는 21단계 리스트를 나타내는 후륜 휠이다.
3. 스테이지-디테일 컴포넌트는 곡선 경로(왼쪽부분)와 관련된 정보를 표시 할 것이다.
4. 팀-디테일 컴포는트는 곡선 경로(오른쪽부분)와 관련된 정보를 표시 할 것이다.
5. 네비게이션은 스테이지에 접근하기 위한 이전, 다음 버튼이 포함 될 것이다.

아래 다이어그램은 위에서 본 Vue 컴포넌트를 표현하지만, 인포그래픽 디자인 컨텍스트이다.

![vue components blended into SVG](https://res.cloudinary.com/indysigner/image/fetch/f_auto,q_auto/w_2000/https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/40b44397-fe45-477e-a3ae-ce437e7414bf/3-vue-components-blended-into-svg.jpeg)

Less is more - 비슷한 프로젝트를 할때에 해야할 접근 방식이어야 한다. *TweenMax* 대신 *TweenLite* 를 사용 할 수 있다면, *animation* 및 *transition* 요구사항을 생각 해봐야 한다. 만약 당신이 복잡한 것 말고 간단한 경로나, 초보적인 모양을 선택 할 옵션이 있다면, 모든 수단을 이용하여 성능에 페널티 없이 쉽게 animate 할 수 있는 가벼운 요소를 선택해라.

다음 섹션에서는 GreenSock 애니메이션과 Vue.js 로 흥미로운 부분을 해볼 것이다.

## GreenSock Animation

자 이제 자세히 살펴 보자.

1. 곡선 경로에 텍스트 애니메이션
2. 휠에 활대 애니메이션

### 곡선 경로에 텍스트 애니메이션

자전거 바퀴 주변에 곡선 경로를 기억해라, 그 곡선 경로는 자전거 휠 반경보다 조금 더 크다. 그러므로, 이 경로에서 텍스트를 애니메이션 하면 휠 모양으로 따라가게 되는 것처럼 보일 것이다.

<iframe height='265' scrolling='no' title='Text on a Curved Path' src='//codepen.io/krutie/embed/zmPzvj/?height=265&theme-id=0&default-tab=html,result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/krutie/pen/zmPzvj/'>Text on a Curved Path</a> by Krutie (<a href='https://codepen.io/krutie'>@krutie</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

`path` 와 `textPath` 는 ` xlink:href` 속성을 사용 하여 어느 경로든 텍스트를 세팅 할 수 있는 SVG 요소의 좋은 조합이다.

```` html
<path id="curvedPath" stroke="none" fill="none" d="..."/>

<text>
  <textPath xlink:href="#curvedPath"
          class="stageDetail"
          startOffset="0%">
          {{ stage.KEY_MOMENT }}
   </textPath>
</text>
````

경로를 따라서 텍스트를 애니메이션화하려면, 단순히 GreenSock 사용하여 `startOffset` 속성을 애니메이션화 하는것이다.

```` js
tl.fromTo( ".stageDetail", 1,
{
  opacity: 0,
  attr: { startOffset: "0%" }
},

  opacity: 1,
  attr: { startOffset: "10%" }
}, 0.5 );
````

`startOffset` 비율을 증가시킴으로서, 텍스트는 원형 둘레를 따라 지나갈 것이다.

우리 마지막 프로젝트에서 어느 활대든 클릭하면, 매번 애니메이션이 트리거 된다. 자 이제 애니메이션의 흥미로운 부분으로 가자.

### 바퀴안에 스테이지/활대 애니메이션

데모에서 봤듯이, 스테이지와 팀 컴포넌트는 본질적으로 몇가지 작은 차이점과 유사함을 볼 수 있다. 자전거 바퀴 한개를 봐보자.

아래 CodePen 예제에서 3가지 주요 아이디어를 봐보자.

1. 스테이지 데이터 패치
2. 데이터 기반으로 동적으로 활대 정렬
3. 스테이지(활대) 클릭시 활대 재 정렬

<iframe height='265' scrolling='no' title='TDF Wheel Animation ' src='//codepen.io/krutie/embed/xyLmdR/?height=265&theme-id=0&default-tab=js,result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/krutie/pen/xyLmdR/'>TDF Wheel Animation </a> by Krutie (<a href='https://codepen.io/krutie'>@krutie</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

위 SVG CodePen 에서 활대는 SVG 직사각형과 텍스트가 그룹화된 것임을 알 수 있다. 텍스트와 활대가 함께 애니메이션 되기 위한 목적으로 그룹화 하였다.

```` html
<g v-for="stage in stages" class="stage">

    <rect x="249" y="250" width="215" height="1" stroke="#3F51B5" stroke-width="1"/>

    <text transform="translate(410 245)" fill="#3F51B5" >
      {{ stage.NAME }}
    </text>

</g>
````

데이터 소스에서 패치한 값을 사용하여 Vue 컴포넌트 `<template>` 부분에 렌더링 할 것이다.

총 21스테이지가 화면에서 나오면, `setSpokes()`를 호출 하여 초기 포지션을 세팅할 것이다.

```` js
// setSpokes()

let stageSpokes = document.querySelectorAll(".stage")
let stageAngle = 360/this.stages.length

_.map(stageSpokes, (item, index) => {
    TweenMax.to(item, 2, 
    { rotation: stageAngle*index, 
      transformOrigin: "0% 100%"
    }, 1)
}
````

스테이지 3가지 주요 요소는 다음과 같다.

1. rotation
    - 활대를 회전시키기 위해, 모든 요소를 `className` 스테이지로 매핑하고 각 활대에 계산된 동적 회전 값을 세팅한다.
2. transform origin
    - 바퀴 중심에서 부터 "0% 100%" 만큼 각 활대 회전을 할 수 있기 때문에 위 코드에 `transformOrigin` 값을 인덱스 만큼 중요하게 표시한다.
3. stageAngle
    - 총 스테이지 갯수를 360도로 나눈 값을 사용하여 계산 된다. 이는 360도 원에서 모든 활대가 고르게 나열 되는데 도움 된다.

### 인터렉티브 추가

다음 단계로 각 스테이지에 클릭 이벤트를 추가하여 데이터 변경시 상호작용하고 인터렉티브하게 만들 도록 한다. 따라서 SVG 이미지에 생명을 불어넣을 것이다.

만약 스테이지/활대가 클릭됬을 때, `stageId` 파라미터를 사용하여 클릭 된 스테이지를 회전시키고, 활성화하는 역할을 수행하는 `goAnimate()` 가 실행 된다.

```` js
goAnimate (stageId) {

  // activate stage id
  this.activeId = stageId

  // rotate spokes

}
````

우리는 인터랙티브의 주요 요소인 [DirectionalRotationPlugin](https://greensock.com/docs/Plugins/DirectionalRotationPlugin) 을 사용 한다. `TweenMax` 가 포함되어 있다.

플러그인을 사용하는 3가지 방법이 있다. 시계방향, 반시계방향, 목적지까지 계산된 최단 거리 속성을 회전하는 애니메이션 을 한다.

지금 짐작했듯이 우리는 현재 스테이지와 새로운 스테이지 사이에 짧은 거리를 회전시키는 세번째 옵션을 하고 있다.

위 CodePen 을 보면, **스테이지 01** 이 원 주위를 계속 움직이는걸 볼 수 있다. 0도 에 새롭게 활성화 된 스테이지 위해 원래 지점을 떠나게 된다.

첫번째로, 클릭한 스테이지 각도를 구하고, **스테이지01** 과 회전을 통해 교환합니다. 그럼 클릭한 스테이지의 회전값은 어떻게 알 수 있을까? 아래 다이어그램을 확인하자.

![distance calcuration from stage 01 to the clicked stage](https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/e4837cf9-0c5c-4868-a13b-6e5a260d7ee1/4-distance-calculation-large.jpeg)

예를들어, 만약 **스테이지 05** 를 클릭하면(위에서 봤듯이) **스테이지 01** 에서 **스테이지 05** 로 이동에는 `4*각도값` 이 필요하다.

따라서 우리는 `(Active stage Id - 1) * 17` 도를 사용하여 정확한 각도를 얻을 수 있으며, `_short` 접미사로 방향 회전 플러그인을 트리거 할 수 있다.


```` js
angle = 360/21 stages = 17
activeId = 5
new angle = ((activeId-1)*angle)+'_short'
          = ((5-1)\*17)+'_short'
          = 68
````

마지막 `goAnimate()` 함수는 다음과 같다.

```` js
_.map(spokes, (item, index) => {

  if(activeId == index+1) { 
    // active stage
    TweenMax.to(item, 2, 
    { rotation: 0+'_short', 
      transformOrigin: "0 100%"
    })   

  } else if (index == 0) { 
    // first stage
    TweenMax.to(item, 2,
    { rotation: (activeId*angle)-angle+'_short',
      transformOrigin: "0 100%"
    })

  } else {
    TweenMax.to(item, 2, 
    { rotation: index*angle+'_short', 
      transformOrigin: "0 100%"
    })
  }

}) // end of map
````

뒷바퀴가 준비되었으면, 앞바퀴(팀용)는 같은 로직으로 따라가 하면 된다.

스테이지 대신에, 팀 데이터를 가져와, `transformOrigin` 속성 등록 포인트를 업데이트 하고 스테이지 휠 반대되는 등록 포인트로 활대가 생성되게 한다.

```` js
// set team spokes

map(teamSpokes, (index, key) => {
  TweenMax.to(index, 2, 
  { rotation: angle*key, 
    transformOrigin: "100% 100%"
  }, 1)
})
````

## 파이널 프로젝트

나와 같이, Vue 컴포넌트에 모든 애니메이션과 데이터 관련 함수를 작성 했다면, Vuex와 Mixins를 사용하여 깔끔하게 할 수 있다.

![](https://res.cloudinary.com/indysigner/image/fetch/f_auto,q_auto/w_2000/https://cloud.netlifyusercontent.com/assets/344dbf88-fdf9-42bb-adb4-46f01eedd629/3e298de5-57fa-4248-988b-387dbb99e1a7/5-vuex-with-vue-components.jpeg)

### Vuex

Vuex는 컴포넌트간 공유 데이터 관리가 쉬우며, 더 중요한건, 메서드와 `data()` 함수를 깔끔하게 유지하면서 코드를 간소화 하여, 데이터를 핸들링 하지않고 단순히 컴포넌트는 렌더링 하게 한다.

라이프사이클 훅은 HTTP 요청을 수행하기에 적절한 위치를 하게 해준다. 우리는 Vue 어플리케이션이 초기화 되었을때, 아직 DOM에 마운트 되지않은 시점에 `created` 훅에서 초기 데이터를 패치한다.

빈 상태변수 `stages` 와 `teams` 는 이 단계에서 `mutation` 을 사용하여 업데이트 된다. 그러면 우리는 `watcher`(한번만) 를 사용하여 이 두변수를 추적하고 업데이트 되면, 애니메이션 스크립트를(mixin.js) 호출 한다.

매시간 사용자는 스테이지와 팀 컴포넌트와 상호 작용 한다. Vuex 스토어와 통신하고, `setActiveData` 를 실행하며 현재 스테이지와 팀 값을 업데이트 한다.

그리고 `state`가 업데이트된 후 활성 데이터가 세팅 되면, `goAnimate` 가 업데이트된 값을 사용하여 활대를 애니메이션한다.(방향 회전)

추천 읽을거리: [Creating Custom Inputs With Vue.js](https://www.smashingmagazine.com/2017/08/creating-custom-inputs-vue-js/)

### MIXINS

이제 데이터는 Vuex가 핸들링 하기에, 우리는 GreenSock 애니메이션을 분리 할 것이다. Vue 컴포넌트가가 긴 애니메이션 스크립트로 어지럽혀지는 것을 방지 할 수 있다. 모든 GreenSock 함수는 minxin.js 파일과 함께 그룹화 된다.

Mixins내에 Vuex Store에 접근 할 수 있으므로, 모든 GSAP 함수는 `state` 변수를 사용하여 SVG 요소에 애니메이션을 적용한다. `store.js` 와 `mixin.js` 전체 함수는 여기서 볼 수 있다. [CodeSandbox example over here.](https://codesandbox.io/embed/y2kx79mnrv)

## 결론

인터렉티브하고 멋진 인포그래픽을 만들기 위해선 데이터를 분석해야하고, 창의적인 비쥬얼과 효율적인 기술인 Vue.js 이어야 한다. 당신의 프로젝트에서 이 컨셉을 더 사용 할 수 있다. 마지막으로 우리가 했던 유사하게 만든 원형 인터렉티브 컬러 휠을 두겠다.

<iframe height='265' scrolling='no' title='Material UI Circular Colour Palette made with Vue JS and GSAP ' src='//codepen.io/krutie/embed/LjWRqZ/?height=265&theme-id=0&default-tab=js,result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/krutie/pen/LjWRqZ/'>Material UI Circular Colour Palette made with Vue JS and GSAP </a> by Krutie (<a href='https://codepen.io/krutie'>@krutie</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>