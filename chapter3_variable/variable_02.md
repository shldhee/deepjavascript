# 자바스크립트의 변수 #2

## 글로벌 변수

*글로벌 변수 사용 예*

``` js
<script type="text/javascript">
	var element = document.getElementById("div"),
		insertHTML = "<b>Hello world</b>";
	element.innerHTML = insertHTML;
</script>
```

`<script>` 태그 안에서 다른 처리 없이 바로 `var`키워드로 변수를 선언하여 사용한다면, 그것이 바로 글로벌 변수를 사용하고 있는 대표적인 예

*글로벌 변수를 사용하여 이벤트 핸들러를 할당하는 예*

``` js
var element = document.getElementById("div"),
	button = document.getElementById("button"),
	insertHTML = "<b>Hello World</b>";
button.onclick = function() {
	element.innerHTML = insertHTML;
};
```

*글로벌 변수를 사용하여 AJAX를 활용하는 예*

``` js
// xhr1.js
var element = document.getElementById("div"),
	button = document.getElementById("button"),
	xhr = new XMLHttpRequest();
button.onclick = function() {
	xhr.open("GET", "http://unikys.tistory.com/");
	xhr.onreadystatechange = function () {
		if (xhr.readyState === 4 && xhr.status === 200) {
			element.innerHTML = xhr.responseText;
		}
	};
	xhr.send();
};

// xhr2.js
(function() {
	xhr = new XMLHttpRequest();
	xhr.open("GET", "http:/blahblah");
	xhr.onreadystatechange = function() {
		if(xhr.readyState === 4 && xhr.status === 200) {
			console.log(xhr.responseText);
		}
	};
}());
```

### 글로벌 변수를 자제해야 하는 이유

'웹'이 다른 애플리케이션들과 다른점
* 소스와 데이터의 공개성과 다양한 라이브러리 등 외부소스 활용
* 비동기 로직과 이벤트 처리 기반
* PC와 같이 좋은 성능에서부터 모바일의 안 좋은 성능까지 다양한 브라우징 환경

1. 웹이라는 특성상 브라우저에서 돌아가는 모든 스소와 데이터는 클라이언트 측에서 돌아가므로 해킹, 보안에 취약할 수 있다.
2. 디버깅 툴로 소시는 기본이고 자바스크립트의 변수값도 쉽게 확인이 가능하다
3. 마지막으로 가장 중요한 이유는 다른 라이브러리를 사용하거나, 큰 프로젝트로 소스를 나누어서 관리할 때 충돌이 일어난다.

글로벌 변수 xhr이 덮어 쒸워지거나, 도중에 속성값들이 변경되는 등 잠재적인 충돌 위험성이 있다.

**소스 간의 충돌은 모듈화를 하거나 클로저를 통해서 대부분 예방할 수 있다. 클로저를 사용하면 일반적은 방법으로는 변숫값에 접근하기 힘들다.**

*클로저를 활용하여 로컬 변수를 활용하는 예*

``` js
(function() {
	var appendDiv = document.getElementById("appendDiv");
	document.getElementById("wrapper").addEventListener("click", append);

	function append(e) {
		var target = e.target || e.srcElement || event.srcElement;
		var callbackFunction = callback[target.getAttribute("data-cb")];
		appendDiv.appendChild(callbackFunction());
	};
	var callback = {
		"1":(function () {
			var div = document.createElement("div");
			div.innerHTML = "Adding new div";
			return function () {
				return div.cloneNode(true);
			}
		}()),
		"2":(function() {
			var img = document.createElement("img");
			img.src = "http://cfile9.uf.tistory.com/image/011F554E50FD140F2B27CA";
			return function () {
				return img.cloneNode(true);
			}
		}()),
		"delete": function() {
			appendDiv.innerHTML = "";
			return document.createTextNode("Cleared");
		}
	}
}());
```

* 즉시 호출 함수를 사용하여 모든 변수를 감싸고 있다.
* 이벤트 호출 콜백에 사용되는 함수와 관련된 변수들을 클로저 안에 선언함으로써 다른 소스와 충돌을 피하고 있다.
* div, img와 같은 간단한 변수명은 쉽게 다른 소스와 충돌할 수 있으므로 이렇게 보호하는 것이 좋다.

#### 웹 환경 두 번째 특징 : 비동기 로직과 이벤트 기반의 처리

