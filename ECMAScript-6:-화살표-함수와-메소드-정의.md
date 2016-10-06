> 이 문서는 http://www.2ality.com/2012/04/arrow-functions.html 를 번역한 내용입니다.


자바스크립트에서 메소드 안에서 함수를 만드는 부분은 올바르게 사용하기 어렵다. 특별한 변수인 ```this``` 때문이다. ES6에서는 ```화살표 함수```와 ```메소드 정의``` 두 가지의 개념을 소개함으로써, 이러한 부분을 쉽게 해준다. 이 글은 이 2가지가 어떤 것들이며 어떻게 우리를 도와줄 수 있는지 알려준다.


## 1. 용어 소개
이 글에서는 두 가지 개념을 구분한다.
- 서브루틴은 자신 그 자체로 존재하며 직접 호출된다. 일반적으로 "함수"가 더 나은 단어이지만, 자바스크립트에서 함수라는 의미는 다양한 의미를 가지고 있다. 그래서 자바스크립트 개발자들이 흔히 말하는 "메소드가 아닌 함수"는 서브루틴이라고 한다. 이 글에서는 함수라는 말 대신 서브루틴이라는 용어를 사용할 것이다. 왜냐하면 서브루틴이라는 단어보다 더 나은 걸 찾지 못했기 때문이다. (콜백, 프로시저도 서브루틴과 비슷한 맥락)
- 메소드는 객체의 한 부분이고, 객체를 통해 호출된다. 


자바스크립트에서 서브루틴과 메소드는 함수라는 키워드로 수행된다. 예를 들어


``` javascript
    var obj = {
        myMethod: function () {
            setTimeout(function () { ... }, 0);
        }
    }
```


여기서 myMethod는 메소드고, setTimeout()의 첫 번째 인수는 서브루틴이다. 서브루틴과 메소드는 함수로 실행된다. 이들의 영향에 대해 설명하기 이전에 더 많은 정의를 만들어볼 필요가 있다. 함수는 호출될 때마다 두 가지 스코프(=문맥)를 가진다. 여기서의 dynamic scope는 호출되는 함수를 가리킨다. 중첩된 구조에서는 각각의 dynamic scope가 모두 발생한다는 것을 알아야 한다. 자유 변수는 함수 내부에 정의되지 않은 변수다. 만약 자유 변수를 호출하거나 수정하려고 하면, 자바스크립트는 lexical scopes를 통해 변수를 탐색한다.


함수는 메소드 실행으로도 잘 동작한다. 함수는 ```this```라 불리는 특별한 변수를 가진다. ```this```는 메소드를 호출되는 객체를 참조한다. 다른 자유 변수들과는 다르게 ```this```는 둘러싸인 코드의 ```lexical scopes```를 탐색하지 않는다. ```this```는 실행되는 시점을 통해 함수로 전달된다. 함수는 ```this```를 동적으로 전달 받기 때문에 여기서의 this는 ```dynamic this```으로 불린다.


함수는 서브루틴 실행으로는 잘 동작하지 않는다. 왜냐하면 함수의 this는 동적이기 때문이다. 서브루틴 호출은 this를 전역 객체로 가지거나 [엄격 모드](http://www.2ality.com/2011/01/javascripts-strict-mode-summary.html)에서는 undefined로 셋팅된다.  That is unfortunate, because the subroutine has no use for its own this, but it shadows the this of the surrounding method, making it inaccessible. 예를 들어


``` javascript
    var jane = {
        name: "Jane",
        
        logHello: function (friends) {
            var that = this;  // (*)
            friends.forEach(function (friend) {
                console.log(that.name + " says hello to " + friend)
            });
        }
    }
```


fotEach의 인수는 서브루틴이다. logHello의 this에 접근하기 위해서는 (*) 지점에서 보이는 별도의 선언이 필요하다. 서브루틴은 명백히  ```lexical this```를 가졌어야 한다. 이 말은 서브루틴에서의 this 역시 다른 자유 변수와 동일한 취급 받았어야 했다는 것을 의미한다. 그리고 인접한 ```lexical scopes```를 참조했어야 했다. ```that = this```은 좋은 해결 방법이다. 이 방법으로 의도대로 ```lexical this```에 접근할 수 있다. 다른 해결 방법으로는 bind를 사용하는 것이다.


``` javascript
    var jane = {
        name: "Jane",
        
        logHello: function (friends) {
            friends.forEach(function (friend) {
                console.log(this.name + " says hello to " + friend)
            }.bind(this));
        }
    }
```


이제 forEach의 인수(function)는 고정 값 this를 가진다. call이나 apply를 호출해도 이제는 더이상 변경할 수 없다. ```lexical this```를 다룰 때마다 3가지 문제점이 있다. 
- 우리는 원하지 않든 필요치 않든 자바스크팁트의 this가 별나게 동작한다는 것을 알아야 한다.
- 우리는 ```lexical this```를 다룰 때마다 끊임없이 조심해야 한다. 사실 그 ```lexical this```는 자동화 되어야하고, 별도의 고민을 필요로 하지 않아야 한다. ```lexical this```를 다루는 것은 성능과 메모리 비용을 초래한다. 그래서 가능하다면 이 경우를 피하고 싶을 것이다.
- 단어를 더 쳐야되고, 시각적 혼란을 가져온다.

심지어 그렇게 유명한 커피스크립트의 화살표 함수 역시 첫 번째, 두 번째 문제를 가진다.


## 2. 화살표 함수
ES6의 [화살표 함수](http://wiki.ecmascript.org/doku.php?id=strawman:arrow_function_syntax)는 서브루틴을 정의할 때 일반 함수를 사용하는 것보다 훨씬 낫다. 화살표 함수는 ```lexical this```를 가지기 때문이다. 위의 forEach에 화살표 함수를 적용하면 아래와 같다.


``` javascript
    let jane = {
        name: "Jane",
        
        logHello: function (friends) {
            friends.forEach(friend => {
                console.log(this.name + " says hello to " + friend)
            });
        }
    }
```


뚱뚱한 화살표는 커피스크립트와 호환이 되도록 선택되었다. 그래서 ES6의 화살표 함수는 커피스크립트에서의 뚱뚱한 화살표 함수와 매우 비슷하다.


인수에 대한 상세:


``` javascript
        () => { ... } // 인수가 없을 때
         x => { ... } // 인수가 하나일 때
    (x, y) => { ... } // 인수가 여러 개일 때
```


몸체에 대한 상세:


``` javascript
    x => { return x * x }  // 블록을 사용
    x => x * x  // 표현식. 위와 동일
```


블록 내부는 일반 함수의 몸체처럼 동작한다. 예를 들어, 어떤 값을 반환해야할 때 return을 사용해야 한다. 표현식을 사용하면 암묵적으로 항상 결과 값을 반환한다. 표현식 몸체에 추가로 블록 몸체를 사용하는 것은 객체 리터럴로 표현식을 원하는 경우, 괄호를 붙여야 한다는 것을 의미한다.


화살표 함수 표현식이 몸체를 얼마나 줄일 수 있는지 비교해보자.


``` javascript
    let squares = [ 1, 2, 3 ].map(function (x) { return x * x });
    let squares = [ 1, 2, 3 ].map(x => x * x);
```


### 2.1 화살표 함수에서의 ```lexical this```
화살표 함수에서의 ```lexical this```로 실행된다.


``` javascript
    x => x + this.y
```


위의 화살표 함수는 아래의 문법 설탕이 적용된 것과 같다.


``` javascript
    function (x) { return x + this.y }.bind(this)
```


위의 함수 표현식은 두 개의 함수를 생성한다. 첫 번째로 매개변수 x와 dynamic this가 있는 익명 함수가 생성된다. 두 번째로 이 익명 함수에 this가 결합된 바인딩 함수다. 반면에 화살표 함수는 마치 bind를 붙여서 생성한 것처럼 동작한다. 그리고 상대적으로 메모리를 덜 먹는다. 단지 하나의 엔티티, this가 외부 함수의 this를 가리키고 있는 함수, 가 생성된다. 


### 2.2 화살표 함수 vs 일반 함수


화살표 함수는 일반 함수와 3가지 측면에서 다르다. 첫째로 화살표 함수는 항상 바인딩 된 this를 가진다. 둘째로 화살표 함수는 생성자로 사용할 수 없다. 왜냐하면 [[Construct]](new 키워드와 일반 함수가 함께 실행될 수 있게 해줌)라는 내부 메소드와 prototype 속성이 없기 때문이다. 그러므로 new (() => {}) 는 에러를 반환한다. 셋째로 화살표 함수는 ES6의 새로운 구조이기 때문에 새로운 방식의 인수 조작([기본 매개변수](http://wiki.ecmascript.org/doku.php?id=harmony:parameter_default_values), [나머지 매개변수](http://wiki.ecmascript.org/doku.php?id=harmony:rest_parameters) 등등)이 가능하고, 더이상 arguments 키워드를 지원하지 않는다. 새로운 방식의 인수 조작으로 arguments로 했던 모든 것들이 가능하기 때문에 더이상 필요할 일이 없기 때문이다.


이런 점을 제외하고, 화살표 함수와 일반 함수의 외적 차이는 없다. 예를 들어, typeof와 instanceof는 예전처럼 사용할 수 있다.


``` javascript
    > typeof () => {}
    'function'
    
    > () => {} instanceof Function
    true
```


## 3. 논쟁 중인 구문 변화
아래의 구문 변화는 여전히 논쟁 중이며 아마도 ES6에 추가되지 않을 것이다.

- **매개변수 생략**:


``` javascript
    => { ... }
```


자바스크립트에는 [자동 세미콜론 삽입](http://www.2ality.com/2011/05/semicolon-insertion.html)이 있기 때문에 이전 라인과 계속 이어지는 것으로 잘못 인식될 수 있다. 예를 들어, 아래의 코드를 보자.


``` javascript
    var x = 3 + a
    => 5
```


위의 두 라인은 아래와 같이 해석된다.


``` javascript
    var x = 3 + (a => 5);
```


하지만 보통 화살표 함수는 표현식 문맥의 내부, 중첩 된 구문 내부에 사용될 것이다. 그렇기 때문에 자동 세미콜론 삽입이 큰 문제가 되지 않을 거라고 생각한다. 만약 자바스크립트가 커피스크립트처럼 [significant newlines](http://www.2ality.com/2011/11/significant-newlines.html)기능을 가진다면 이 문제는 완벽히 해결된다.


- **몸체 생략**: 


``` javascript
    x =>
```


이건 하나의 매개변수를 가지고 항상 undefined를 반환하는 함수다. 이 구문은 [void 연산자](http://www.2ality.com/2011/05/void-operator.html)와 같다. 이 기능이 얼마나 유용한지는 잘 모르겠다.


- **기명 화살표 함수**: 자바스크립트는 이미 기명 함수 표현법을 가지고 있다. 함수에 이름을 부여하고, 이 이름으로 함수를 실행할 수 있다. 그 이름은 함수에 대한 로컬이고, 외부 스코프에 전혀 오염되지 않는다. 기명 화살표 함수는 동일하게 동작한다. 예를 들어, 아래의 코드를 보자.


``` javascript
    let fac = me(n) => {
        if (n <= 0) {
            return 1;
        } else {
            return n * me(n-1);
        }
    }
    console.log(me); // ReferenceError: me is not defined
```


### 3.1 화살표 함수 해석
대부분의 자바스크립트 파서는 two-token look-ahead를 가진다. 파서는 아래의 두 표현식을 어떻게 구분할까?


``` javascript
    (x, y, z)
    (x, y, z) => {}
```


첫 번째 표현식은 괄호 내에 있는 세 개의 변수에 적용되어 있는 쉼표 연산자다. 두 번째 표현식은 화살표 함수다. 만약 이 두 개를 구별하고 싶다면, 화살표가 있는지 없는지를 체크하면 된다.


위의 두 개를 분석하기 위해서는 분석 도구는 cove grammer라고 불리는 트릭을 사용한다. 분석 도구는 두 가지 케이스를 모두 적용할 수 있는 문법 규칙을 만든다. 그리고 분석을 한 후 post-processing 방식으로 코드를 실행한다. 만약 닫힘 괄호 뒤에 화살표가 붙었다면, 앞서 분석된 구문은 오류를 발생하고, 앞서 분석된 구문은 화살표 함수의 매개변수로 사용될 것이다. 만약 화살표가 없다면, 이미 분석된 구문은 오류를 발생하고, 앞서 분석된 구문은 괄호 속 표현식이 된다. 아래와 같이 몇몇 코드는 괄호 표현식으로 실행될 수 있다.


``` javascript
    (foo(123))
```


다른 건 오직 매개변수 리스트에서만 실행된다. 예를 들어, [나머지 매개변수](http://wiki.ecmascript.org/doku.php?id=harmony:rest_parameters)를 정의 할 때,


``` javascript
    (a, b, ...rest)
```


몇 가지 더 수행 할 수 있습니다.


``` javascript
    (title = "no title")
```


위는 표현식 문맥에서의 선언과 화살표 함수 문맥에서의 기본 매개변수 정의다.


## 4. 가능한 화살표 함수의 기능: optional dynamic this
화살표 함수의 한 가지 기능으로 dynamic this를 바꿀 수 있다. 이 기능은 계속 지연되고 있지만 아마도 추가될 것이다. 이 기능의 활용은 아래와 같다. 제이쿼리에서 몇몇 인수들은 서브루틴이다. 그리고 이 서브루틴은 this를 암시적인 매개변수로 가지고 있다.


``` javascript
    $(".someCssClass").each(function (i) { console.log(this) });
```


현재는 each 함수의 인수로 화살표 함수를 사용할 수 없다. 왜냐하면 화살표 함수에는 this를 결합하기 위한 call과 apply 메소드를 적용할 수 없다. dynamic this를 바꾸기 위해서는 아래와 같이 해야한다.


``` javascript
    $(".someCssClass").each((this, i) => { console.log(this) });
```


보통 매개변수의 이름으로 this를 사용할 수 없기 때문에 위의 코드는 화살표 함수에 dynamic this를 사용하는 좋은 마커다. 


optional dynamic this를 사용하기 위한 간단한 해결책이 더 없을까? 간단해 보이는 두 가지 접근방법이 있지만, 동작하지는 않는다.


### 4.1 해결책 아님: dynamic this와 lexical this간의 전환
함수가 어떻게 실행되느냐에 따라 dynamic this 혹은 lexical this를 사용한다. 만약 함수가 메소드로 실행된다면, dynamic this를 사용한다. 만약 서브루틴으로 실행된다면, lexical this를 사용한다. 이러한 방식은 함수가 잘못된 방식으로 실행되는 문제를 야기한다. 만약 함수가 서브루틴으로 실행되어야 하는데 메소드로 실행된다면 함수의 lexical context의 this에 대한 참조를 잃어버리게 된다. 만약 메소드로 실행해서 인스턴스에 접근하려고 의도했는데 함수로 실행된다면, 의도와는 다르게 외부 함수의 this에 접근하게 된다. ECMAScript 5의 엄격모드는 에러를 반환함으로써 이 이슈를 해결한다. 만약 메소드가 서브루틴으로 실행된다면, 여기서의 this는 undefined이기 때문에 속성에 접근하자마자 에러를 반환한다. 그리고 서브루틴은 바인딩 함수가 되기 이전에는 그 this로 아무것도 할 수 없게 된다. this는 결코 바뀌지 않는다. ES6에서도 동일하게 동작하지만, 잘못된 함수 사용을 방지해준다. 화살표 함수는 기본적으로 서브루틴이 될 것이고, 이는 고정된 lexical this를 가지게 된다. 메소드 정의(아래 코드 참고)는 기본적으로 메소드가 될 것이고 반드시 객체를 통해서만 실행이 될 수 있다. 객체로부터 추출되어 사용되는 메소드를 제외한다.


두 종류 this의 전환에는 보안에 관련된 또다른 문제점이 있다. 함수가 어떻게 작성 되었던 간에 우연히 그 버그가 알려지기 전까지는 그 함수를 사용하는 사용자를 통제할 수 없다. 예제: 얇은 화살표 함수(->로 정의됨)가 있다고 가정하자. 이 표기법은 dynamic this와 lexical this간의 전환을 가능하게 해준다.


``` javascript
    let objectCreator = {
        create: function (secret) {
            return {
                secret: secret,
                getSecret: () -> {
                    return this.secret;
                }
            };
        },
        secret: "abc"
    }
```


아래는 보통 obj를 사용하는 방법이다.


``` javascript
    let obj = objectCreator.create("xyz");
    // dynamic this:
    console.log(obj.getSecret()); // xyz
```


아래는 공격자가 objectCreator.secret에 어떻게 접근하는지에 대한 내용이다.


``` javascript
    let obj = objectCreator.create("xyz");
    let func = obj.getSecret;
    // lexical this:
    console.log(func()); // abc
```


### 4.2 해결책 아님: call이나 apply 메소드로 this를 결합
이건 문제가 많다. 왜냐하면 call 또는 apply는 lexical this를 참조하는 화살표 함수를 뜻하지 않게 깰 수 있기 때문이다. 따라서 이 방법은 불안정한 해결책이다.


### 4.3 축약형에 대한 변론
나는 화살표 함수에 optional dynamic this가 필요하지 않다고 생각한다. optional dynamic this 기능은 부분적으로 화살표 함수의 단순함을 파괴하게 된다. 더욱이, dynamic this 사용이 가능한 메소드는 객체 지향의 철학에 반대된다. API가 인수로 this를 서브루틴에게 전달할 때마다 API는 진짜 매개변수를 대신 소개해야 한다. 이미 제이쿼리에서 each 메소드에서는 이 방법을 사용할 수 있도록 해놨다. 

``` javascript
    $(".someCssClass").each(function (i, ele) { console.log(ele) });
```


또다른 방법으로는 일반 함수를 사용하는 것이다. 또는 화살표 함수를 일반 함수로 감싸고, 앞에서 뒤로 this를 전달과 동시에 미리 정의된 매개변수를 전달하는 것이다. 아래의 코드에서 curryThis 메소드는 감싸는 역할을 한다.


``` javascript
    $(".someCssClass").each(
        (that, ele) { console.log(that) }.curryThis()
    );
```


curryThis는 아래와 같이 구현될 수 있다.


``` javascript
    Function.prototype.curryThis = function () {
        var f = this;
        return function () {
            var a = Array.prototype.slice.call(arguments);
            a.unshift(this);
            return f.apply(null, a);
        };
    };
```


## 5. 메소드 정의
화살표 함수의 존재로 인해 dynamic this와 lexical this에 대한 선택은 점점 자동화되고 있다.


- 서브루틴이 필요하다면? 화살표 함수를 사용해서 lexical this를 가진다.

- 메소드가 필요하다면? 일반 함수를 사용해서 dynamic this를 가진다.


두 번째 방법이 최선의 방법은 아니다. 메소드를 정의할 때 function 키워드를 고려하는 것은 오해를 사기 쉽다. 그리고 여전히 이 두 종류의 함수에 대해 생각해봐야 한다. 사람들은 아마도 메소드를 정의하기 위해 실수로 화살표 함수를 사용할 수도 있다. (이 경우 this가 실행되자마자 에러를 반환한다) 이러한 이슈를 해결하기 위해 ES6에서는 메소드를 정의하는 구문을 별도로 제공한다. 이건 메소드를 정의하는 더욱 간편한 방법이다. 또한, 메소드 정의는 ES6의 또 다른 기능인 [super 참조](http://www.2ality.com/2011/11/super-references.html)를 가능하게 해준다. 이를 위해 함수는 자신이 어떤 객체에 저장 되어있는지 알 필요가 있다. 메소드 정의는 자동으로 이 정보를 함수에 추가해준다. 메소드 정의는 두 가지 문맥에서 사용될 수 있다: 클래스 정의와 객체 리터럴 방법


### 5.1 클래스 정의에 사용되는 메소드 정의
“maximally minimal classes”라고 불리는 ES6를 위한 한 제안이 현재 논쟁 중이다. 예를 들어


``` javascript
    class Point {
        constructor(x, y) {
            this.x = x;
            this.y = y;
        }
        // 메소드 정의:
        dist() {
            return Math.sqrt((this.x*this.x)+(this.y*this.y));
        }
    }
```


만약 이 제안이 수용된다면 클래스 정의는 더 좋아질 것이다. 또한 자바스크립트 프레임워크끼리 코드 공유하는 것을 더 쉽게 만들어 줄 것이다. (현재는 서로 다른 상속 API를 사용함) 생성자 함수 부분이 먼저 해석되는 기본 개념은 클래스 정의에서도 바뀌지 않았다. dist는 Point.prototype.dist와 같은 의미를 가지는 메소드 정의다. 보다시피 메소드 정의는 기존 방법에 비해 훨씬 글자 수가 줄어들었고, 화살표 함수를 사용하고픈 잘못된 생각이 떠오르지 않게 해준다.


### 5.2 객체 리터럴에 사용되는 메소드 정의
메소드 구문 정의가 사용되는 첫 번째 단계는 위에서 설명한 클래스 정의에서였다. 다음으로는 객체 리터럴에서도 메소드 정의를 사용할 수 있다. 예를 들어: 


``` javascript
    let jane = {
        name: "Jane",
        
        // 메소드 정의:
        logHello(friends) {
            friends.forEach(friend => {
                console.log(this.name + " says hello to " + friend)
            });
        }
    }
```


다시 한 번 말하지만, 코드에서는 logHello가 function 키워드로 선언되지 않았다. 이건 메소드 처럼 보이고 dynamic this를 가진다. 만약 이미 존재하는 객체에 새로운 메소드를 추가해야할 때에는 어떻게 해야할까?


``` javascript
    obj.method1 = function (...) { ... };
    obj.method2 = function (...) { ... };
```


우리는 이전에 계속 사용했던 function 정의법이다. 하지만 ES6에서는 이 방법 대신에 객체 리터럴 방법을 사용할 수 있게 해준다.


``` javascript
    Object.assign(obj, {
        method1(...) { ... },
        method2(...) { ... }
    });
```


## 6. 어떤 게 진짜 필요할까?
실제 자바스크립트 코드에서 어떤 게 필요한지 알기 위해, Kevin Smith는 수 많은 진영(jQuery, Facebook, Node.js, etc.)을 조사했다. 거기에는 20667개의 서로 다른 함수 표현식을 포함하고 있었다. 그는 그것들 중에서 아래와 같은 분포를 찾아냈다.

| **Arrow function candidates (AFCs)**  | 55.7% |
| :------------ | ------------: |
|     Expression body (return expr) | 8.9% |
|     Object literal body (return {...}) | 0.14% |
|     Block with single statement | 20% |
|     Block with multiple statements | 26.7% |
| **Methods (in object literal, with this)**  | 35.77% |
| **AFCs or methods**  | 91.46% |
| **Functions with this (outside object literal)**  | 8.54% |


용어 설명
- 화살표 함수 후보(AFCs)는 dynamic this를 사용하지 않은 함수 표현식을 의미한다. 이 함수는 몸체의 this를 참조하지 않는다. 
- "Expression body"는 “object literal body”의 상위 구조다.
- 객체 리터럴에 있고, this를 참조하지 않는 함수 표현식은 AFCs다. 이 경우 함수를 감싸고 있는 객체는 네임스페이스 역할을 한다.


dynamic this를 다루는 많은 함수는 아마도 존재하는 객체에 추가된 메소드일 것이다. 이제부터 이 방법은 ES6에 있는 Object.assign을 통해서도 가능하다. 뿐만 아니라, 이 설문조사는 단지 대략적인 견적을 알아보기 위함이었기 때문에 추가 분석 노력은 가치가 없었다. 표에 있는 “functions with this” 카테고리는 this가 바인딩된 함수를 포함한다. 예를 들어


``` javascript
    function (...) { ... }.bind(this)
```


명백히, 이것들은 AFCs다.


이 자료는 자바스크립트에서 서브루틴(lexical this 사용)과 메소드를 각각 정의하는 쉬운 방법이 필요하다는 것을 의미한다. 그렇기 때문에 ES6에서는 옳은 결정을 했다. 또 다른 의미로 dynamic this가 적용된 화살표 함수는 그렇게 중요하지 않다. 마지막으로 expression body에서 객체 리터럴을 괄호로 묶는 형식은 거의 문제가 안 된다. 겨우 0.14%의 함수 표현식이 객체 리터럴을 반환한다.


## 7. 결론
ES6에서는 구시대 function 사용법을 더이상 찾기 힘들 것이다.
- 생성자 함수는 클래스 정의로 대체될 수 있다.
- 서브루틴은 화살표 함수로 대체될 수 있다.
- 메소드는 메소드 정의로 대체될 수 있다.


나는 숙련된 자바스크립트 프로그래머들이 이 개념에 대한 명백한 분리가 익숙해지기까지 쉽지는 않을 것이라고 생각한다. 하지만, 내 생각에 이 개념들은 자바스크립트를 더욱 단단하게 만들어준다. 현재 사용중인 function은 위 3가지 개념에 믹스인 되어있기 때문에 이해하기가 어렵다. 하지만, 새로운 개념이 나왔다고 해서 기존 방법이 없어지는 것은 아니다. 별도로 필요하다면 언제나 사용가능하다.