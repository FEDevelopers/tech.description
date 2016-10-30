> https://www.codecademy.com/blog/78 에 글을 번역한 것 입니다.

언제 세미콜론이 필요합니까? 여기 당신을 위한 치트키가 있습니다..

***

#필수사항: 두개 statements가 같은 줄에 있을때
 당신이 2개 또는 많은 statements를 같은 줄에 작성하면 세미콜론은 의무입니다.
```` javascript
var i = 0; i++        // <-- semicolon obligatory
                      //     (but optional before newline)
var i = 0             // <-- semicolon optional
    i++               // <-- semicolon optional
````
#옵션사항: statements 이후에
자바스크립트 세미콜론은 별도 statements에서 사용됩니다. 그러나 만약 줄바꿈이 된다면 세미콜론은 생략할 수 있습니다.(또는 한개의 `{}`블록문이라면).
statements는 어떤 일을 하기 위해 컴퓨터에게 알려주는 코드 조각입니다. 여기 일반적인 statements 타입들이 있습니다.

```` javascript
var i;                        // variable declaration
i = 5;                        // value assignment
i = i + 1;                    // value assignment
i++;                          // same as above
var x = 9;                    // declaration & assignment
var fun = function() {...};   // var decl., assignmt, and func. defin.
alert("hi");                  // function call
````

위의 모든 statements은 세미콜론으로 종료될 수 있습니다. 하지만 위의 코드 모두 꼭 세미콜론을 붙일 필요는 없습니다. <br>
몇몇은 모든 statements의 종료마다 세미콜론을 써주는 것을 좋은 습관이라고 고려합니다. 그러한 습관은 당신 코드를 해석하기 쉽게 만들어주고, 쉽게 압축할 수 있게 해줍니다.(세미콜론을 사용해서 코드의 종료를 명확하게 하면) 코드에서 줄바꿈(br이나 \n)을 제거하더라도 몇몇 statements이 같은 줄에서 불완전하게 끝나는 것에 대해 걱정할 필요가 없습니다.

#Avoid!
##1. {}가 닫힌 후에
 당신은 `}` 이 닫힌 이후에 세미콜론을 사용하면 안됩니다. `var obj={};`와 같은 할당문은 예외입니다.(위 참조)
```` javascript
// NO semicolons after }:
if  (...) {...} else {...}
for (...) {...}
while (...) {...}

// BUT:
do {...} while (...);

// function statement: 
function (arg) { /*do this*/ } // NO semicolon after }
````

##2. `if, for, while, switch`의 괄호(`)`) 후에 statement
`if` statement의 `{ }`이 후에 세미콜론을 사용해도 상관없습니다.(그것은 무시되어지고, 불필요하다고 warning을 보게됩니다.) 그러나 세미콜론이 속하지 않는 (`if, for, while, switch` statement의 `()`의 후와 같은)경우에는 좋은 생각이 아닙니다. 

```` javascript
if (0 === 1); { alert("hi") }

// equivalent to:

if (0 === 1) /*do nothing*/ ;
alert ("hi");
````

위 코드는 `alert "hi"` 가 실행 되지만, `0 === 1` 은 다릅니다. 이는 세미콜론 때문에 작동 할 수 있는 겁니다.<br>
자바스크립트는 당신이 비어있는 statment를 사용한 것이라고 생각하고, 그리고 오른쪽에 있는 모든 것은 더이상 `if` 조건에 속하지 않고 독립적으로 처리됩니다.

#물론 예외는 있습니다..
`for` 루프의 `()` 안에 세미콜론은 오로지 첫번째 이후와 두번째 statement에만 사용하며 세번째는 사용하지 않습니다.
```` javascript
for (var i=0; i < 10; i++)  {/*actions*/}       // correct
for (var i=0; i < 10; i++;) {/*actions*/}       // SyntaxError
````

#세미콜론을 해결 하는 방법
Codecademy 코드 에디터에 빌트인된 자바스크립트 문법교정 툴 [JSLint](http://www.jslint.com/)은 불필요한 세미콜론을 찾을 수 있는 좋은 방법입니다.

![alt jslint예제](https://i.imgur.com/gK0Pi2G.png)

JSLint는 코드라인에서 삼각형 노란색경고로 보여줄 것입니다. 마우스를 삼각형 위에 올리면 세미콜론이 불필요한것인지, 놓친것인지 말해줍니다.