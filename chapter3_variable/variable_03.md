# 자바스크립트의 변수 #3

## window 객체

글로벌 객체(Global object)는 실행 전 단계에 객체가 생성되어, 이 객체에는 덮어쓸 수 있으나 루프로 돌 수 없는 기본 내장된 속성들을 가지고 있다.

* 생성자가 없어 new를 생성할 수 없고
* 함수로 호출할수 없고
* 프로토타입도 없다.

**HTML DOM환경에서 이 객체는 "window" 속성을 가지고 있으며, 이것이 "Global object"그 자체를 나타낸다고 정의한다.**

window객체는 글로벌 변수로 선언한 모든 변수를 속성으로 갖는다.

*window 객체를 통한 글로벌 변수 접근*

``` js
var myGlobal = "am i in window?";
myGlobal === window.myGlobal; // true
```

*글로벌 변수 접근 방법*

``` js
var myGlobal = "am i in window?";
var myVariableName = "myGlobal";
console.log("1. " + myGlobal);
console.log("2. " + window.myGlobal);
console.log("3. " + window["myGlobal"]);
console.log("4. " + window[myVariableName]);
// "am i in window?"
```

window 객체를 이용해서 글로벌 변수를 다른 변수의 속성에 접근

*window 객체를 이용한 처리 방안*

``` js
var i;
for ( i = 1; i < 5; i++ ) {
	window["button" + i] = document.getElementById( "button" + i );
	window["button" + i].dosomething = "Do something same witout eval, clicked button" i;
}
```

window 객체를 이용하면 변수뿐만 아니라 함수도 호출할 수 있다.

*window 객체를 통한 글로벌 함수 호출*

``` js
function myGlobalFunction() {
	alert("Global function invoked");
}

window["myGlobalFunction"]();
window["myGlobalFunction"].call();
```

위의 예에서는 글로벌 변수로 선언한 변수들이 window객체의 속성으로 들어가 있는것을 확인했다.
반대로 window 객체의 속성으로 변수나 함수를 선언하면, 이들이 글로벌 변수나 함수가 되므로 양방향으로 용긴하게 사용할 수 있다.

*window객체를 통한 글로벌 함수 정의*

``` js
(function() {
	window.myGlobalFunction = function() {
		alert("Global function");
	};
});

myGlobalFunction();
myGlobalFunction.call();
```

*NaN, undefined 둘 다 window 객체의 속성*

## 글로벌 변수 선언 방법과 차이

글로벌 변수 선언 방법

* 글로벌 스코프에서 var 키워드를 써서 변수 선언
* 상위 스코프에서 같은 변수명으로 선언되지 않고 var 없이 바로 변수 사용하는 경우
* window 객체가 재선언되지 않은 스코프에서 window.global과 같이 속성 추가
* window 객체가 재선언되지 않은 스코프에서 window["global"]과 같이 속성 추가

window라는 변수명은 사용하지 말아야 한다.

### 글로벌 변수 선언 방법의 차이

다른 점은 변수가 선언되는 시기이다.

* var 키워드 이용시 호이스팅?이 적용된다.(최초 소스의 파싱 단계에서 미리 글로벌 변수로 정의되어 undefined로 값 설정)
* var 키워드 없이 바로 사용하면 구문이 실행되기 전에는 변수가 window의 속성으로 정의되어 있지 않아 에러 발생(해당 구문이 실행되는 순간에 글로벌 변수를 window객체의 속성으로 추가)

var키워드로 변수를 정의하면 해당 스코프의 실행 환경에 진입하면서 미리 모든 변수를 찾아서 정의하고 값을 `undefined`로 초기화하는것이다.

함수의 소스코드인 code 전체를 기준으로 변수를 정의하기 떄문
*var 키워드로 정의하는 변수의 위치는 의미가 없고, 변수의 초기화 위치만 의미가 있다.*

*if 구문 안에 변수를 정의하는 예*

``` js
function optimizedFunc(flag) {
	if (flag) {
		var lotsOfVariables1, lotsOfVariables2, lotsOfVariables3;
		console.log("1: " + lessVariables);
	} else {
		var lessVariables;
		console.log("2: " + lotsOfVariables1);
	}
	optimizedFunc(true);
	optimizedFunc(false);
}
```

