> 이 글은 [https://www.sitepoint.com/object-oriented-javascript-deep-dive-es6-classes/](https://www.sitepoint.com/object-oriented-javascript-deep-dive-es6-classes/)을 번역한 글입니다.

#서문

종종 우리는 자동차 엔진, 컴퓨터 파일, 라우터, 또는 온도계같은 프로그램에서 우리의 아이디어나 개념을 표현 할 필요가 있습니다. 코드에서 이런 개념을 나타내는 것은 상태를 표현하는 데이터와 행동을 표현하는 함수 이 두가지로 나뉩니다. 클래스는 우리의 개념을 표현 할수 있는 객체의 상태와 행동을 정의하는 편리한 구문을 제공합니다. 그것들은 초기화 함수가 불러지는 것을 보장함으로써 우리의 코드를 안전하게 만들어며, 데이터를 조작하고, 유효한 상태를 유지하는 함수 집합을 쉽게 정의 할 수 있게 해줍니다. 만약 별도의 엔티티로써 *어떤'것'*을 생각한다면, 당신의 프로그램에 그 *어떤'것'*을 나타내기 위한 클래스를 정의 해야 합니다.  
아래 클래스 코드가 아닌 것을 보십시오. 얼마나 많은 오류를 찾을 수 있겠습니까? 그럼 어떻게 그것을 해결 하겠습니까?

``` javascript
// set today to December 24
let today = {
  month: 12,
  day: 24,
};

let tomorrow = {
  year: today.year,
  month: today.month,
  day: today.day + 1,
};

let dayAfterTomorrow = {
  year: tomorrow.year,
  month: tomorrow.month,
  day: tomorrow.day + 1 <= 31 ? tomorrow.day + 1 : 1,
};
```