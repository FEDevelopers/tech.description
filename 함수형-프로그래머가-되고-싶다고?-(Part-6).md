> 이 문서는 https://medium.com/@cscalfani/so-you-want-to-be-a-functional-programmer-part-6-db502830403#.lmm1riwt1 를 번역한 내용입니다.


함수형 프로그래밍 개념을 처음으로 접해본다는 것 자체가 중요하다. 그리고 이 단계가 가장 어렵기도 하다. 하지만, 올바른 관점으로 접근한다면 그렇게 어렵진 않다.


지난 글: [파트1](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-1)), [파트2](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-2)), [파트3](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-3)), [파트4](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-4)), [파트5](https://github.com/FEDevelopers/tech.description/wiki/함수형-프로그래머가-되고-싶다고%3F-(Part-5))

![functinoal](https://cdn-images-1.medium.com/max/800/1*AM83LP9sGGjIul3c5hIsWg.png)

## 그래서?

![](https://cdn-images-1.medium.com/max/1600/1*yVZA0aT5t6crvBPAMn46Kg.png)

이처럼 위대한 것들을 배운뒤 이런 생각이 들지도 모르겠다. "그래서요? 이걸 어떻게 프로그래밍에 사용할 수 있는거죠?"

만약 엘름(Elm)이나 하스켈(Haskell)같은 순수 자바스크립트 언어로 프로그래밍을 할 줄 안다면 이런 아이디어를 모두 사용할 수 있다. 그리고 이 언어들은 그렇게 할수 있는 쉬운 방법을 제공한다.

만약 대부분의 사람들처럼 자바스크립트 같은 명령형 언어로만 프로그래밍 할 줄 안다면 당신이 그동안 배운 것들을 많이 활용할 수는 있지만 그만큼 더 많은 훈련히 필요하다.

## 함수형 자바스크립트

![](https://cdn-images-1.medium.com/max/1600/1*w_gG-CXQX4TV3B5bN24nqg.png)

자바스크립트는 보다 함수형적인 방법으로 프로그래밍할 수 있는 기능이 많이 있다. 순수하지는 않지만 자바스크립트 언어로 불변성을 얻을수 있으며 라이브러리를 사용한다면 더 많은 것을 얻을 수 있다.

자바스크립트가 이상적이지는 않지만, 사용해야만 한다면 함수형 언어의 이점을 얻지 않을 이유가 없다.

## 불변성

첫번째로 고려할 것이 불변성이다. ES2015(ES6라고 불렀던)에는 const 라는 새로운 키워드가 있다.이것은 한 번 변수가 설정되면 다시 설정될수 없다는 의미이다.

```javascript
const a = 1;
a = 2; // 크롬, 파이어폭스, 노드에서 TypeError를 던진다 
       // 하지만 사파리는 잘된다 (circa 10/2016) 
```

위에서  a는 상수로 정의했고 일단 설정하면 변경할 수가 없다. a = 2가 예외를 던지는 이유가 이때문이다. (사파리는 제외)

자바스크립트의 const의 문제는 충분하지 않는다는 것이다. 아래 예제는 이 한계를 설명한다.


```javascript
const a = {
    x: 1,
    y: 2
};
a.x = 2;  // 예외 아님!
a = {};   // TypeError 예외!
```

a.x = 2가 어떻게 예외를 던지지 않는지 주목하자. 오직 변수 a만 const 키워드를 통해 불변성을 가질 뿐이고, a가 가리키는 어떤것도 변경될 수 있다.

이것은 자바스크립트를 너무 좋게 만든 꼴이 되버려서 매우 유감이다.

우리는 어떻게 자바스크립트에서 불변성을 얻을 수 있을까?

불행히도 Immutable.js라 불리는 라이브러리를 사용해야만 한다. 이것은 더 나은 불변성을 제공하지만 우리가 작성한 코드를 자바스크립트가 아닌 자바처럼 보이게 만들어 버린다. ㅜㅜ

## 커링과 컴포지션 

이 시리즈 초기에 커리된 함수를 작성하는 방법을 배웠다. 여기 더 복잡한 예제가 있다.

```javascript
const f = a => b => c => d => a + b + c + d
```

커링 부분을 직접 작성한 것에 주의하자.

그리고 f 를 호출하기 위해 우리는 이렇게 작성한다.

```javascript
console.log(f(1)(2)(3)(4)); // 10 출력 
```

리습(Lisp) 프로그래머를 울릴만큼 괄호가 너무 많다.

이것을 더 쉽게 만들수 있는 라이브러리가 많은데 그중 내가 가장 좋아하는 것이 [람다(Ramda)](http://ramdajs.com)다.

람다를 이용해서 우리는 이제 이렇게 작성한다.

```javascript
const f = R.curry((a, b, c, d) => a + b + c + d);
console.log(f(1, 2, 3, 4)); // 10 출력
console.log(f(1, 2)(3, 4)); // 역시 10 출력 
console.log(f(1)(2)(3, 4)); // 역시 10 출력
```

함수 정의부는 개선 되지 않았지만 모든 괄호를 넣을 필요는 없게 되었다. 우리가 f를 호출할 때마다 원하는 만큼의 매개변수를 적용 할 수 있음에 주목하라.

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

## 맵, 필터, 리듀스 

람다는 자체 버전의 맥(map), 필터(filter), 리듀스(reduce) 함수를 가지고 있다. 비록 바닐라 자바스크립트의 Array.prototype에 이러한 함수가 있긴지만 람다 버전은 커리된 것이다.

```javascript
const isOdd = R.flip(R.modulo)(2);
const onlyOdd = R.filter(isOdd);
const isEven = R.complement(isOdd);
const onlyEven = R.filter(isEven);

const numbers = [1, 2, 3, 4, 5, 6, 7, 8];
console.log(onlyEven(numbers)); // prints [2, 4, 6, 8]
console.log(onlyOdd(numbers)); // prints [1, 3, 5, 7]
```

R.modulo는 2개 파라매터를 취한다. 하나는 나워지는 수(피제수)이고 두번째는 나누는 수(제수)이다. 

isOdd 함수는 2로 나눌때의 나머지 값이다. 나머지가 0이면 거짓(홀수 아님), 나머지가 1이면 참(홀수)이다. 우리는 나누는 수(제수) 2를 지정하기 위해 modulo의 첫번째와 두번째 파라매터를 뒤집는다.

isEven 함수는 isOdd 함수의 보수(complement)이다.

onlyOdd 함수는 isOdd의 불리언 값을 반환하는 함수인 조건자(predicate)가 있는 filter 함수이다. 실행되기 전에 숫자형 목록과 마지막 파라매터를 기다린다.

onlyEven 함수는 isEven을 조건자로 사용하는 filter 함수다.

numbers를 onlyEven과 onlyOdd에 통과할때 isEven과 isOdd는 그들의 마지막 파라매터를 얻게되고, 결국 우리가 기대한 숫자 목록을 반환한다.

## 자바스크립트의 단점들 

