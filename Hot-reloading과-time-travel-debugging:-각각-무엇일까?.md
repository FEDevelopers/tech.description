> 해당 문서는 https://code-cartoons.com/hot-reloading-and-time-travel-debugging-what-are-they-3c8ed2812f35#.h493rgis2 를 번역한 내용입니다.


리덕스에 대해 많은 사람들이 열광하는 두 가지 특징이 hot reading과 time travel debugging이라는 것이다. 하지만 각각은 무엇일까?


## Hot reloading


우리는 어플리케이션을 개발할 때, 종종 작은 변화가 일어난다. 그 변화가 적을수록, 점점 더 빠르게 개발할 것이고, 실수로부터 금방 회복할 수 있다.


hot reloading의 장점은 화면이 바뀔때마다 당신의 앱 state 정보가 초기화되지 않는다는 점이다. 당신이 특정 화면을 테스트한다고 가정해보자. 투두 아이템을 추가하고, 그 중 몇 개를 완료하는 상황이다. 하지만 몇몇 글자를 수정해야한다는 것을 알게 되었다. 예를 들어, "New toto"에서 "New todo"로 수정이 필요한 상황이다.


hot reloading이 없는 상황에서는 이렇게 할 것이다.


- 코드를 수정하고
- 브라우저의 페이지를 새로고침 한 뒤,
- 다시 데이터를 채워넣고, 이전과 동일한 버튼들을 눌러야 한다. **_(역자주: 디비 연동이 없는 상황으로 추정)_**


![todo](https://cdn-images-1.medium.com/max/800/1*6xdA93PT8l61i0ptXVWpOw.png)

> hot reloading이 없으면 페이지 새로고침을 했을 때 투두 항목들이 다 날아간다.


hot reloading을 사용하면, 데이터를 다시 채워넣을 필요가 없다. 왜냐하면 state를 잃지 않기 때문이다. 당신의 투두 리스트는 여전히 그대로 있을 것이다. 이 기능은 디버깅을 더욱 빠르게 도와줄 것이다.


만약 리액트 앱이 리덕스를 데이터로 사용하지 않더라도, hot reloading을 할 수 있다. 단지 hot reload를 하는 것에 제한이 생길 뿐이다. view와 action creators는 hot reload될 수 있지만, store는 그럴 수 없다. 스토어에는 두 가지 규칙이 있기 때문이다. action에 기반하여 state를 바꾸는 것과 state를 유지하는 것이다. 만약 state가 수정되는 코드로 새로고침 된다면, 현재 스토어가 유지하고 있는 state를 잃게 될 것이다.


state 로직을 hot reload 할 수 있는 기능, 그게 바로 리덕스가 제공하는 것이다. 그리고 두 가지의 역할을 분리함으로써 그렇게 한다. 리덕스에서, store는 state를 유지하고, reducer라는 다른 객체에서는 state를 바꾸는 로직을 수행한다. 즉, 리듀서를 통해 store에 있는 state를 잃지 않고, state 변경 로직을 hot reload 할 수 있다.



![store](https://cdn-images-1.medium.com/max/800/1*Z5Na_SX0yHZYMSTpXIrxEQ.png)

> 리덕스의 hot reloading 기능으로 어플리케이션 state를 잃지 않는다.


## Time travel debugging


hot reloading은 코드 수정이 일어나도 state를 유지할 수 있게 해준다. time travel debugging 기능으로는 이전 지점의 state로 뒤돌아갈 수 있다.


이것은 특정 화면에서의 인터렉션 테스트를 더 빠르게 해준다. 예를 들어, 화면에 버그가 있다 치자. 이건 하나의 투두를 완료하고, 다른 투두를 추가하는 상황에서만 일어나는 버그다. 최초 state를 설정하기 위해 전체 시나리오대로 한 번 실행한다. 그 후, 버그가 발생하면 한 단계 직전으로 돌아가서 두 번째 투두를 추가하며 테스트를 계속 할 수 있다.


![time travel debugging](https://cdn-images-1.medium.com/max/800/1*E8b-Kwelt7YUMsS7GuSQYw.png)

> Time travel debugging 기능은 state history에서 다른 지점으로 쉽게 이동할 수 있게 해준다. 


이 기능을 활용하는 다른 좋은 예가 있다. QA 테스터의 모든 작업을 기록할 수 있다. 만약 QA 테스터가 버그를 발견하게 되면 action과 state history를 따로 기록하기만 하면 된다. 자동화 테스트에서도 적용 가능하다.


Flux에서도 Time travel debugging이 기술적으로 가능하긴 하지만, 약간 복잡하다. 리덕스에서는 쉽다.