if문은 스코프를 생성하지 않는다.
따라서 optimizedFunc() 함수 안으로 들어갔을 때는 if문, else문의 변수들이 모두 정의된다.
if문, else문 분기 변수들의 수는 메모리의 효율과 상관없다.

JSLint 추천방식

``` js
function optimizedFunc(flag) {
	if (flag) {
		var lotsOfVariables1, lotsOfVariables2, lotsOfVariables3, lessVariables;;
		console.log("1: " + lessVariables);
	} else {
		console.log("2: " + lotsOfVariables1);
	}
	optimizedFunc(true);
	optimizedFunc(false);
}
```

## var 키워드의 효율적인 사용

*여러 개의 var구문으로 변수 정의*

``` js
(function(){
	var subjects = ["1st", "2nd", "3rd"];
	for (var i = 0;  i < subjects.length; i++) {
		var el = document.getElementById("subject" + i);
	}

	var xhr = new XMLHttpRequest();
}());
```

*위 소스에서 변수들을 var 구문 하나로 선언*

``` js
(function() {
	var subjects = ["1st", "2nd", "3rd"], el, i, xhr;
	for ( i = 0;  i < subjects.length; i++) {
		el = document.getElementById("subject" + i);
	}

	xhr = new XMLHttpRequest();
} ());
```

이러한 변수 선언 방식을 권장하는 이유
* 초기화 실수의 최소화
* 해당 스코프에서 사용하고 있는 변수에 대한 관리 용이
* 미니피케이션(minification) 최적화


**초기화 실수의 최소화**
	상단에 모든 변수를 나열하고 필요한 변수들에 대하여 초기화 체크를 쉽게 할 수 있다.
	기본값인 undefined로 두는 것이 아니라 null이나 0등과 같은 기본값으로 설정

*초기화 여부와 변수 선언 여부를 확인하는 예*

``` js
console.log("선언을 안한 경우 검사: " + (typeof notExists === "undefined")); // true
var notIntialized;
console.log("초기화를 안한 경우 검사: " + (typeof notIntialized === "undefined")); // false
```

대부분 개발자들이 `undefined`로 변수 기본값으로 두는 것을 선호하지 않는다. null 등과 같은 값으로 설정하는 것을 선호한다.
변수를 null로 초기화하면 typeof가 아닌 `notIntialized === null`로 검증이 가능하다.

var 구문이 실행된 이후에 초기화되므로 변수가 활용되는 스코프가 실행되지마자 초기화되는 이점도 있다.(무슨 이점?) 따라서 변수들을 하나의 var로 묶어서 선언하라고 권장하는 것이다.

**해당 스코프에서 사용하고 있는 변수에 대한 관리 용이**

*var 키워드 생략으로 인한 오동작 예*

``` js
(function() {
	var xhr, i;
	for (i = 0; i < 10; i++) {
		xhr = new XMLHttpRequest();
		xhr.open("GET", "http://unikys.tistory.com/" + i);
		xhr.onload = function() {
			var json = JSON.parse(xhr.responseText);
			for ( i = 0; i < 5; i++) {
				alert(i + " = " + json[i]);
			}
		};
	}
}()); // 코드 실행이 안됨
```

* 외부 i = 0; -> 내부 i = 0에서 5까지 감 -> 외부로 갔는데 i = 0 ->내부 0에서 5까지 감??

``` js
(function() {
	var xhr, i;
	for (i = 0; i < 10; i++) {
			for ( i = 0; i < 5; i++) {
				alert(i);
			}
		};
	}());
```

이건 맞는것 같음

p97~p98 좀 더 보기

상단에 나열된 변수명과 초깃값들만 봐도 해당 스코프에서 어떠한 작업을 하고자 하는지 판단할 수 있다.

**미니피케이션(minification)**

미니피케이션은 소스를 읽기 어렵게 만들어서 크기를 줄이고 암호화한다.
var를 키워드를 하나로 묶으면 1번 확인되지만 var키워드를 따로 했으면 var 키워드가 더 많이 나왔다.
