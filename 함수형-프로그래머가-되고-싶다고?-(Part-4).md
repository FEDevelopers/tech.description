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


우리가 정말 원하는 건 우주대통합(the best of both worlds)이다.


만약 generalBracket의 매개변수들을 재정렬한다면, 함수가 커링된다는 영향력에 의해 bracket과 doubleBracket을 만들 수 있다.


``` javascript
generalBracket prefix suffix str =
    prefix ++ str ++ suffix
bracket =
    generalBracket "{" "}"
doubleBracket =
    generalBracket "{{" "}}"
```


먼저 static으로 고정할 부분(prefix, suffix)을 우선 매개변수로 넘긴 후, dynamic으로 바뀔 부분(str)을 마지막 매개변수로 넘긴다. 이렇게 하면 쉽게 generalBracket의 특별한 버전을 만들 수 있다.


> 완전히 커링을 활용하는 데 매개변수의 순서는 중요하다.


또한, bracket과 doubleBracket은 point-free 표기법으로 작성되었다. str 매개변수는 함축되었기 때문이다. bracket과 doubleBracket 함수는 모두 마지막 매개변수를 기다리는 형태다.


이제 우리는 이렇게 사용할 수 있다.


``` javascript
bracketedJoe =
    bracket "Joe"
doubleBracketedJoe =
    doubleBracket "Joe"
```


하지만 이번에는 일반적인 커링 함수 generalBracket을 사용해보자.


## 일반적인 함수형 함수


