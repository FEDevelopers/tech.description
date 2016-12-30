> 이 문서는 https://medium.com/@cscalfani/so-you-want-to-be-a-functional-programmer-part-6-db502830403#.lmm1riwt1 를 번역한 내용입니다.


함수형 프로그래밍 개념을 처음으로 접해본다는 것 자체가 중요하다. 그리고 이 단계가 가장 어렵기도 하다. 하지만, 올바른 관점으로 접근한다면 그렇게 어렵진 않다.


지난 글: [파트1](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-1)), [파트2](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-2)), [파트3](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-3)), [파트4](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-4)), [파트5](https://github.com/FEDevelopers/tech.description/wiki/함수형-프로그래머가-되고-싶다고%3F-(Part-5))

![functinoal](https://cdn-images-1.medium.com/max/800/1*AM83LP9sGGjIul3c5hIsWg.png)

## 그래서?

![](https://cdn-images-1.medium.com/max/1600/1*yVZA0aT5t6crvBPAMn46Kg.png)

이처럼 위대한 것들을 배운뒤 이런 생각이 들지도 모르겠다. 

> "그래서요? 이걸 어떻게 프로그래밍에 사용할 수 있는거죠?"

만약 엘름(Elm)이나 하스켈(Haskell)같은 순수 자바스크립트 언어로 프로그래밍을 할 줄 안다면 이런 아이디어를 모두 사용할 수 있다. 그리고 이 언어들은 그렇게 할 수 있는 쉬운 방법을 제공한다.

만약 대부분의 사람들처럼 자바스크립트 같은 명령형 언어로만 프로그래밍 할 줄 안다면 당신이 그동안 배운 것들을 많이 활용할 수는 있지만 그만큼 더 많은 훈련히 필요하다.

## 함수형 자바스크립트

![](https://cdn-images-1.medium.com/max/1600/1*w_gG-CXQX4TV3B5bN24nqg.png)

자바스크립트는 보다 함수형적인 방법으로 프로그래밍할 수 있는 기능이 많이 있다. 순수하지는 않지만 자바스크립트 언어로 불변성을 얻을수 있으며 라이브러리를 사용한다면 더 많은 것을 얻을 수 있다.

자바스크립트가 이상적이지는 않지만, 사용해야만 한다면 함수형 언어의 이점을 얻지 않을 이유가 없다.

### 불변성

첫번째로 고려할 것이 불변성이다. ES2015(ES6라고 불렀던)에는 const라는 새로운 키워드가 있다. 이것은 한 번 변수가 설정되면 다시 설정될수 없다는 의미이다.

```javascript
const a = 1;
a = 2; // 크롬, 파이어폭스, 노드에서 TypeError를 던진다 
       // 하지만 사파리는 잘된다 (circa 10/2016) 
```

위에서 a는 상수로 정의했고 일단 설정하면 변경할 수가 없다. a = 2가 예외를 던지는 이유가 이때문이다. (사파리는 제외)

자바스크립트의 const의 문제는 충분하지 않는다는 것이다. 아래 예제는 이 한계를 설명한다.

```javascript
const a = {
    x: 1,
    y: 2
};
a.x = 2;  // 예외 아님!
a = {};   // TypeError 예외!
```

a.x = 2가 어떻게 예외를 던지지 않는지 주목하자. 오직 변수 a만 const 키워드를 통해 불변성을 가질 뿐이고, a가 가리키는 어떤 것도 변경될 수 있다.

이것은 자바스크립트를 너무 좋게 만든 꼴이 되버려서 유감이다.

우리는 어떻게 자바스크립트에서 불변성을 얻을 수 있을까?

불행히도 Immutable.js라 불리는 라이브러리를 사용해야만 한다. 이것은 더 나은 불변성을 제공하지만 우리가 작성한 코드를 자바스크립트가 아닌 자바처럼 보이게 만들어 버린다.

### 커링과 컴포지션 

이 시리즈 초기에 커리된 함수를 작성하는 방법을 배웠다. 여기 더 복잡한 예제가 있다.

```javascript
const f = a => b => c => d => a + b + c + d
```

커링 부분을 직접 작성한 것에 주의하자.

그리고 f를 호출하기 위해 우리는 이렇게 작성한다.

```javascript
console.log(f(1)(2)(3)(4)); // 10 출력 
```

리습(Lisp) 프로그래머를 울릴만큼 괄호가 너무 많다.

이것을 더 쉽게 만들수 있는 라이브러리가 많은데 내가 가장 좋아하는 것이 [람다(Ramda)](http://ramdajs.com)이다.

람다를 이용해서 우리는 이제 이렇게 작성한다.

```javascript
const f = R.curry((a, b, c, d) => a + b + c + d);
console.log(f(1, 2, 3, 4)); // 10 출력
console.log(f(1, 2)(3, 4)); // 역시 10 출력 
console.log(f(1)(2)(3, 4)); // 역시 10 출력
```

함수 정의부는 개선 되지 않았지만 모든 괄호를 넣을 필요는 없게 되었다. 우리가 f를 호출할 때마다 원하는 만큼의 매개변수를 적용할 수 있음에 주목하라.

람다를 사용함으로서 [파트3](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-3))과 [파트4](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-4))의 `mult5AfterAdd10` 함수를 다시 작성할 수 있다.

```javascript
const add = R.curry((x, y) => x + y);
const mult5 = value => value * 5;
const mult5AfterAdd10 = R.compose(mult5, add(10));
```

람다에는 이러한 코드를 작성하기 위한 많은 헬퍼 함수들을 제공한다. 예를 들어 코드를 적게 작성할 수 있는 R.add 와 R.multiply다.

```javascript
const mult5AfterAdd10 = R.compose(R.multiply(5), R.add(10));
```

### 맵, 필터, 리듀스 

람다는 자체 버전의 맵(map), 필터(filter), 리듀스(reduce) 함수를 가지고 있다. 비록 바닐라 자바스크립트의 Array.prototype에 이러한 함수가 있긴지만 람다에서 제공하는 것은 커링된 버젼이다.

```javascript
const isOdd = R.flip(R.modulo)(2);
const onlyOdd = R.filter(isOdd);
const isEven = R.complement(isOdd);
const onlyEven = R.filter(isEven);

const numbers = [1, 2, 3, 4, 5, 6, 7, 8];
console.log(onlyEven(numbers)); // prints [2, 4, 6, 8]
console.log(onlyOdd(numbers)); // prints [1, 3, 5, 7]
```

R.modulo는 2개 파라매터를 취한다. 하나는 나눠지는 수(피제수)이고 두번째는 나누는 수(제수)이다. 

isOdd 함수는 2로 나눌 때의 나머지 값이다. 나머지가 0이면 거짓(홀수 아님), 나머지가 1이면 참(홀수)이다. 우리는 제수 2를 지정하기 위해 modulo의 첫번째와 두번째 파라매터를 뒤바꾼다.

isEven 함수는 isOdd 함수의 보수(complement)이다.

onlyOdd 함수는 isOdd의 불리언 값을 반환하는 함수인 조건자(predicate)가 있는 filter 함수이다. 실행되기 전에 숫자형 목록과 마지막 파라매터를 기다린다.

onlyEven 함수는 isEven을 조건자로 사용하는 filter 함수다.

numbers를 onlyEven과 onlyOdd에 넘겨줄 때 isEven과 isOdd는 그들의 마지막 파라매터를 얻게되고, 결국 우리가 기대한 숫자목록을 반환한다.

## 자바스크립트의 단점들 

![](https://cdn-images-1.medium.com/max/1600/1*GjSzT5C7dKD0GPgSZVFGIw.png)

자바스크립트 라이브러리와 언어적 개선에도 불구하고 자바스크립트가 모든 사람들에게 모든 것을 시도하려는 명령형 언어라는 사실은 매우 괴로운 일이다.

대부분의 프론트엔드 개발자들에게 자바스크립트는 브라우져에서 오랫동안 유일한 선택지였다. 하지만 이제 많은 개발자들은 직접 자바스크립트를 작성하지 않고 있다.

대신 다른 개발 언어로 작성한 뒤 컴파일하고 더 정확하게 자바스크립트로 변환한다.

커피스크립트가 이러한 언어들 중 첫번째 언어였다. 타입스크립트는 앵귤러 2에서 채택되었고, 바벨도 자바스크립트를 위한 트랜스파일러라고 할 수 있다.

사람들은 점점 프로덕션에서 이러한 접근을 취하고 있다.

그러나 이러한 언어들은 자바스크립트를 약간 좋게 만들었을 뿐이다. 순수 함수형 언어에서 자바스크립트로 변환하는 방법은 어떨까?

## Elm / 엘름 

![](https://cdn-images-1.medium.com/max/1600/1*oVJSlb6bJfNCXYacQmcvew.png)

이번 시리즈에서 함수형 프로그래밍의 이해를 돕기위해 엘름을 살펴봤다.

### 그런데 엘름은 뭐죠? 어떻게 사용하는거죠?

엘름은 자바스크립트로 컴파일하는 순수 함수형 언어이므로 일명 TEA(리덕스 개발자들에게 영향을 미침)라고하는 [엘름 아키텍처](https://guide.elm-lang.org/architecture/)를 이용해 웹 어플리케이션을 작성하는데 사용할 수 있다.

엘름 프로그램은 런타임 에러가 없다.

엘름은 창시자인 Evan Czapliki가 일하는 곳인 [NoRedlnk](https://www.noredink.com)같은 회사에서 프로덕션으로 사용된다. (그는 과거에 [Prezi](https://prezi.com)에서 일하기도 했다.)

더 자세한 정보는 NoRedlnk와 엘름 에반젤리스트의 [6 Months of Elem in Production](https://www.youtube.com/watch?v=R2FtMbb-nLs)의 이야기를 참고하라.

### 모든 자바스크립트를 엘름으로 바꿔야 하나요?

아니다. 점차적으로 변경할 수 있다. 더 배우기 위해서 [How to use Elm at Work](http://elm-lang.org/blog/how-to-use-elm-at-work) 블로그를 읽어보라.

### 왜 엘름을 배우죠?

1. 순수 함수형 언어로 개발하는 것은 제한적이면서 해방적이다. 당신이 할 수 있는 일을 제한하지만 동시에 버그와 나쁜 디자인 선택에서 해방시켜 준다. 모든 엘름 프로그램은 엘름 아키텍쳐(a Functionally Reactive Model)를 따르기 때문이다.

2. 함수형 프로그래밍은 당신을 더 나은 개발자로 만들어 줄 것이다. 이 글의 아이디어는 빙산의 일각일 뿐이다. 당신은 정말로 그것들을 볼 필요가 있다. 당신의 프로그램이 얼마나 크기가 줄어들고 안정성이 늘어나는지 알아야 한다.

3. 자바스크립트는 초기에 10일만에 빌드된 뒤 지난 20년간 다소 함수형적이고 객체지향적이며 완전히 명령형 프로그래밍 언어가 되도록 패치되어 왔다. 엘름은 지난 수 십년 동안 수학 및 컴퓨터 과학 분야에서 일해온 하스켈 커뮤니티의 지난 30년간에 배운 것을 이용해 디자인 되었다. 엘름 아키텍처 (TEA)는 수년에 걸쳐 디자인되고 정제되어 왔다. 그리고 함수형 리엑티브 프로그램에서 에반의 논문 결과이기도 하다. 이것의 디자인 공식화에 들어간 사고 수준을 이해하기 위해 [Controlling Time and Space](https://www.youtube.com/watch?v=Agu6jipKfYw)를 보라.

4. 엘름은 프론트엔드 웹개발자를 위해 설계되었고 삶을 더 편리하게 만드는데 촛점을 맞추었다. 이해하기 돕기위해 [Let's Be Mainstream](https://www.youtube.com/watch?v=oYk8CKH7OhE)을 보라.

## 앞으로

![](https://cdn-images-1.medium.com/max/1600/1*0FpreasFPaa5rYns6Mpe6w.png)

미래가 어떻게 될지 아는 것은 불가능하지만 약간의 추측은 할 수 있다. 몇 가지를 추측해 보자.

> 자바스크립트로 컴파일되는 언어로 이동할 것이다.

> 40년 이상 존재해온 함수형 프로그래밍 아이디어는 현재의 소프트웨어 복잡성 문제를 해결하기 위해 재발견될 것이다.

> 하드웨어의 상태(예를 들어 대량의 값싼 메모리와 빠른 프로세서)는 함수형 기술을 가능하게 할 것이다.

> CPU는 더 이상 빨라지지 않겠지만 코어 수는 계속 증가할 것이다.

> 가변 상태는 복잡한 시스템에서 가장 큰 문제중 하나로 인식될 것이다.

나는 함수형 프로그래밍은 미래라고 믿고 지난 몇 동안 그것을 배우기 위해 고생했기 때문에 이 시리즈를 작성했다.

나의 목표는 다른 사람들이 이런 개념을 좀 더 쉽고 빠르게 배울수 있고, 미래에 유능한 커리어를 겸비한 더 나은 프로그래머가 되도록 돕는 것이다.

엘름이 미래에 엄청난 언어가 될 것이라는 예측이 잘못되었다 하더라고 확실히 말할수 있다. 미래가 어떻게 되던지 간에 함수형 프로그래밍과 엘름은 어느 정도 궤도에 오를것이라고.

이 시리즈를 읽은 뒤에 당신의 능력과 함수형 프로그래밍의 이해에 대한 확신을 가지길 바란다.

앞으로의 노력에 행운을 빌며.
