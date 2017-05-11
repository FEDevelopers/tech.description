> 이 문서는 http://2ality.com/2013/10/typeof-null.html를 번역한 것입니다.


자바스크립트에서 ```typeof null```의 결과는 'object'이며 null을 객체라고 잘못 알려주고 있다. null은 원시타입이다. 이것은 불행하게도 수저오딜 수 없는 버그다. 왜냐하면 기존 코드에 부정적인 영향을 미칠 수 있기 때문이다. 이 버그의 역사에 대해 살펴보자.


```typeof null``` 버그는 자바스크립트의 첫 번째 버전의 잔존물이다. 이 버전에서 변수의 값은 32비트 단위로 저장되었는데, 1-3 비트에서는 타입 태그로 변수의 타입에 대해 저장했고, 나머지 비트에는 실제 데이터로 구성되었다. 아래는 그에 해당되는 5종류다.


- 000: 객체. 데이터는 객체에 대한 참조다.
- 1: 정수. 데이터는 31비트의 부호를 가진 정수다.
- 010: 실수. 데이터는 double 부동 소수점에 대한 참조다.
- 100: 문자. 데이터는 문자에 대한 참조다.
- 110: 참/거짓. 데이터는 참/거짓이다.


즉, 가장 낮은 비트는 1이었고, 그 타입 태그의 길이는 오직 1비트였다. 혹은 0인데, 그 길이는 4 종류의 타입을 표현하기 위해 2개의 비트가 추가로 있는 3비트였다.


2가지 값은 특별했다.


- undefined (JSVAL_VOID)은 정수 −230였다. (정수 범위를 벗어난 숫자).
- null (JSVAL_NULL)은 기계어 코드 NULL 포인터였다. 혹은 객체 타입과 0인 참조였다.


이제서야 왜 typeof null의 결과가 객체라고 나오는지 명확해졌다. 타입 태그를 조사하니 그 값이 "객체"였기 때문이다. 아래의 코드는 typeof 명령어가 정의된 엔진의 코드다.


``` C
    JS_PUBLIC_API(JSType)
    JS_TypeOfValue(JSContext *cx, jsval v)
    {
        JSType type = JSTYPE_VOID;
        JSObject *obj;
        JSObjectOps *ops;
        JSClass *clasp;

        CHECK_REQUEST(cx);
        if (JSVAL_IS_VOID(v)) {  // (1)
            type = JSTYPE_VOID;
        } else if (JSVAL_IS_OBJECT(v)) {  // (2)
            obj = JSVAL_TO_OBJECT(v);
            if (obj &&
                (ops = obj->map->ops,
                 ops == &js_ObjectOps
                 ? (clasp = OBJ_GET_CLASS(cx, obj),
                    clasp->call || clasp == &js_FunctionClass) // (3,4)
                 : ops->call != 0)) {  // (3)
                type = JSTYPE_FUNCTION;
            } else {
                type = JSTYPE_OBJECT;
            }
        } else if (JSVAL_IS_NUMBER(v)) {
            type = JSTYPE_NUMBER;
        } else if (JSVAL_IS_STRING(v)) {
            type = JSTYPE_STRING;
        } else if (JSVAL_IS_BOOLEAN(v)) {
            type = JSTYPE_BOOLEAN;
        }
        return type;
    }
```


위의 코드가 수행되는 단계는 아래와 같다.


- (1)에서 첫번째로 엔진은 v라는 값이 undefined(VOID)인지 검사한다. 이 검사는 동등 연산자를 통해 값을 비교하여 수행된다.
    

    ```#define JSVAL_IS_VOID(v)  ((v) == JSVAL_VOID)```


- (2)에서 해당 값이 객체 태그를 가졌는지 검사한다. 만약 (3)에서처럼 호출이 가능하거나 (4)에서처럼 내부속성인 ```[[Class]]```의 값이 함수를 의미하면 v는 함수다. 그렇지 않으면 객체를 의미한다. 이 부분이 바로 typeof null의 값이 할당되는 부분이다.


- 후속 검사는 숫자, 문자, 참/거짓을 위함이다. 더이상 아래와 같은 null에 대한 명시적인 검사는 없다.


    ```#define JSVAL_IS_NULL(v)  ((v) == JSVAL_NULL)```


이것은 매우 명백한 버그처럼 보일지 모르겠지만, 자바스크립트의 첫 번째 버전이 매우 짧은 시간에 탄생했다는 걸 잊지마라. 


(역자주: 자바스크립트는 10일만에 설계가 완성됨)