![functionalfunction](https://cdn-images-1.medium.com/max/800/1*I7nCgMOzuVxKPj_amfQxNw.png)


함수형 언어에서 사용되는 3가지의 일반적인 함수를 살펴보자.


하지만 먼저, 아래의 자바스크립트 코드를 보자.


``` javascript
for (var i = 0; i < something.length; ++i) {
    // do stuff
}
```


여기에는 한가지 중요한 게 하나 있다. 버그는 아니다. 문제는 이 코드가 자주 반복될 boilerplate 코드라는 것이다.


만약 Java, C#, Javascript, PHP, Python 등등과 같은 명령형 언어로 코딩할 때 boilerplate를 반복하고 있는 자신을 발견할 것이다.


이게 바로 잘못된 것이다.


그러므로 이걸 없애보자. boilerplate를 함수 안에 담고, for-loop를 절대 사용하지 않는다. 음..최소한 함수형 언어로 넘어간다면 사용할 일이 거의 없다.


things라는 배열의 내용을 수정하는 걸로 시작해보자.


``` javascript
var things = [1, 2, 3, 4];
for (var i = 0; i < things.length; ++i) {
    things[i] = things[i] * 10; // MUTATION ALERT !!!!
}
console.log(things); // [10, 20, 30, 40]
```


읅! 원본이 변했다!


다시 시도해보자. 이번에는 things를 수정하지 않을 것이다.


``` javascript
var things = [1, 2, 3, 4];
var newThings = [];
for (var i = 0; i < things.length; ++i) {
    newThings[i] = things[i] * 10;
}
console.log(newThings); // [10, 20, 30, 40]
```


좋다. 이번에는 things을 수정하지 않고, newThings를 수정했다. 우선 이 부분은 넘어갈 것이다. 우리는 결국 자바스크립트 안에 있다. 함수형 언어로 한 번 넘어가면 수정이 일어나지 않을 것이다.


여기에서의 초점은 이 함수들이 어떻게 작동하고, 코드 안의 소음을 어떻게 줄일 수 있는지를 이해하는 것이다.


이번에는 해당 코드를 함수에 넣어보자. 첫번째로 만든 일반 함수를 map이라고 부를 것이다. 왜냐하면 이 함수는 old 배열의 요소를 하나씩 순회해서 새로운 값을 만든뒤 new 배열에 담기 때문이다.


``` javascript
var map = (f, array) => {
    var newArray = [];
    for (var i = 0; i < array.length; ++i) {
        newArray[i] = f(array[i]);
    }
    return newArray;
};
```


map 함수로 전달되는 매개변수 f는 array에 있는 요소에 원하는 작업을 하나씩 할 수 있게 해준다.


이제 우리는 이전 코드를 map 함수를 호출하는 형식으로 수정할 수 있다.


``` javascript
var things = [1, 2, 3, 4];
var newThings = map(v => v * 10, things);
```


보다시피 for-loop가 없다. 그리고 훨씬 쉽게 읽을 수 있고, 추론하기도 쉽다.


음 내부적으로 map 함수 안에는 for-loop가 존재한다. 하지만 최소한 boilerplate 코드를 반복할 필요가 없어졌다.


이번에는 배열에서 요소를 걸러내는 역할을 하는 일반적인 함수를 만들어보자. 


``` javascript
var filter = (pred, array) => {
    var newArray = [];
for (var i = 0; i < array.length; ++i) {
        if (pred(array[i]))
            newArray[newArray.length] = array[i];
    }
    return newArray;
};
```


어떻게 pred 함수가 참을 반환하는지 아니면 거짓을 반환하는지 살펴보자.


아래의 코드는 홀수를 걸러내는 filter 함수의 적용 예다.


``` javascript
var isOdd = x => x % 2 !== 0;
var numbers = [1, 2, 3, 4, 5];
var oddNumbers = filter(isOdd, numbers);
console.log(oddNumbers); // [1, 3, 5]
```


새로운 filter 함수를 적용하는 것은 for-loop를 사용하는 것에 비하면 굉장히 단순하다.


마지막 일반적인 함수는 reduce라고 불린다. 일반적으로 이것은 리스트를 입력 받아서 하나의 값으로 줄이는데 사용된다. 하지만 그 이상의 기능을 할 수 있다.


이 함수는 함수형 언어에서 보통 fold라고 불린다.


``` javascript
var reduce = (f, start, array) => {
    var acc = start;
    for (var i = 0; i < array.length; ++i)
        acc = f(array[i], acc); // f() takes 2 parameters
    return acc;
};
```


reduce 함수는 감축 기능을 하는 f와 초기 값, 그리고 배열을 입력받는다.


감축 함수 f는 2개의 매개변수(array의 현재 요소, 누산기 acc)를 받는다. f는 각 반복마다 새로운 누산기 생산하기 위해 위의 매개변수를 사용한다. 최종 반복에서 누산기가 반환된다.


어떻게 동작하는지 이해하기 쉽게 아래의 예제를 살펴보자.


``` javascript
var add = (x, y) => x + y;
var values = [1, 2, 3, 4, 5];
var sumOfValues = reduce(add, 0, values);
console.log(sumOfValues); // 15
```


add 함수는 2개의 매개변수를 받고 합계를 반환한다. 위 코드에서의 reduce 함수는 2개의 매개변수를 입력 받아서 잘 동작하기를 기대한다.


초기 값을 0으로 시작하고, 우리 배열 값을 전달하여 합산합니다. reduce 함수 내부에서는 합계는 값을 반복할 때마다 누적된다. 최종 누적된 값이 sumOfValues로 반환된다.


위의 map, filter, reduce 각각의 함수는 boilerplate for-loop의 반복 없이 일반적인 배열의 조작을 가능하게 한다.


하지만 함수형 언어에서는 생성에서의 loop조차 없고, 재귀로만 이루어져있기 때문에 더욱 유용하다. 반복 함수들은 매우 도움되는 편은 아니다. 그래도 필요하긴 하다.


## 아이고 머리야!!!


![Brain](https://cdn-images-1.medium.com/max/800/1*IK5485-iZaHeZRfP8aWmYg.png)


오늘은 이걸로 충분하다.


다음 문서부터는 참조 투명성, 실행순서, 형식 등에 대해 이야기 할 예정이다.