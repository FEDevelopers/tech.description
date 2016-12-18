> 이 문서는 https://medium.com/@cscalfani/so-you-want-to-be-a-functional-programmer-part-5-c70adc9cf56a#.inibcu15i 를 번역한 내용입니다.


![functinoal](https://cdn-images-1.medium.com/max/800/1*AM83LP9sGGjIul3c5hIsWg.png)


함수형 프로그래밍 개념을 처음으로 접해본다는 것 자체가 중요하다. 그리고 이 단계가 가장 어렵기도 하다. 하지만, 올바른 관점으로 접근한다면 그렇게 어렵진 않다.


지난 글: [파트1](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-1)), [파트2](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-2)), [파트3](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-3)), [파트4](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-4))


## 참조 투명성


![refer](https://cdn-images-1.medium.com/max/800/1*4QRVgRMKN2che7VG8H5FxA.png)


참조 투명성은 순수한 함수가 표현식으로 안전하게 대체 될 수 있다고 설명하는 멋진 용어다. 아래의 예제가 이해하는 데 도움이 될 것이다.


대수학에서 아래의 공식을 받았을 때:


``` javascript
y = x + 10
```


그리고 아래의 값이 주어져있을 때


``` javascript
x = 3
```


x 값을 방정식에 대입하여 아래와 같은 값을 얻을 수 있다.


``` javascript
y = 3 + 10
```


아래의 방정식은 여전히 유효하다. 같은 의미로써의 대체를 순수함수로도 할 수 있다.


아래의 예제는 Elm에서의 문자열 주위에 작은 따옴표를 넣는 함수다.


``` javascript
quote str =
    "'" ++ str ++ "'"
```


아래는 위의 함수를 사용하는 예제다.


``` javascript
findError key =
    "Unable to find " ++ (quote key)
```


findError라는 함수는 key를 탐색에 실패할 때 오류 메세지를 만든다.


quote 함수가 순수하기 때문에, 우리는 간단하게 quote를 함수 내용으로 간단하게 대체할 수 있다.


``` javascript
findError key =
   "Unable to find " ++ ("'" ++ str ++ "'")
```


이게 바로 역 리팩토링이다. 이것은 코드에 대한 추론으로 프로그래머나 프로그램(컴파일러나 테스트 프로그램)에 의해 진행될 수 있는 절차다.


이것은 재귀함수를 추론할 때 특히 유용하다.


## 실행 순서


![Line](https://cdn-images-1.medium.com/max/800/1*k8zgyx2Mhlg6F82aSR9U4A.png)


대부분의 프로그램은 싱글 스레드(한번에 하나씩만 실행되는) 기반이다. 설령 멀티 스레드 기반의 프로그램일지라도 대부분의 스레드는 입출력(파일, 네트워크 등)이 완료될 때까지 차단된다.


이것이 우리가 코드를 작성할 때 자연스럽게 순서화 된 단계로 생각하는 이유 중 하나다.


``` javascript
1. 빵을 꺼낸다
2. 2조각을 토스터에 넣는다
3. 손잡이를 찾는다
4. 레버를 아래로 내린다
5. 토스트가 튀어나오길 기다린다
6. 토스트를 제거한다
7. 버터를 꺼낸다
8. 버터 칼을 꺼낸다
9. 토스트에 버터를 바른다
```


이 예제에서는 2가지의 독립적인 실행이 있다. 버터를 꺼내는 것과 빵을 굽는 것이다. 이것들은 9단계가 되어서야 상호의존이 된다.


7단계와 8단계는 1단계에서 6단계까지 관련이 없기 때문에 별도로 동시에 수행될 수 있다.


하지만 우리가 이것을 할 때 상황은 복잡해진다.


``` javascript
쓰레드 1
--------
1. 빵을 꺼낸다
2. 2조각을 토스터에 넣는다
3. 손잡이를 찾는다
4. 레버를 아래로 내린다
5. 토스트가 튀어나오길 기다린다
6. 토스트를 제거한다
쓰레드 2
--------
1. 버터를 꺼낸다
2. 버터 칼을 꺼낸다
3. 쓰레드 1이 종료되길 기다린다
4. 토스트에 버터를 바른다
```


만약 쓰레드1이 실패하면 쓰레드2는 어떻게 될까? 두 쓰레드를 조정하는 것은 어떤 메커니즘일까? 토스트를 소유하는 쓰레드는 둘 중 어떤 것일까?


이런 복잡성에 대해 생각하지 않고, 싱글 쓰레드로 두는 게 더 쉽다.


하지만 프로그램의 가능한 모든 효율을 짜낼 때가 되면 멀티 쓰레드 기반의 소프트웨어를 만들기 위해 엄청난 노력을 해야한다.


하지만 멀테 쓰레드에는 2가지 주요 문제점이 있다. 첫째, 멀티 쓰레드 기반의 프로그램은 작성, 읽기, 추론, 테스트, 디버깅하기 어렵다.


둘째, 자바스크립트와 같은 언어에서는 멀티 쓰레드를 지원하지 않는다. 설령 지원하더라도 좋은 수준은 아니다.


하지만, 순서를 중요하게 생각지 않고, 모든 것을 병렬로 처리해벼리면 어떻게 될까?


미친 소리로 들리겠지만, 생각만큼 혼란스럽지 않다. 이 개념을 이해하기 위해 아래의 Elm 코드를 살펴보자


``` javascript
buildMessage message value =
    let
        upperMessage =
            String.toUpper message
        quotedValue =
            "'" ++ value "'"
    in
        upperMessage ++ ": " ++ value
```


buildMessage은 message와 value를 입력 받고, 대문자 메세지와 콜론, value를 반환한다.


upperMessage와 quotedValue가 어떻게 독립적인지 주목하자. 어떻게 알 수 있을까?


독립적이기 위해서는 2가지가 반드시 참이어야 한다. 첫째, 모두 순수함수여야 한다. 이것은 중요하다. 왜냐하면 상대방의 실행에 영향을 받지 않기 때문이다.


만약 순수한 상태가 아니라면 결코 독립적이라는 것을 알 수 없다. 이 경우 실행 순서를 결정하기 위해 프로그램에서 호출 된 순서에 의존해야한다. 이러한 방식이 모든 명령형 언어가 작동하는 방식이다.


독립적이기 위해 참이어야 하는 둘째로 한 함수의 출력이 다른 함수의 입력으로 사용되지 않아야 한다는 것이다. 만약 그렇게 되면 두번째를 실행하기 전에 첫번째 동작이 끝나기를 기다려야 한다.


이 경우, upperMessage와 quotedValue는 모두 순수하고 다른 하나의 출력을 필요로 하지 않는다.


그러므로, 두 함수는 어떤 순서로 실행되어도 무방하다.


컴파일러는 프로그래머의 도움 없이 결정할 수 있다. 이것은 오직 순수한 프로그래밍 언어에서 가능하다. 왜냐하면 부작용의 파급효과를 파악하기 매우 어렵기 때문이다.


> 순수 함수형 언어에서의 실행 순서는 컴파일러에 의해 결정될 수 있다.


이것은 CPU의 발전 속도가 점점 더뎌질 때 매우 장점이 있다. 대신에 제조업체는 CPU에 더 많은 코어를 추가한다. 이것은 코드가 하드웨어 단에서 병렬로 실행된다는 것을 의미한다.


불행하게도, 명령형 언어에서는 매우 거친 수준을 제외하고는 이러한 코어의 모든 장점을 취할 수 없다. 하지만 그렇게 하려면 우리의 프로그램의 설계를 크게 변경해야 한다.


순수 함수형 언어에서는 코드를 변경하지 않고, 세분화된 수준의 CPU 코어를 자동을 활용할 수 있게 된다.


## 타입 명시


![type](https://cdn-images-1.medium.com/max/800/1*btL9u2b5VZwivpqNbfoVmw.png)


In Statically Typed Languages, types are defined inline. Here’s some Java code to illustrate:


``` javascript
public static String quote(String str) {
    return "'" + str + "'";
}
```