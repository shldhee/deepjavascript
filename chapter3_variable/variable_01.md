# 자바스크립트의 변수 #1

## 자바스크립트의 기본형과 typeof

자바스크립트에서 객체가 아닌 기본적인 키워드와 문자로 활용되는 기본형은 다음과 같다.

* number
* string
* boolean
* undefined
* null
* symbol

자바스크립트는 이와 같은 **기본형 변수들**과 **Object를 기초로 하는 객체들**로 구성된다.

특정 변수가 어떤 형태인지 확인하는 연산자로 typeof가 있다. 오른쪽에 입력값 하나를 받는 단항 연산자

`typeof myVariable`

typeof 연산자를 사용하면 문자열을 반환하는데 그 목록은 다음과 같다.

* undefined
* boolean
* number
* string
* object
* function
* symbol

*변수형에 따른 처리 분기*

``` js
if (typeof myVariable === "function") { // myVariable function일때
    alert("Variable is functin");
    myVariable.call(this);
} else if (typeof myVariable === "String") {    // myVariable String일때
    alert("Variable is string: " + myVariable);
}

typeof 3; // "number"
typeof "str"; // "string"
typeof {}; // "object"
typeof []; // "object"
typeof function () {}; // "function"
typeof null; // "object"
```

**null 값이 object이다**
이는 자바스크립트가 개발되었을 당시 다른 언어들과 마찬가지로 **`null`이면 0값을 가지는 객체** 로 취급하여 `"object"` 반환하도록 개발했기 때문이다.