* 비동기 로직 처리는 AJAX를 통해서 많이 경험해 봤을 것이다.
* 이벤트 기반 처리는 사용자 입력 또는 서버와의 통신으로 인하여 HTML에서 이벤트를 수신하여 자바스크립트를 호출하는 것을 의미
* 이러한 환경에서는 서로 충돌이 쉽게 일어 난다.
* 특히 이 둘을 조합하여 사용자 이벤트로 비동기 로직을 처리하게 되면 글로벌 변수 때문에 이유도 모르고 예상치 못한 동작을 맞이하게 될수도 있다.

``` js
var xhr = new XMLHtttpRequest();
document.getElementById("buttonCheckId").addEventListener("click", function () {
	var id = document.getElementById("inputId").value;
	xhr.open("GET", "http://unikys.tistory.com/api/checkId?id=" + id);
	xhr.onreadystatechange = function () {
		if (xhr.readyState === 4 & xhr.status === 200) {
			if (xhr.responseText === "1") {
				alert("ID exists!");
			}
		}
	};
});
```

* xhr를 최초에 한 번 정의한 후 사용자가 버튼을 여러번 클릭해도 같은 xhr변수를 재사용하여 자원을 아낀다.
* 하지만 버튼을 두번 연속 클릭, 응답전에 입력값을 바꿔서 다시 버튼을 클릭하면 사용자는 예상치 못한 결과값을 받게 될 수도 있다.

#### 웹 환경 세 번째 특징 : PC와 모바일의 다양한 브라우징 환경

모바일과 PC 브라우저의 차이점은 크게 세 가지로 나눌 수 있다.

1. 화면 표시 장치가 다르다
2. 모바일 기기의 브라우저가 특징적으로 나뉘어 있다.
3. 컴퓨팅 자원의 차이가 크다. 이 부분은 글로벌 변수 사용 문제와 연결된다.
	즉 현재 웹페이지가 떠 있는 한 모든 글로벌 변수가 메모리에 상주하므로 컴퓨팅 자원이 소모된다.

따라서 모바일 브라우저의 메모리 사용을 원할하게 해주기 위하여 잠시 사용할 변수는 글로벌 변수가 아닌 로컬 변수로 현재의 스코프에 올려서 사용하고 사용 안 할 때는 해제한다.(AJAX도 마찬가지다.)

위와 같은 특징 때문에 글로벌 변수의 사용을 자제하는 것이 좋다.

## 글로벌 변수 정의

글로벌 변수는 말 그대로 선언하면 어디서든지 접근할 수 있는 변수

``` js
<script>
var global = "This is global variable";
</script>
```

이렇게 `<script>`태그 안에 별도의 처리 없이 바로 var를 쓰면 글로벌 변수가 된다.

*일반적인 변수명의 글로벌 변수로 인한 오동작의 예*

``` js
function addOneToTen() {
	sum = 0;
	for ( i = 1; i < 11; i++ ) {
		sum = sum + i;
	}
	return sum;
}

sum = 0;
for ( i = 0; i < 10; i++ ) {
	sum = sum + addOneToTen(); // 여기서 addOneToTen 함수가 실행되고 i값이 11로 반환되어 for문 종료
}
console.log(sum); // 55
```

`addOneToTen()` 함수의 `sum`,`i`는 글로벌 변수.
즉, `addOneToTen()`함수 안에 있는 `i`와 `sum`은 글로벌 영역에 있는 변수들을 참조
따라서 `addOneToTen()`함수가 호출 된후 `i`의 값은 11이 된다.

**`var`키워드를 쓰지 않으면 '현재보다 상위의 스코프를 탐색하면서 변수가 있는지 검사'하는 단계를 반복해서 거치게 된다. 이러한 검사가 글로벌 영역까지 가서 변수가 존재 하지 않으면 변수를 글로벌 영역에 추가 한다.**

*var 키워드를 사용하지 않는 변수 접근의 예*

``` js
var get = "global";
(function() {
	var get = "immdediate function";
	insideFunction();
	console.log("2. immdediate function: " + get); //3. get이 2에서 할당되어 "will I be global"

	function insideFunction() {
		console.log("1. Inside function: " + get); // 1/ get === "immdediate function"
		get = "will I be global?" // 2. get 할당
	}
}());
console.log("3. Global: " + get); // 4. get === global
```
