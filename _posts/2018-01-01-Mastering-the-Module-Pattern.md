> https://toddmotto.com/mastering-the-module-pattern/ 를 번역한 글입니다.

저는 자바스크립트 모듈 패턴 팬이며, 패턴에 유스 케이스와 다른점에 대해 공유하고 왜 중요한지 설명하고 싶습니다. 모듈페턴은 우리가 [디자인 패턴](http://code.tutsplus.com/tutorials/understanding-design-patterns-in-javascript--net-25930) 이라고 부르는 것이며, 많은 이유로 매우 유용한 패턴입니다. 모듈패턴(변종인 *Revealing Module Pattern*)에 대한 가장 큰 매력은 프로그램 디자인을 복잡하게 만들지 않고, 범위를 좁게 한다는 것입니다.  

또한 매우 간단하고 읽기 쉽고, 사용하기 쉽게 유지하여 객체를 사용하는데 매우 나이스한 방법이고, 반복적인 프로토타입 선언을 사용하여 코드를 부풀리지 않게합니다. 난 모듈의 멋진 부분들에 관한 나의 인사이트를 공유하고, 어떻게 변종과 특징들을 마스터 할 수 있는지에 대해 공유하고자합니다.

# Creating a Module
모듈이 무엇을 주는지 이해하기 위해서, 당신은 다음에 나오는 `function`이 무엇을 하는지 이해해야 합니다.

```javascript
(function(){
	// code
})();
```

함수를 선언하면, 즉시 자기 함수 호출을 합니다. 이들은 [즉시호출함수표현식](http://benalman.com/news/2010/11/immediately-invoked-function-expression)라고도 불리며, 이 함수는 새로운 스코프를 생성하고, `private`(역자주:메서드 개인정보)를 생성합니다. 자바스크립트는 `private`를 가지고 있지않지만, 새로운 스코프를 생성하면 모든 함수로직을 그 안에 모두 포함할 때 이를 에뮬레이션 합니다. 그 다음 *idea* 는 필요한 부분만 반환하고, 다른 코드는 전역 스코프에서 제외됩니다.  

새로운 스코프가 생성된 후, 우리가 반환하는 모든 메서드에 접근할 수 있도록 코드에 네임스페이스가 필요합니다. 익명 모듈을 위한 네임스페이스를 만들어보겠습니다.

```javascript
var Module = (function(){
	// code
})();
```

이제 우리는 전역 스코프에 선언된 모듈을 가지게 됩니다. 이제 우리가 좋아하는 어디든 호출 할 수 있으며, 심지어 다른 모듈에도 전달 할 수도 있습니다.

# Private methods
당신은 자바스크립트에 `private 메서드`에 대해 많이 보고 듣게됩니다. 그러나 자바스크립트는 엄격한 `private 메서드` 가지고 있지 않지만, 우리는 동등하게 작동시킬 수 있습니다.  


당신이 물어보는 `private 메서드`는 무엇입니까? `private 메서드` 는 어느것도 그것들이 속한 스코프 밖에서 *사용자/개발자/해커* 가 보거나 호출 하지 않게 하려는 것입니다. 우리는 서버 호출을 하거나 민감한 데이터를 포스팅 할 수도 있습니다. 하지만 우리는 이런 기능을 공개적으로 노출시키고 싶지 않지만, 그들은 어떤 포스팅도 하지 않고 우리 코드를 이용할 수 있었습니다. 그래서 우리는 우리 코드를 보호하면서 클로저를 생성하고 더 현명해질수 있었습니다.(자바스크립트가 할 수 있는 최선을) 그러나 보호에 관한 전부는 아니였지만, 네이밍 충돌이 있었습니다. 당신이 처음 jQuery/Javascript를 작성하였을 때, 모든 코드는 한 파일에 있었으며, 그것들은 모두 `function, function, function` 이 였을 겁니다. 조금이라도 당신이 이런 것들이 모두 `전역`이란 걸 알았다면, 아마도 당신은 어떤 시점에서 고통 받았을 겁니다. 그렇게 당신은 왜, 어떻게 변경 해야하는지 배우게 됩니다.  

따라서 우리가 새로 생성한 모듈 스코프를 사용하여 스코프 밖에서 우리 메서드에 접근하지 못하게 합니다. 초보자를 위한 모듈패턴, 다음 예제는 `private 메서드`를 어떻게 정의하는지 이해하는데 도움이 될것 입니다.

```javascript
var Module = (function(){
	var privateMethod = function(){
		// do something
	}
})();
```

위 예제는 새로운 스코프 안에서 지역적으로 선언된 `privateMethod` 함수를 가진 함수를 선언 하였습니다. 만약 우리 모듈 밖에 어디서든 호출을 시도하려고 하면 자바스크립트는 에러를 뿜으면서 멈출 것입니다. 우리는 우리 메서드가 누군가에게 호출되지 않기를 원합니다. 특히 서버로 왔다갔다 하거나 데이터를 조작하는 메서드에 필요합니다.

# Understanding "return"
일반적인 모듈은 `return` 을 사용하며, `Module`에 `Object` 를 반환합니다. `Object`에 바인딩된 메서드들은 `Module` 네임스페이스로 접근 할 수 있습니다.  
다음은 `function`을 가진 `Object`를 속성으로 반환하는 실제 예제입니다.

```javascript
var Module = (function(){
	return {
		pubilcMethod: function(){
			// code
		}
	}
})();
```

`Object Literal`로 반환해서, 객체 리터럴 방식으로 호출 할 수 있습니다.

```javascript
Module.publicMethod();
```

이전에 객체 리터럴 방식을 사용하지 않은 사람들을 위해 표준 객체 리터럴 방식은 다음과 같습니다.

```javascript
var myObjLiteral = {
	defaults: {name: 'Todd'},
	someMethod: function(){
		console.log(this.defaults);
	}
};

// console.log: Object { name: 'Todd'}
myObjLiteral.someMethod();
```

그러나 객체 리터럴의 이슈는 패턴이 남용된다는 것입니다. `private` 로 의도된 메서드는 객체에 일부분으로 사용자가 접근 할 수 있습니다.  
여기서 모듈을 통해 우리가 모든 `private` 한 것들을 지역적으로 정의하고 좋은 부분만 반환하도록 허용합니다. (This is where the Module comes in to save us, by allowing us to define all our “private” stuff locally and only return “the good parts”.)  

객체 리터럴 방식과 완변하게 좋은 모듈패턴 그리고 `return` 키워드 룰에 대해 더 알아보도록 하겠습니다. 일반적으로 모듈은 객체를 반환하지만, 객체가 정의되고 생성되는건 당신에게 달렸습니다.  
프로젝트와 코드의 설정과 역할에 따라 몇가지 문법 중 하나를 사용할 수 있습니다.

## Anonymous Object Literal return
쉬운 패턴중 하나로 우리가 위에 선언 했던것과 동일합니다. 객체는 이름없이 로컬에 선언되고, 객체만 반환할 뿐입니다.

```javascript
var Module = (function(){
	var privateMethod = function(){};

	return {
		publicMethodOne: function(){
			// privateMethod() 호출 할 수 있습니다.
		},
		publicMethodTwo: function(){

		},
		publicMethodThree: function(){

		}
	};
})();
```

# Locally scoped Object Literal
지역 스코프는 스코프안에 함수와 변수를 선언한 것 을 의미합니다. [Conditionizr](https://conditionizr.com/) 프로젝트에서는 파일이 100라인이 넘기 때문에 우리는 지역 스코프 네임스페이스를 사용하여, `return`문을 확인하지 않고 `public` , `private` 메서드가 무엇인지 볼 수 있는것이 좋습니다. 이런 의미로, 지역 스코프 네임스페이스가 있어서, 쉽게 `public`이 무엇인지 볼수 있습니다.

```javascript
var Module = (function(){
	// 지역 스코프 객체
	var myObject = {};

	// 'var'와 함께 선언, 'private' 임
	var privateMethod = function(){};

	myObject.someMethod = function(){
		// public 메서드
	};

	return myObject;
})();
```

위 예제를 보면 `Module` 마지막 라인에 `myObject`를 반환 하는 것을 볼 수 있습니다.
우리 전역 `Module` 객체는 이름을 가진 지역 스코프 객체를 신경쓰지 않고, 이름없이 실제 반환되는 객체만을 얻게 됩니다. 이는 더나은 코드 관리를 할 수 있게 제공합니다.

# Stacked locally scoped Object Literal
이는 앞선 예제와 거의 동일하지만, `전통적인` 단일 객체 리터럴 표기법을 사용합니다.

```javascript
var Module = (function(){
	var privateMethod = function(){};

	var myObject = {
		someMethod: function(){},
		anotherMethod: function(){}
	};

	return myObject;
})();
```

나는 두번째 접근법인 지역 스코프 객체 리터럴 방식을 선호합니다. 왜냐하면, 우리는 그것들을 사용하기 전에 다른 기능들을 선언해야만 하기 때문입니다.(당신은 `function myFunction () {}` 호이스팅을 사용하여 당신의 함수를 사용해야하며, 잘못 사용할 경우 이슈가 발생 할 수 있습니다.)  
`var myFunction = function () {};` 문법을 사용하면 위 문제에 대해 걱정할 필요가 없습니다. 우리가 함수를 사용하기 전에 모두 선언하기 때문에, 자바스크립트 인터프리터는 함수 선언을 하기보다는 우리가 정의한 순서대로 렌더링 하므로 디버깅이 쉬워집니다. 하지만 "stacking" 메서드는 종종 장황해 보이기 때문에, `public` 메서드를 사용하기 위한 지역 스코프범위내 객체 네임스페이스가 없기 때문에 이 방법을 너무 좋아하진 않습니다.

# Revealing Module Pattern
우리는 `Module`을 살펴봤으며, 모듈 스코프안에 공개 포인터를 공개하는 `revealing` 패턴으로 할 수 있는 적절한 형태가 있습니다. 다시 말해서, 모듈로 다시 전달 하는 메소드를 명확하게 보고,정의 할 수 있는 정말 멋진 코드 관리 시스템을 만들 수 있습니다.

```javascript
var Modeul = (function(){
	var privateMethod = function(){
		// private
	};

	var someMethod = function(){
		// public
	};

	var anotherMethod = function(){
		// public
	};

	return {
		someMethod : someMethod,
		anotherMethod : anotherMethod
	}
})();
```

저는 정말로 선언적인 방식인 위 문법을 좋아합니다. 더 거대한 자바스크립트 모듈의 경우 이 패턴은 더 많은 도움을 줍니다. 표준 `Module Pattern`을 사용하면 구문과 코드 구조에 따라 의존성을 제어 할 수 있습니다.

# Accessing "Private" Methods

이 포스팅의 몇몇 부분에서 "일부 메서드를 `private`로 설정하면 어떻게 호출 할 수 있습니까?" 라고 생각 할 것입니다. `public`메서드를 통해 `private` 함수를 호출 할 수 있게 해줍니다.

```javascript
var Module = (function(){
	var privateMethod = function(message){
		console.log(message);
	};

	var publicMethod= function(text){
		privateMethod(text);
	};

	return {
		publicMethod : publicMethod
	};
})();

// private 메서드로 데이터를 전달하는 예제
// private 메서드는 `console.log()` 'Hello!' 를 찍는다.
Module.publicMethod('Hello!');
```

그리고 메서드에 국한되어 접근하지 않아도 됩니다. 객체, 배열, 어느것이든 접근 할 수 있습니다.

```javascript
var Module = (function(){
	var privateArray = [];

	var publicMethod = function(somethingOfInterest){
		privateArray.push(somethingOfInterest);
	};

	return {
		publicMethod : publicMethod
	};
})();
```

# Augmenting Modules
지금까지, 멋진 모듈을 만들어, 객체를 반환 하였습니다. 그런데 만약 우리의 기존 모듈을 확장 하고, 다른 작은 모듈을 포함시키려고 한다면 어떻게 해야 할까요?
아래 코드에서 가정해봅시다.

```javascript
var Module = (function(){
	var privateMethod = function(){
		// private
	};

	var someMethod = function(){
		// public
	};

	var anotherMethod = function(){
		// public
	};

	return {
		someMethod : someMethod,
		anotherMethod : anotherMethod
	};
})();
```

자 이제 우리의 어플리케이션의 한부분이라고 생각해봅시다. 그리고 우리는 어플리케이션의 핵심에 무언가를 포함시키지 않기로 결정했습니다. 그래서 우리는 확장 모듈을 독립형 모듈로 포함시킬 수 있습니다.  
지금까지 우리 모듈의 객체는 다음과 같았습니다.

``` javascript
Object {someMethod: function, anotherMethod: function}
```

하지만 내가 확장형 모듈을 추가하고싶다면, 또다른  `public` 메서드를 다음과 같이 추가해야 합니다.

``` javascript
Object {someMethod: function, anotherMethod: function, extension: function}
```

3번째 메서드는 지금 사용할 수 있지만 어떻게 관리해야 할까요? `ModuleTwo` 라는 이름의 객체를 만들고 우리의 `Module` 네임스페이스를 전달하면 확장할 객체에 접근 할 수 있게 됩니다.

``` javascript
var ModuleTwo = (function(Module){
	// Module 에 접근
})(Module);
```

이제 그러면 이 모듈안에 `private`한 스코핑/기능의 모든 장점을 가지고있는 또다른 메서드를 만들어 `extension` 메서드에 할당하여 반환하면 됩니다.  
저의 의사 코드는 다음과 같습니다.

``` javascript
var ModuleTwo = (function(Module){
	Module.extension = function(){
		// 다른 메서드
	};

	return Module;
})(Module || {});
```

`Module`이 `ModuleTwo`로 전달되면 확장메서드는 추가되고 다시 반화되어집니다. 우리의 객체는 thrown되어 지지만, 자바스크립트의 유연성은 다음과 같습니다. :D  
그런다음 Chrome's Dev Tools 통해 초기 모듈에 3번째 속성이 있음을 볼 수 있습니다.

```javascript
// Object { someMethod: function, anotherMethod: function, extension: function}
console.log(Module);
```

또다른 힌트로 두번째 `ModuleTwo`에 `Module || {}` 을 전달 하는 것을 알 수 있습니다. 이 모듈은 아직 정의되지 않았습니다. (오류는 발생하지 않을 것입니다.) 이런 것들이 하는 일은 새로운 객체를 인스턴스화 하고, 우리의 확장 메서드에 바인딩하여 반환 하는 것입니다.

# Private Naming Conventions

저는 개인적으로 `Revealing Module Pattern`을 좋아합니다. 이를테면 저는 시각적으로 모두 같은것으로 선언 된 코드 주변에 많은 함수들을 산재시켜놓고, 제가 주변을 스캐닝 할때에도 같아 보이게 합니다. 때때로 저는 지역 스코프 객체를 생성하지만, 가끔은 그렇지 않습니다. 제가 어떻게 `private` 변수/메서드를 구분할 수 있겠습니까? 아마도 웹주변에 산재된것을 보셨을겁니다. 이제는 왜 그렇게 했는지 알 것입니다.

```javascript
var Module = (function(){
	var _privateMethod = function(){
		// private stuff
	}

	var publicMethod = function(){
		_privateMethod();
	}

	return {
		publicMethod: publicMethod
	}
})();
```
