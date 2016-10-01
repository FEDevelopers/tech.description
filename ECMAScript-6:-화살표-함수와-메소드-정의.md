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
여기서 myMethod는 메소드고, setTimeout()의 첫 번째 매개변수는 서브루틴이다. 두 가지는 함수로 수행된다. 이들의 영향에 대해 설명하기 이전에, 더 많은 정의를 살펴볼 필요가 있다. 함수가 호출될 때마다 두 가지의 스코프(=문맥)를 가진다. 함수의 ```lexical scope```는 구문 구조다. 이걸 둘러싸고 있는. ()