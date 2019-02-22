> 이 문서는 https://medium.com/@cscalfani/so-you-want-to-be-a-functional-programmer-part-2-7005682cec4a#.kr91fkgy5 를 번역한 내용입니다.


![functinoal](https://cdn-images-1.medium.com/max/800/1*AM83LP9sGGjIul3c5hIsWg.png)


함수형 프로그래밍 개념을 처음으로 접해본다는 것 자체가 중요하다. 그리고 이 단계가 가장 어렵기도 하다. 하지만, 올바른 관점으로 접근한다면 그렇게 어렵진 않다.


지난 글: [파트1](https://github.com/FEDevelopers/tech.description/wiki/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EA%B0%80-%EB%90%98%EA%B3%A0-%EC%8B%B6%EB%8B%A4%EA%B3%A0%3F-(Part-1))


## 친절한 조언


![thumb](https://cdn-images-1.medium.com/max/800/1*RYgiClt6s_Xj9OUK9qapIw.png)


코드를 천천히 읽어주길 바란다. 코드를 이해한 다음 다음 라인으로 넘어가길 바란다. 각각의 섹션은 이전 섹션과 이어진다.


만약 서둘러서 보게 된다면 나중에 중요하게 사용될 개념을 놓치게 될지도 모른다. 


## 리팩토링


![refactor](https://cdn-images-1.medium.com/max/800/1*_GBlt7_8aD19rxHh6f2Uow.png)


먼저 리팩토링에 대해 잠시 생각해보자. 아래는 자바스크립트 코드다.


``` javascript
function validateSsn(ssn) {
    if (/^\d{3}-\d{2}-\d{4}$/.exec(ssn))
        console.log('Valid SSN');
    else
        console.log('Invalid SSN');
}
function validatePhone(phone) {
    if (/^\(\d{3}\)\d{3}-\d{4}$/.exec(phone))
        console.log('Valid Phone Number');
    else
        console.log('Invalid Phone Number');
}
```


우리는 예전부터 이런 코드를 계속 써왔다. 그리고 두 가지 모두 같은 기능을 하고 있다는 걸 인식할 수 있을 것이다.


validateSsn 함수와 validatePhone 함수를 각각 복사, 붙여넣기로 만다는 것보다 매개변수를 이용해서 하나의 함수로 만들어야 한다.


이 예제에서 우리는 변수(value), 정규식(regex), 출력할 메세지(message) 3가지를 매개변수로 이용할 수 있다.


리팩토링 결과:


``` javascript
function validateValue(value, regex, type) {
    if (regex.exec(value))
        console.log('Invalid ' + type);
    else
        console.log('Valid ' + type);
}
```


이전 코드에 있었던 매개변수 ssn과 phone은 value라는 변수로 재활용되었다.


정규식 /^\d{3}-\d{2}-\d{4}$ 과 /^\(\d{3}\)\d{3}-\d{4}$/ 는 regex 변수로 재활용되었다.


마지막으로, 메세지 부분인 ‘SSN’ 과 ‘Phone Number’ 은 type 변수로 재활용되었다.


두 개, 세 개의 함수를 중복 사용하는 것보다 한 개의 함수로 줄이는 방법이 훨씬 낫다. 이런 방식은 코드를 깔끔하게 만들어주고 유지보수에도 도움이 된다.


예를 들어, 만약에 버그가 발생했을 때 함수가 복사, 붙여넣기로 파편화되어 있다면 모든 곳을 뒤져야 하지만 하나의 함수로 구성되어 있다면 그곳만 수정하면 된다.


하지만 아래의 상황에서는 다른 일이 발생한다.


``` javascript
function validateAddress(address) {
    if (parseAddress(address))
        console.log('Valid Address');
    else
        console.log('Invalid Address');
}
function validateName(name) {
    if (parseFullName(name))
        console.log('Valid Name');
    else
        console.log('Invalid Name');
}
```


여기에 있는 parseAddress 와 parseFullName 함수는 스트링 값을 입력 받아서 참 거짓을 구분해준다.


어떻게 리팩토링 할까?


음. 일단 직전 예제에서 했던 것처럼 address 와 name를 대체할 변수를 사용할 수 있다. 그리고 ‘Address’ 와 ‘Name’를 대체할 변수를 사용할 수 있다. 하지만 여기에는 정규식을 활용하는 함수가 존재한다.


만약 매개변수로 함수를 전달할 수 있다면...


## 고차함수


![HOF](https://cdn-images-1.medium.com/max/800/1*hZyWFJAiDDiqci0ygBLeoA.png)


함수를 매개변수로 전달하는 건 대부분의 언어에서는 안 된다. 몇몇 언어에서는 되지만, 쉽지는 않다.


> 함수형 프로그래밍에서는 함수라는 게 1급 시민이다. 다른 말로 함수는 또다른 값일 뿐이다.


함수는 단순한 값이기 때문에 매개변수로 함수를 전달할 수 있다.


비록 자바스크립트가 순수한 함수형 언어는 아니지만, 함수형 명령을 사용할 수 있다. 아래의 코드는 두 개의 함수를 하나의 함수로 리팩토링 후, parseFunc라는  매개변수로 함수를 전달받아 실행하게 작성되었다.


``` javascript
function validateValueWithFunc(value, parseFunc, type) {
    if (parseFunc(value))
        console.log('Invalid ' + type);
    else
        console.log('Valid ' + type);
}
```


이 새로운 함수를 **고차함수**라고 부른다.


> 고차함수는 함수를 매개변수로 받을 뿐만 아니라 함수를 반환하기도 한다.


그러므로 저 고차함수로 이전에 작성한 4개의 함수를 호출할 수 있다. (자바스크립트에서 Regex.exec는 정규식에서 일치하는 문자열이 있으면 그 문자열을 반환해준다)


``` javascript
validateValueWithFunc('123-45-6789', /^\d{3}-\d{2}-\d{4}$/.exec, 'SSN');
validateValueWithFunc('(123)456-7890', /^\(\d{3}\)\d{3}-\d{4}$/.exec, 'Phone');
validateValueWithFunc('123 Main St.', parseAddress, 'Address');
validateValueWithFunc('Joe Mama', parseName, 'Name');
```


이 방식은 동일한 기능을 하는 4개의 함수를 중복으로 가지는 것보다 훨씬 낫다.


하지만 정규식을 보자. 약간 복잡하다. 리팩토링으로 조금 더 간결하게 만들어보자.


``` javascript
var parseSsn = /^\d{3}-\d{2}-\d{4}$/.exec;
var parsePhone = /^\(\d{3}\)\d{3}-\d{4}$/.exec;

validateValueWithFunc('123-45-6789', parseSsn, 'SSN');
validateValueWithFunc('(123)456-7890', parsePhone, 'Phone');
validateValueWithFunc('123 Main St.', parseAddress, 'Address');
validateValueWithFunc('Joe Mama', parseName, 'Name');
```


훨씬 낫다. 이제 phone number를 분석할 때 정규식을 일일이 복사하지 않아도 된다.


하지만 parseSsn와 parsePhone 외에 분석해야 할 더 많은 정규식이 있을 상황을 가정보자. 매번 정규식 파서를 만들때마다 그 정규식 끝 부분에는 .exec 를 붙여야한다. 사실 이런건 실수로 까먹을 수도 있다.


exec 함수를 리턴하는 고차함수를 만듦으로써 이 실수를 미연에 방지할 수 있다.


``` javascript
function makeRegexParser(regex) {
    return regex.exec;
}

var parseSsn = makeRegexParser(/^\d{3}-\d{2}-\d{4}$/);
var parsePhone = makeRegexParser(/^\(\d{3}\)\d{3}-\d{4}$/);

validateValueWithFunc('123-45-6789', parseSsn, 'SSN');
validateValueWithFunc('(123)456-7890', parsePhone, 'Phone');
validateValueWithFunc('123 Main St.', parseAddress, 'Address');
validateValueWithFunc('Joe Mama', parseName, 'Name');
```


makeRegexParser 함수는 정규식(문자열)을 입력받고 exec 함수를 반환한다. validateValueWithFunc 함수는 string과 value 입력 값을 parseFunc로 전달한다. 


parseSsn와 parsePhone는 이전과 정확히 동일하다. 입력받은 정규식을 실행하는 함수다.


사실, 이건 미미한 향상이지만, 고차함수가 함수를 반환하다는 것을 예제를 보여주기 위함이다. 


하지만, 만약 makeRegexParser가 훨씬 복잡하다면 이러한 것의 장점을 알 수 있다. 


아래는 고차함수가 함수를 반환하는 또다른 예제다.


``` javascript
function makeAdder(constantValue) {
    return function adder(value) {
        return constantValue + value;
    };
}
```


여기 상수를 입력받고 가산기를 반환하는 makeAdder라는 함수가 있다. 생성된 함수는 저장된 상수에 입력받은 값을 더한다.


아래는 어떻게 사용 되는지에 대한 예제다.


``` javascript
var add10 = makeAdder(10);
console.log(add10(20)); // prints 30
console.log(add10(30)); // prints 40
console.log(add10(40)); // prints 50
```


상수 10을 makeAdder에 전달해서 add10이라는 함수를 만들었다. 이 함수는 입력된 값에 10을 더한 값을 반환한다.


makeAddr의 결과가 반환된 이후에 생성된 가산기 함수는 상수 10에 접근할 수 있는 점을 주목해라. 가산기가 생성될 때의 스코프에 상수가 존재했기 때문이다.


만약 이런 개념이 없다면, 함수를 반환하는 함수가 그닥 유용하지 않게된다. 그래서 이 개념이 어떻게 동작 하는지에 대해 이해하는 것은 매우 중요하다.


이 개념이 바로 클로저다.


## 클로저


![closure](https://cdn-images-1.medium.com/max/800/1*0phT7qIAPVxG7KXcL-6B5g.png)


아래는 클로저를 사용하는 함수의 인위적인 예제다.


``` javascript
function grandParent(g1, g2) {
    var g3 = 3;
    return function parent(p1, p2) {
        var p3 = 33;
        return function child(c1, c2) {
            var c3 = 333;
            return g1 + g2 + g3 + p1 + p2 + p3 + c1 + c2 + c3;
        };
    };
}
```


이 예제에서 child는 자신의 변수와 parent의 변수, 그리고 grandParent의 변수에 접근한다.


그리고 parent는 자신의 변수와 grandParent의 변수에 접근한다.


grandParent는 오직 자신의 변수에만 접근할 수 있다.


(명확성을 위해 위에있는 피라미드를 참고하자.)


이걸 사용하는 예제를 살펴보자.


``` javascript
var parentFunc = grandParent(1, 2); // returns parent()
var childFunc = parentFunc(11, 22); // returns child()
console.log(childFunc(111, 222)); // prints 738
// 1 + 2 + 3 + 11 + 22 + 33 + 111 + 222 + 333 == 738
```


grandParent가 parent를 반환하는 시점부터 parentFunc는 parent의 스코프를 보관한다.


마찬가지로 parentFunc가 child를 반환하는 시점부터 childFunc는 child의 스코프를 보관한다.


함수가 생성될 때, 함수의 스코프 내에 존재하는 모든 변수는 함수의 생명 기간(lifetime)에 접근이 가능하다. 함수는 참조가 남아있는 한 계속 존재한다. 예를 들어, child의 스코프는 childFunc가 계속 참조하는 한 존재한다.


> 클로저라는 것은 함수에 대한 참조로 인해 계속 살아있는 함수의 스코프다.


자바스크립트에서 클로저는 문제가 있다. 왜냐하면 변수가 변할 수(mutable) 있기 때문이다. 참고로 클로저는 closed 되는 시점부터 반환되는 함수가 호출되는 동안 값이 변경될 수 있다.


고맙게도 함수형 언어에서의 변수는 변하지 않기(immutable) 때문에 그러한 버그와 혼동을 없애준다.


## 아이고 머리야


![brain](https://cdn-images-1.medium.com/max/1600/1*IK5485-iZaHeZRfP8aWmYg.png)


오늘은 이걸로 충분하다.


다음 문서부터는 합성 함수, 커링, 함수형 함수(map, filter, fold 등), 기타 등등에 대해 이야기 할 예정이다.