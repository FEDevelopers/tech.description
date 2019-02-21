> https://www.codecademy.com/blog/78 에 글을 번역한 것 입니다.

이것은 moderator Alex J.가 게스트로 올린 게시물입니다. 포럼에서 그를 보게되면 인사하십시오!  
언제 세미콜론이 필요합니까? 여기에 편리한 치트시트가 있습니다..

***

# 필수사항: 두개 명령문이 같은 줄에 있을때
 세미콜론은 같은 줄에 둘 이상의 명령문(*statements*)이있을 때만 필수입니다.

```` javascript
var i = 0; i++        // <-- semicolon obligatory
                      //     (but optional before newline)
var i = 0             // <-- semicolon optional
    i++               // <-- semicolon optional
````

# 옵션사항: 명령문 이후에
자바스크립트의 세미콜론은 명령문을 분리하는데 사용되지만, 명령문 다음에 줄바꿈이 있으면(또는 `{}`에 단하나의 명령문만 있는경우) 생략할 수 있습니다.  
명령문은 어떤 일을 지시하기 위해 컴퓨터에게 알려주는 코드 조각입니다. 다음은 가장 일반적인 명령문 유형들이 있습니다.

```` javascript
var i;                        // variable declaration
i = 5;                        // value assignment
i = i + 1;                    // value assignment
i++;                          // same as above
var x = 9;                    // declaration & assignment
var fun = function() {...};   // var decl., assignmt, and func. defin.
alert("hi");                  // function call
````


위 모든 명령문은 세미콜론으로 종료될 수 있습니다. 하지만 위의 코드 모두 꼭 세미콜론을 붙일 필요는 없습니다. <br>
어떤이들은 세미콜론으로 각 명령문을 종료하는 것이 좋은 습관이라고 생각합니다. 그러한 습관은 당신 코드를 해석하기 쉽게 만들어주고, 쉽게 압축할 수 있게 해줍니다.(세미콜론을 사용해서 코드의 종료를 명확하게 하면) 코드에서 줄바꿈(br이나 \n)을 제거하면, 같은 줄에서 분리되지 않은 명령문에 대해 걱정할 필요가 없습니다.

# Avoid!
## 1. {}가 닫힌 후에
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

## 2. `if, for, while, switch`의 괄호(`)`) 후에 명령문
`if` 문의 `{}` 후에 세미콜론을 사용해도 상관없습니다. (무시되어지고, 불필요하다고 warning을 보게됩니다.) 그러나 세미콜론이 속하지 않는 (`if, for, while, switch` 문의 `()`의 후와 같은)경우에는 좋은 생각이 아닙니다. 

```` javascript
if (0 === 1); { alert("hi") }

// equivalent to:

if (0 === 1) /*do nothing*/ ;
alert ("hi");
````

위 코드는 `alert "hi"` 가 실행 되지만, `0 === 1` 은 다르므로 세미콜론 때문에 발생하지 않습니다.  
자바스크립트는 당신이 비어있는 명령문을 사용한 것이라고 생각하며, 오른쪽에 있는 모든 것은 더이상 `if` 조건에 속하지 않고 독립적으로 처리됩니다.

# 물론 예외는 있습니다..
`for` 루프의 `()` 안에 세미콜론은 오로지 첫번째 이후와 두번째 명령문에만 사용하며 세번째는 사용하지 않습니다.
```` javascript
for (var i=0; i < 10; i++)  {/*actions*/}       // correct
for (var i=0; i < 10; i++;) {/*actions*/}       // SyntaxError
````