typeof null은 "object"를 반환하므로 만약 typeof 결과를 대상으로 별도의 처리를 하고 싶다면 null을 확인하는 조건도 같이 넣어주는것이 좋다.
[typeof null의 역사](https://github.com/FEDevelopers/tech.description/wiki/%E2%80%9Ctypeof-null%E2%80%9D%EC%9D%98-%EC%97%AD%EC%82%AC)

``` js
var myVariable = false;

if (myVariable !== null && typeof myVariable === "object") { // null이 아니고 객체일때
	alert("first#1");
	// do something with myVariable
} else if (!!myVariable && typeof myVariable === "object") { // true이고 객체일때 왜 !! 쓰는가?
	alert("second#2");
	// do something with myVariable
}
// 둘중 아무거나 사용해도 된다.
```

* typeof myVariable을 사용하고자 할 때는 if처럼 직접 null과 비교하는 방법도 가능
* 미리 undefined나 false같은 조금 더 넓은 범위에서 검증하고 싶다면 else if 처럼 !!myVariable 사용

---

## new String("")과 "", 그리고 String("")의 차이

**기본형의 형태를 알아보는 함수 `typeof` 연산자**
**객체에 대하여 어떠한 객체인지 확인하는 `instanceof` 연산자**
 * `instacneof`는 `typeof`와 달리 이항 연산자로 인자를 2개 받는다.
 * 왼쪽에 받는 인자가 오른쪽에 받는 인자의 인스턴스인지 확인(오른쪽이 더 넓은 놈)
 * 그 결과로 true , false 반환
 * 즉 변수가 해당 클래스의 인스턴스인지 확인하는 키워드

```js
function Person(name, blog) {
	this.name = name;
	this.blog = blog;
}

var unikys = new Person("unikys", "http://unikys.tistory.com");

console.log(unikys instanceof Person); // true
console.log(unikys instanceof Object); // true
console.log(typeof unikys); // "object"
```
instanceof의 연산 결과로 unikys 객체가 생성자인 Person을 통해서 생성되었고, Person은 Object를 확장하고 있다는 것을 확인할 수 있다.

*instanceof와 typeof의 동작은 유사한듯 하지만 차이가 있다.*
**instanceof 연산자는 기본형에 대해서는 동작하지 않는다.**

``` js
console.log(true instanceof Boolean); // false
console.log(true instanceof Object); // false

console.log([0,1] instanceof Array); // true
console.log({name: "unikys"} instanceof Object); // true

var color1 = new String("red");
var color2 = "red";

console.log(color1 == color2); // true
console.log(color1 instanceof String); // true
console.log(color2 instanceof String); // false ?!
console.log(color2 instanceof Object); // false
```

true와 false와 같은 값은 기본형이라서 Boolean 객체의 instanceof가 false로 나온다.
하지만 []처럼 배열을 생성하는 표현식은 내부적으로 new Array()와 같은 동작을 취하게 되므로 instanceof Array가 true이다.

String 관련 객체와 기본형을 비교
new String()으로 생성한 문자열과 기본형인 큰따옴표로 생성한 변수를 비교(==)하면 true
그런데 new String()으로 생성한 문자열은 instanceof String이 true지만, 큰 따옴표로 생성한 문자열은 false이다.

***문자열이 서로 같은지 == 연산자로 비교하면 true로 서로 같은 값이라는 결과가 나오지만, 내부적으로 보면 서로 다르다.***

### 기본형 문자열과 문자열 객체 비교

``` js
var color1 = new String("red");
var color2 = "red";

console.log(color1 === color2); // false
console.log(color1.constructor === String); // true
console.log(color2.constructor === String); // true
```

* `color1`과 `color2`를 == 연산자로 비교했을때는 true이지만 ===연산자로 비교시에는 false이다.
* ==비교 연산자는 두 피연산자가 다른 형태일 때 대부분 비교를 위해 형변환이 일어나서 같은 값으로 판단
* ===비교 연산자는 형변환이 일어나지 않은 엄격한 비교
* 따라서 color1은 String의 인스턴스이고, color2는 기본형이므로 ===비교연산자 사용시에는 false이다.

*그런데 이 두 변수의 constructor속성을 살펴보면 2개가 똑같이 String*
color2는 String이라는 생성자를 가지고 있다고 하는데, 앞에 결과를 보면 이해하기 힘들다.
color2의 생성자가 String인 이유는 color2.constructor를 연살할 때 내부적으로 형변환이 일어난 다음에 constructor접근하기 떄문이다.

(p65~66 참조)

ToObject 함수의 인자로 String 기본형을 넘기면 새로운 String객체를 생성하여 반환한다.
***string 기본형인 color2의 속성에 접근하려고 하면 자바스크립트 내부적으로 String 객체로 변환*** wrapper객체????

**문자열 생성 방법들**

``` js
var color1 = new String("red");
var color2 = "red";
var color3 = String("red");

console.log(color1.toUpperCase()); // RED ;
console.log(color2.toUpperCase()); // RED ;
console.log(color3.toUpperCase()); // RED ;

console.log(color1 instanceof String); // true
console.log(color2 instanceof String); // false
console.log(color3 instanceof String); // false

console.log(typeof color1); // object
console.log(typeof color2); // string
console.log(typeof color3); // string

console.log(color3 === color2); // true
```

* color1은 new 키워드 사용, color3은 new가 없다
* 객체의 함수(`toUpperCse`)를 문제 없이 사용
* 하지만 instanceof와 typeof의 결과값이 다르다.
* color2와 color3이 일치하는 것처럼 보인다.
* ToString의 정의를 살펴보면 입력되는 인자 값이 기본형 문자열일 때는 변환없이 입력되는 인자 값을 그대로 반환

***표준에 의하면 입력 인자가 String이면 인자를 그대로 반환하게 되어 있다. 따라서 String("red")이라고 설정했던 변수는 인자인 "red"가 그대로 반환되어 문자열 기본형인 "red와 같다"***

### 추가 속성 선언 여부

문자열을 생성하는 방법 중 다른 점이 또 있다면 바로 기본형은 추가 속성을 선언할 수 없다

*String 객체와 기본형 문자열의 속성 설정 비교*

``` js
var constructorString = new String("unikys");
constructorString.name = "LEE";
console.log(constructorString.name === "LEE");  // true

var primitiveString = "unikys";
primitiveString.name = "Hee"
console.log(primitiveString.name === "Hee"); // false
```

* 기본형에 추가 속성을 추가 할 수 없다.
* 하지만 prototype과 연계하면 자바스크립트만의 특징이 나타난다.
* String.prototype에 추가한 함수들을 기본형 문자열에서도 그대로 사용이 가능하다.
