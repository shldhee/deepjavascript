# 자바스크립트의 변수

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
이는 자바스크립트가 개발되었을 당시 다른 언어들과 마찬가지로 `null`이면 0값을 가지는 개체로 취급하여 `"object"` 반환하도록 개발했기 때문이다.

typeof null은 "object"를 반환하므로 만약 typeof 결과를 대상으로 별도의 처리를 하고 싶다면 null을 확인하는 조건도 같이 넣어주는것이 좋다.
