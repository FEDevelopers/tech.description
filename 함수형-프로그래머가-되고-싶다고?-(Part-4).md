> 이 문서는 https://medium.com/@cscalfani/so-you-want-to-be-a-functional-programmer-part-4-18fbe3ea9e49#.iddazh5wz 를 번역한 내용입니다.


![functinoal](https://cdn-images-1.medium.com/max/800/1*AM83LP9sGGjIul3c5hIsWg.png)


함수형 프로그래밍 개념을 처음으로 접해본다는 것 자체가 중요하다. 그리고 이 단계가 가장 어렵기도 하다. 하지만, 올바른 관점으로 접근한다면 그렇게 어렵진 않다.


지난 글: [파트1](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-1)), [파트2](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-2)), [파트3](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-3))


## 커링


![puzzle](https://cdn-images-1.medium.com/max/1600/1*zihd0We3yAkjAxleLPL2aA.png)


[파트3](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-3))를 기억한다면 mult5 함수와 add 함수를 합성할 때 문제가 발생했었는데, 그 원인은 mul5 함수는 1개의 매개변수를 받고, add 함수는 2개를 받기 때문이다.


우리는 모든 함수를 매개변수 1개만 받도록 제한함으로써 쉽게 이 문제를 해결할 수 있다.


믿어라. 생각보다 그리 나쁜 방법은 아니다.


우리는 2개의 매개변수를 받았던 add 함수를 1개만 받도록 간단하게 고친다. 커링 함수는 이걸 가능하게 해준다.


> 커링 함수는 한 번에 오직 1개의 매개변수만 받는 함수다. 


이 개념은 mult5 함수와 합성하기 이전에 우리에게 add 함수의 첫번째 매개변수를 준다. 그리고 mult5AfterAdd10가 호출되면 add 함수는 두번째 매개변수를 받게 된다.


add 함수를 리팩토링하면서 커링 개념을 적용할 수 있다.


``` javascript
var add = x => y => x + y
```

이 버전의 add 함수는 처음에는 오직 하나의 매개변수만 받고, 나머지 한 개는 나중에 받는다.


조금 더 구체적으로, add 함수는 x라는 한 개의 매개변수를 받고, y라는 한 개의 매개변수를 받는 새로운 함수를 반환한다. 그 함수는 결과적으로 x와 y의 합계를 반환한다.


이제 우리는 이 버전의 add를 mult5AfterAdd10의 working version을 만들기 위해 사용할 수 있다. 


``` javascript
var compose = (f, g) => x => f(g(x));
var mult5AfterAdd10 = compose(mult5, add(10));
```


compose 함수는 f, g라는 2개의 매개변수를 받는다. 그리고 x라는 1개의 매개변수를 받는 함수를 반환한다. 호출될 대 그 함수는 g 함수에 x를 적용한 후, 그 결과에 f를 적용한다.


여태 우리가 하고 있는 게 정확히 어떤 걸까? 평범한 예전 add 함수를 커링 버전으로 바꿨다. 이것으로 add 함수를 더욱 유연하게 만들었다. 왜냐하면 첫번째 매개변수 10은 상위 함수로 상속되고, 마지막 매개변수는 mult5AfterAdd10가 호출될 때 전달되기 때문이다.


이 부분에서 Elm에서는 add 함수를 어떻게 다시 작성하는지 아마 궁금할지도 모른다. 그럴 필요가 없는 게, Elm과 다른 함수형 언어에서는 모든 함수가 자동으로 커링되기 때문이다.


그래서 add 함수는 동일하게 생겼다.


``` javascript
add x y =
    x + y
```


이게 바로 [파트3](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-3))에서 mult5AfterAdd10가 작성 되었어야 할 모습이다.


``` javascript
mult5AfterAdd10 =
    (mult5 << add 10)
```


구문적인 면에서 Elm은 자바스크립트와 같은 명령형 언어를 이긴다. 왜냐하면 이 언어는 커링과 합성 같은 함수형 개념에 최적화 되어있기 때문이다.


## 커링과 리팩토링


![baby](https://cdn-images-1.medium.com/max/1600/1*kbFszF2qDVeeN591mpq8Ug.png)


커링이 빛나는 또다른 경우는 리팩토링을 할 때다. 수많은 매개변수가 있는 일반적인 버전의 함수를 소수의 매개변수를 가진 버전으로 바꿀 때가 그 경우다.


예를 들어, 문자열에 1개의 중괄호 추가하거나 2개의 중괄호를 추가하는 함수가 아래에 있다. 


``` javascript
bracket str =
    "{" ++ str ++ "}"
doubleBracket str =
    "{{" ++ str ++ "}}"
```


아래는 위의 함수를 실제로 사용한 예다.


``` javascript
bracketedJoe =
    bracket "Joe"

doubleBracketedJoe =
    doubleBracket "Joe"
```


우리는 bracket과 doubleBracket을 일반화 할 수 있다.


``` javascript
generalBracket prefix str suffix =
    prefix ++ str ++ suffix
```


하지만 이제 generalBracket을 사용할 때마다 중괄호를 넘겨줘야한다.


``` javascript
bracketedJoe =
    generalBracket "{" "Joe" "}"

doubleBracketedJoe =
    generalBracket "{{" "Joe" "}}"
```


What we really want is the best of both worlds.
우리가 정말 원하는 건 the best of both worlds이다.


If we reorder the parameters of generalBracket, we can create bracket and doubleBracket by leveraging the fact that functions are curried:
