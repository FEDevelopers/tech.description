> 이 문서는 http://www.2ality.com/2012/04/arrow-functions.html 를 번역한 내용입니다.


자바스크립트에서 메소드 안에서 함수를 만드는 부분은 올바르게 사용하기 어렵다. 특별한 변수인 ```this``` 때문이다. ES6에서는 ```화살표 함수```와 ```메소드 정의``` 두 가지의 개념을 소개함으로써, 이러한 부분을 쉽게 해준다. 이 글은 이 2가지가 어떤 것들이며 어떻게 우리를 도와줄 수 있는지 알려준다.


## 용어 소개
이 글에서는 두 가지 개념을 구분한다.
- 서브루틴은 자신 그 자체로 존재하며 직접 호출된다. 일반적으로 "함수"가 더 나은 단어이지만, 자바스크립트에서 함수라는 의미는 다양한 의미를 가지고 있다. 그래서 자바스크립트 개발자들이 흔히 말하는 "메소드가 아닌 함수"는 서브루틴이라고 한다. 이 글에서는 함수라는 말 대신 서브루틴이라는 용어를 사용할 것이다. 왜냐하면 서브루틴이라는 단어보다 더 나은 걸 찾지 못했기 때문이다. (콜백, 프로시저도 서브루틴과 비슷한 맥락)
- A method is part of an object o and called via an object (which isn’t necessarily the same object as o).
메소드는 객체의 한 부분이고, 객체를 통해 호출된다. 


자바스크립트에서 서브루틴과 메소드는 함수라는 키워드로 수행된다. 예를 들어


``` javascript
    var obj = {
        myMethod: function () {
            setTimeout(function () { ... }, 0);
        }
    }
```


myMethod is a method, the first argument of setTimeout() is a subroutine. Both are implemented by functions. Before we can explain the implications of that, we need to make a few more definitions. Whenever a function is called, it is in two kinds of scopes (or contexts): Its lexical scopes are the syntactic constructs that surround it (a trait of the source code or the lexicon). Its dynamic scopes are the function that called it, the function that called that function, etc. Note the nesting that occurs in both cases. Free variables are variables that aren’t declared within a function. If such variables are read or written, JavaScript looks for them in the surrounding lexical scopes.
여기서 myMethod는 메소드고, setTimeout()의 첫 번째 인수는 서브루틴이다. 두 가지는 함수로 수행된다. 이들의 영향에 대해 설명하기 이전에, 더 많은 정의를 살펴볼 필요가 있다. 함수가 호출될 때마다 두 가지의 스코프(=문맥)를 가진다. 함수의 ```lexical scope```는 함수를 둘러싸고 있는 코드로부터 상속받는 구조다. ```dynamic scopes```는 함수다. 


함수는 메소드 실행으로도 잘 동작한다. 함수는 ```this```라 불리는 특별한 변수를 가진다. ```this```는 메소드가 실행되는 객체를 참조한다. 다른 자유 변수들과는 다르게 ```this```는 둘러싸고 있는 ```lexical scopes```를 참조하지 않는다. ```this```는 실행을 통해 함수에게 전달된다. 함수는 동적으로 ```this```를 전달 받기 때문에 여기서의 this는 ```dynamic this```으로 불린다.


Functions don’t work well as implementations of subroutines, because this is still dynamic. The subroutine call sets it to undefined in strict mode [1] and to the global object, otherwise. That is unfortunate, because the subroutine has no use for its own this, but it shadows the this of the surrounding method, making it inaccessible. For example:
함수는 서브루틴 실행으로는 잘 동작하지 않는다. 왜냐하면 여기서의 this는 동적이기 때문이다. 서브루틴 호출은 this를 전역 객체로 가지거나 [엄격 모드](http://www.2ality.com/2011/01/javascripts-strict-mode-summary.html)에서는 undefined로 셋팅된다. 이건 안 좋은 의미다 왜냐하면, 서브루틴은 this를 사용하지 않기 때문이다. 하지만 서브루틴을 감싸고 있는 메소드의 this를 상속받기 때문에 asdfsf. 예를 들어


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


The argument of forEach is a subroutine. You need the assignment at (*) so that it can access logHello’s this. Clearly, subroutines should have lexical this, meaning that this should be treated the same as other free variables and looked up in the enclosing lexical scopes. that = this is a good work-around. It simulates lexical this, if you will. Another work-around is to use bind:
fotEach의 인수는 서브루틴이다. logHello의 this에 접근하기 위해서는 (*) 지점에서 보이는 별도의 선언이 필요하다. 서브루틴은 명백히  ```lexical this```를 가진다. 이 말은 서브루틴에서의 this 역시 다른 자유 변수와 동일한 취급 받는다는 것을 의미한다. 그리고 인접한 ```lexical scopes```를 참조한다. ```that = this```은 좋은 해결 방법이다. 이 방법으로 ```lexical this```에 접근할 수 있다. 다른 해결 방법으로는 bind를 사용하는 것이다.


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


Now the argument of forEach has a fixed value for this. I can’t be changed, not even via call or apply. There are three problems with any simulation of lexical this:
이제 forEach의 인수(function)는 고정 값 this를 가진다. call이나 apply를 호출해도 이제는 더이상 변경할 수 없다. ```lexical this```에는 3가지 문제점이 있다. 
- You have to know how JavaScript’s quirky this works (which you should neither want to nor need to).
우리는 자바스크팁트의 this가 별나게 동작한다는 것을 알아야 한다.
- You have to constantly be alert as to when to simulate lexical this. That choice should be automatic and not require extra thought. A simulation incurs a performance and memory cost, so you’ll want to avoid it if you don’t need it.
우리는 ```lexical this```를 해석 할 때마다 끊임없이 조심해야 한다. 그 선택은 자동화 되어야하고 추가 생각을 필요로 하지 않아야 한다. 해석은 성능과 메모리 비용을 초래한다. 그래서 필요하지 않는 경우를 방지할 수 있다.
- There is more to type and more visual clutter.


심지어 커피스크립트의 화살표 함수 역시 첫 번째와 두 번째 문제를 가진다.


## 2. 화살표 함수
ECMAScript.next’s arrow functions are better suited for defining subroutines than normal functions, because they have lexical this. Using one for forEach looks as follows.
ES6의 [화살표 함수](http://wiki.ecmascript.org/doku.php?id=strawman:arrow_function_syntax)는 서브루틴을 정의할 때 일반 함수를 사용하는 것보다 더 낫다. 화살표 함수는 ```lexical this```를 가지기 때문이다. 위의 forEach에 화살표 함수를 적용하면 아래와 같다.


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


뚱뚱한 화살표는 커피스크립트와 호환이 되도록 선택되었다. 그래서 커피스크립트에서의 뚱뚱한 화살표 함수와 기능이 매우 비슷하다.


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


## 2.1 화살표 함수에서의 ```lexical this```
화살표 함수에서의 ```lexical this```는 인접 문맥을 상속받는다. 


``` javascript
    x => x + this.y
```


이 화살표 함수는 아래의 문법 설탕과 같다.


``` javascript
    function (x) { return x + this.y }.bind(this)
```


위의 함수 표현식은 두 개의 함수를 생성한다. 첫 번째로 매개변수 x와 dynamic this가 있는 원본 익명 함수가 생선된다. 두 번째로 this가 결합된 바인딩 함수다. 반면에 화살표 함수는 마치 bind를 붙여서 생성한 것처럼 동작한다. 그렇기 때문에 상대적으로 메모리를 덜 먹는다. 단지 하나의 엔티티, 이 특이한 함수(=화살표 함수)의 this는 외부 함수의 this로 가리키고 있는 함수, 가 생성된다. 


## 화살표 함수 vs 일반 함수


화살표 함수는 일반 함수와 3가지 측면에서 다르다. 첫째로 화살표 함수는 항상 바인딩 된 this를 가진다. 둘째로 화살표 함수는 생성자로 사용할 수 없다. 왜냐하면 [[Construct]](new 키워드와 일반 함수가 함께 실행될 수 있게 해줌)라는 내부 메소드와 prototype 속성이 없기 때문이다. 그러므로 new (() => {}) 는 에러를 반환한다. 셋째로 화살표 함수는 ES6의 새로운 구조이기 때문에 새로운 방식의 인수 조작([기본 매개변수](http://wiki.ecmascript.org/doku.php?id=harmony:parameter_default_values), [나머지 매개변수](http://wiki.ecmascript.org/doku.php?id=harmony:rest_parameters) 등등)이 가능하고, 더이상 arguments 키워드를 지원하지 않는다. 새로운 방식의 인수 조작으로 arguments로 했던 모든 것들이 가능하기 때문에 더이상 필요할 일이 없기 때문이다.


이러한 단순함을 제외하고, 화살표 함수와 일반 함수의 외적 차이는 없다. 예를 들어, typeof와 instanceof는 예전처럼 사용할 수 있다.


``` javascript
    > typeof () => {}
    'function'
    
    > () => {} instanceof Function
    true
```


## 논쟁 중인 구문 변화
아래의 구문 변화는 여전히 논쟁 중이며 아마도 ES6에 추가되지 않을 것이다.

- 매개변수 생략:


``` javascript
    => { ... }
```


자바스크립트의 [자동 세미콜론 삽입](http://www.2ality.com/2011/05/semicolon-insertion.html)은 표현식이 이전 라인에서 끝났음에도 불구하고 계속 이어지는 것으로 잘못 인식되는 위험성이 있다. 예를 들어, 아래의 코드를 보자.


``` javascript
    var x = 3 + a
    => 5
```


위의 두 라인은 아래와 같이 해석된다.


``` javascript
    var x = 3 + (a => 5);
```


하지만 보통 화살표 함수는 표현식 문맥 내부, 중첩 된 구문 내부에 사용된다. 그렇기 때문에 자동 세미콜론 삽입이 큰 문제가 되지 않을 거라고 생각한다. 만약 자바스크립트가 커피스크립트처럼 [significant newlines](http://www.2ality.com/2011/11/significant-newlines.html)기능을 가진다면 이 문제는 완벽히 해결된다.


- 몸체 생략: 


``` javascript
    x =>
```


이건 하나의 매개변수를 가지고 항상 undefined를 반환하는 함수다. 이 구문은 [void 연산자](http://www.2ality.com/2011/05/void-operator.html)와 같다. 이 기능이 얼마나 유용한지는 잘 모르겠다.


- Named arrow functions: JavaScript already has named function expressions, where you give a function a name so that it can invoke itself. That name is local to that function, it doesn’t leak into any surrounding scopes. Named arrow functions would work the same. For example:
기명 화살표 함수: 자바스크립트는 이미 기명 함수 표현법을 가졌다. 함수에 이름을 부여하고 이 이름으로 함수를 실행할 수 있다. That name is local to that function, it doesn’t leak into any surrounding scopes. 기명 화살표 함수는 동일하게 동작한다. 예를 들어, 아래의 코드를 보자.


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


## Parsing arrow functions
Most JavaScript parsers have a two-token look-ahead. How then is such a parser supposed to distinguish between the following two expressions?
대부분의 자바스크립트 파서는 two-token look-ahead를 가진다. 파서는 아래의 두 표현식을 어떻게 구분할까?


``` javascript
    (x, y, z)
    (x, y, z) => {}
```


첫 번째 표현식은 괄호 내에 있는 세 개의 변수에 적용되어 있는 쉼표 연산자다. 두 번째 표현식은 화살표 함수다. 만약 이 두 개를 구별하고 싶다면, 화살표가 있는지 없는지를 체크하면 된다.


To parse both of the above with a limited look-ahead, one uses a trick called cover grammar: One creates a grammar rule that covers both use cases, parses and then performs post-processing. If an arrow follows the closing parenthesis, some previously parsed things will raise an error and the parsed construct is used as the formal parameter list of an arrow function. If no arrow follows, other previously parsed things will raise an error and the parsed construct is an expression in parentheses. Some things can only be done in a parenthesized expression:
제한된 look-ahead로 위 두 개를 분석하기 위해 cover grammer라 불리는 트릭을 사용한다. 


``` javascript
    (foo(123))
```


Other things can only be done in a parameter list. For example, declaring a rest parameter:
다른 건 오직 매개변수 리스트에서만 실행된다. 예를 들어, 나머지 매개변수를 정의 할 때,


``` javascript
    (a, b, ...rest)
```


양쪽 문맥에서 여러가지가 실행된다.


``` javascript
    (title = "no title")
```


위는 표현식 문맥에서의 선언과 화살표 함수 문맥에서의 기본 매개변수 정의다.


## 4. 가능한 화살표 함수의 기능: 선택적인 dynamic this
화살표 함수의 한 가지 기능으로 dynamic this를 바꿀 수 있다. 이 기능은 계속 지연되고 있지만 아마도 추가될 것이다. 이 기능의 활용은 아래와 같다. 제이쿼리에서 몇몇 인수들은 this를 암시적인 매개변수로 가지고 있는 서브루틴이다.


``` javascript
    $(".someCssClass").each(function (i) { console.log(this) });
```


현재는 each 함수의 인수로 화살표 함수를 사용할 수 없다. 왜냐하면 화살표 함수에는 this를 결합하기 위해 call과 apply 메소드를 사용할 수 없다. dynamic this를 바꾸기 위해서는 아래와 같이 해야한다.


``` javascript
    $(".someCssClass").each((this, i) => { console.log(this) });
```


Normally, no parameter can have the name this, so the above is a good marker for an arrow function with dynamic this.
보통 매개변수가 없는 건 this라는 이름을 가질 수 있다. 그러므로 위의 코드는 좋은 마커다. dynamic this가 적용된 화살표 함수


Shouldn’t there be a simpler solution for optional dynamic this? Alas, two seemingly simpler approaches won’t work.
선택적인 dynamic this를 사용하기 위한 간단한 해결책이 있을까? 


## 4.1 해결책 아님: dynamic this와 lexical this를 바꿈
함수가 어떻게 실행되느냐에 따라 dynamic this와 lexical this로 바뀐다. 만약 메소드로 실행된다면, dynamic this를 사용한다. 만약 서브루틴으로 실행된다면, lexical this를 사용한다. 이러한 방식은 함수가 잘못된 방식으로 실행되는 문제를 야기한다. 만약 함수가 서브루틴으로 실행되어야 하는데 메소드로 실행된다면 lexical context로 되어야할 this를 잘못 참조하게 된다. 만약 메소드로 실행해서 인스턴스에 접근하려고 의도했는데 함수로 실행된다면, 의도와는 다르게 외부 함수의 this에 접근하게 된다. ECMAScript 5의 엄격모드는 에러를 반환함으로써 이 이슈를 해결한다. 만약 메소드가 서브루틴으로 실행된다면, 여기서의 this는 undefined이기 때문에 속성에 접근하자마자 에러를 반환한다. 그리고 서브루틴은 바인딩 함수가 되기 이전에는 그 this로 아무것도 할 수 없게 된다. this는 결코 바뀌지 않는다. ES6에서도 동일하게 동작하지만, 잘못된 함수 사용을 방지해준다. 화살표 함수는 기본적으로 서브루틴이 될 것이고, 이는 고정된 lexical this를 가지게 된다. 메소드 정의(아래 코드 참고)는 기본적으로 메소드가 될 것이고 반드시 객체를 통해서만 실행이 될 수 있다. (객체로부터 추출되어 사용되는 메소드를 제외)


Another problem with switching between the two kinds of this is security-related: You can’t control how a function you write will be used by clients, opening the door to inadvertently exposed secrets. Example: Let’s pretend there are “thin arrow functions” (defined via ->) that switch between dynamic and lexical this, on the fly.
두 가지 종류의 this로 바뀌게 되는 또다른 문제점은 보안에 관련되어있다. You can’t control how a function you write will be used by clients, opening the door to inadvertently exposed secrets. 예제: 얇은 화살표 함수(->로 정의됨)가 있다고 가정하자. 


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


## 4.2 해결책 아님: call이나 apply 메소드로 this를 결합
이건 문제가 많다. 왜냐하면 call 또는 apply는 lexical this를 참조하는 화살표 함수를 뜻하지 않게 깰 수 있기 때문이다. 따라서 이 방법은 불안정한 해결책이다.


## 4.3 Arguing in favor of simplicity
I don’t think that we need optional dynamic this for arrow functions, it partially destroys their simplicity. Furthermore, having dynamic this available in something that is not a method goes against the grain of object-orientation. Every time an API hands an argument to a subroutine via this, it should instead introduce a real parameter. jQuery already allows you to do that for the each method:
나는 화살표 함수에 optional dynamic this가 필요하지 않다고 생각한다. 이것은 부분적으로 화살표 함수의 단순함을 파괴하게 된다. Furthermore, having dynamic this available in something that is not a method goes against the grain of object-orientation. API가 서브루틴에게 this를 통해 인수를 전달할 때마다 진짜 매개변수를 대신 소개한다. 제이쿼리는 each메소드에 이러한 방법을 사용하도록 이미 허락했다. 


``` javascript
    $(".someCssClass").each(function (i, ele) { console.log(ele) });
```


Another option is to use a normal function. Or you can wrap a normal function around an arrow function and pass this from the former to the latter, as an additional (prefixed) parameter. In the following code, method curryThis performs such wrapping.
또다른 방법으로는 일반 함수를 사용하는 것이다. 또는 화살표 함수를 일반 함수로 감싸서 마치 미리 정의된 추가 매개변수처럼 this를 전달하는 것이다. 아래의 코드에서 curryThis 메소드는 감싸는 역할을 한다.


``` javascript
    $(".someCssClass").each(
        (that, ele) { console.log(that) }.curryThis()
    );
```


curryThis는 아래와 같이 실행될 수 있다.


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
화살표 함수로 dynamic this와 lexical this에 대한 선택은 점점 자동화되고 있다.


- 서브루틴이 필요하다면? 화살표 함수를 사용해서 lexical this를 가진다.

- 메소드가 필요하다면? 일반 함수를 사용해서 dynamic this를 가진다.


두 번째 방법이 최선의 방법은 아니다. 메소드를 정의할 때 function 키워드를 고려하는 것은 오해를 사기 쉽다. 그리고 여전히 이 두 종류의 함수에 대해 생각해봐야 한다. 사람들은 아마도 메소드를 정의하기 위해 실수로 화살표 함수를 사용할 수도 있다. (이 경우 this에 접근하자마자 에러를 반환한다) 이러한 이슈를 해결하기 위해 ES6에서는 메소드를 정의하는 구문을 별도로 제공한다. 이건 메소드를 정의하는 더욱 간편한 방법이다. 또한, 메소드 정의는 ES6의 또 다른 기능인 [super 참조](http://www.2ality.com/2011/11/super-references.html)를 가능하게 해준다. 이를 위해 함수는 자신이 어떤 객체에 저장 되어있는지 알 필요가 있다. 메소드 정의는 자동으로 이 정보를 함수에 추가해준다. 메소드 정의는 두 가지 문맥에서 사용될 수 있다: 클래스 정의와 객체 리터럴 방법


## 클래스 정의에 사용되는 메소드 정의