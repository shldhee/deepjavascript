# 자바스크립트의 변수 #1

## 글로벌 변수 최소화하기

* 클로저를 이용하는 방법
* 모듈/네임스페이스 패턴을 이용하는 방법

*클로저로 글로벌 변수 사용을 피하는 예*

``` js
(function () {
	var localVariable = "I'm not global";
}());
```

클로저를 사용함으로써 간단하게 글로벌 변수가 아닌 로컬 변수를 사용

*모듈로 글로벌 변수 사용을 피하는 예*

``` js
var myModule = (function () {
	var localVariable = "I'm an inside local variable";
	return {
		show: function () {
			alert(localVariable);
		}
	};
}());
myModule.show();
```

* myModule은 글로벌 변수
* myModule 이외의 다른 변수들은 모두 즉시 호출 함수 안에서 사용하여 글로벌 변수가 아니다.
* myModule.show(); 를 통해 간접적으로 내부 함수의 로컬 변수들에 접근하고 사용 할 수 있다.

*즉시 호출 함수로 모듈을 생성하는 예*

``` js
(function (window){
		var localVariable = "I'm an isinde local variable";

		window.myModule = {
			show: function () {
				alert(localVariable);
			}
		};
}(window));
myModule.show();
```

즉시 호출 함수 안에서 직접 window 객체에다가 글로벌 변수로 사용할 변수 하나를 정의하기도 한다.
***글로벌 변수를 최소화함으로써 서로 다른 자바스크립트 소스 간의 충돌을 최소화하고자 하는 것이 바로 모듈/네임스페이스 패턴***

글로벌 변수의 사용을 최소화하기 위한 방법을 선택할 때는 몇가지 기준을 따라야 한다.

* DOM에 자바스크립트 코드를 넣고 변수에 접근해야 한다 - 모듈 방식
* 다른 개발자와 상호작용하는 소스를 만든다 - 모듈 방식
* 자바스크립트 라이브러리를 만들고 싶다 - 모듈 방식
* 외부에서 접근할 일이 없다. - 클로저 방식
* 잘 모르겠다 - 클로저 방식

대부분은 클로저를 사용해 글로벌 변수 없이 전체를 로컬 변수로 만들어 사용하는 것이 좋다.

*HTML에 자바스크립트가 들어가는 일반적인 예*

``` js
<body>
	<form id="formWrite" onsubmit="return validate()">
		// content
	</form>
	<script>
		function validate() {
			// content
		}
	</script>
</body>
```

* validate라는 일반 명사 함수명으로 사용함으로써, 다른 소스와 충동날 수 있는 가능성이 많다.
* DOM에서 onsubmit은 직접 자바스크립트 함수를 호출하므로 이렇게 글로벌 함수를 정의할 수 밖에 없다고 판단하기도 한다.

그러나 처음부터 DOM에 자바스크립트 코드를 넣지 않으면 글로벌 함수를 사용하지 않고도 같은 기능 구현이 가능하다.

*HTML과 자바스크립트를 구분하여 구현한 예*

``` js
<body>
	<form id="formWrite">
		// content
	</form>
	<script>
		(function() {
			var formWrite = document.getElementById("formWrite");
			formWrite.onsubmit = validate;

			function validate() {
				//content
			}
		}());
	</script>
</body>
```

* 소스의 양은 늘어났지만 DOM과 자바스크립트가 분리되어 있다.
* MVC관점에서 보면 DOM은 뷰(view), 뷰를 처리하는 컨트롤러(controller)는 자바스크립트
* HTML,자바스크립트를 분리해놔서 DOM 이벤트 소스 분석시 자바스크립트부분만 확인하면 된다.
* 소스 관리와 유지 보수시 용이하다.

클로저 변수를 사용할때 기준 중 하나인 "보안이 중요한 데이터" 이다.?
데이터를 자바스크립트로 가져오면 이미 브라우저 내부에 저장되므로 어떠한 방법을 써서라도 내용 확인이 가능하다.

보안이 중요한 데이터는 처음부터 브라우저의 자바스크립트로 가져오지 않는것이 정답이다.

## 변수 사용 방법과 성능

*상위 스코프 체인의 변수를 사용하는 일반적인 예*
