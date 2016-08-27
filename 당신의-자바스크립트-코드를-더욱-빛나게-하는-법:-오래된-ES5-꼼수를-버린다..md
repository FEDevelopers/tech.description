> 이 문서는 https://rainsoft.io/make-your-javascript-code-shide-knockout-old-es5-hack/ 를 번역한 내용입니다.

ECMAScript 2015 이전의 자바스크립트 언어는 다루기 까다로웠다.
간단한 ES5 스크립트는 괜찮았다. 하지만 어플리케이션의 복잡도가 커지게 되면서, 언어 구조의 부족함으로 인해 코드가 불편해 진다는 것을 느끼기 시작하게 된다.

당신은 어플리케이션 요구사항에 맞게 개발을 해야하기 때문에, 당신은 ES5 문법으로 개발하게 되면 골칫거리가 될 코드를 그냥 넘기지 못한다. 그에 대한 해결책으로 합리적이진 않지만, 꼼수를 사용하게 된다.

꼼수와 차선책으로 개발된 여러 줄의 코드는 문제를 발생하게 된다. 더불어 쉽게 읽을 수 없게 되고, 유지보수를 하기에도 어렵게 된다.

그리고 일반적인 말로

> 자바스크립트 꼼수를 적용하는 것은 카드로 집을 짓는 것과 같다.


![카드집](https://rainsoft.io/content/images/2016/08/House-of-cards-014-1.jpg)


차선 책으로는 Underscore 혹은 lodash와 같은 유틸리티 라이브러리를 사용하는 것이다. 라이브러리는 어려운 것을 쉽게 해결할 수 있는 다양한 함수를 제공한다.
훨씬 나은 방법이지만, 이것 역시 여전히 차선책이다. 당신은 추가 의존성을 유지해야되고, 필요한 해결책을 항상 찾지 않게 될지도 모른다.

ECMAScript 2015, 2016, 그리고 곧 나올 2017은 이런 현상을 개선하기 위해 효과적인 것들을 만들고 있다. 이미 꼼수로 개발된 대부분의 것들을 안정적인 것들로 쉽게 대체될 수 있다. 또한 당신의 프로젝트의 프로세스를 개선할 수 있다.

어떻게 하면 자바스크립트 코드를 바위처럼 단단히, 빛나게 개발할 수 있을까? 당신의 코드에서 꼼수를 제거하고, 해결책을 적용하고 있는 이 가이드를 따라라. 

## 1. Verify the element existence in an array

> Searching for an element using array.indexOf(element) !== -1

Since every application deals with arrays, it's a common task to verify if an element exists within a collection.

- In ES5 the usual solution was to use Array.prototype.indexOf(element, [fromIndex]) method and verify the returned value to be different than -1, which indicates the missing element.

Such verification is using a side effect of the .indexOf() method, because it normally returns the index of an element in array, and simply -1 when its not found.

Let's see a sample:

```
var months = ['January', 'March', 'July'];  
months.indexOf('March') !== -1;  // => true  
months.indexOf('August') !== -1; // => false  
```
To verify if an elements exists in months array, months.indexOf() should return a value different than -1. 
For an existing element months.indexOf('March') the method returns the index 1, so the comparison 1 !== -1 is true. 'March' element exists. 
Calling months.indexOf('August') is evaluated to -1, which indicates that the element is missing: -1 !== -1 is false. 'August' element does not exist.

Even worse you may encounter other ways of comparison:

- months.indexOf('March') != -1
- months.indexOf('Match') < 0
- ~months.indexOf('March')
- and so on...

Such way to search elements is not convenient and smells like a hack.

- To pass this problem, ECMAScript 2016 introduces a new method Array.prototype.includes(element, [fromIndex]). It returns a boolean that indicates the existence of element in array.