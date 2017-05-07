# 클로저 #2

클로저가 생기는 다른 상황

*setInterval() 함수를 통한 클로저 발생*

``` js
<button id="btnToggle">Toggle Pending</button>
<div id="divPending">Pending</div>
<script type="text/javascript">
    (function () {
        var pendingInterval = false,
            div = document.getElementById("divPending");
            btn = document.getElementById("btnToggle");

        function startPending() {
            if (div.innerHTML.length > 13) {
                div.innerHTML = "Pending";
            }
            console.log(div.innerHTML.length);
            div.innerHTML += ".";
        };
        btn.addEventListener("click", function () {
            if (!pendingInterval) {
                pendingInterval = setInterval(startPending, 500);
            } else {
                clearInterval(pendingInterval);
                pendingInterval = false;
            }
        })
    }());
</script>
```

* var 변수들은 내부 함수에서만 접근할 수 있도록 private 변수로 선언
* startPending() 함수 안에 있을 법한 divPending을 가져오는 부분을 상위 스코프에 미리 가져다 놓는다.(`var div`)
* 매번 `<div>`를 getElementById로 탐색해서 가져오지 않도록 했다.
* 글로벌 변수가 아닌 클로저를 이용해서 내부에 로컬 변수에 사용
* 클로저가 발생한 이유
    1. 상위 스코프에서 div 변수를 참조하고 있는 부분(`var div`)
    2. `setInterval()` 함수에서 첫 번째 인자로 startPending 함수를 사용하는 부분
        함수 안에 함수가 있어서 내부에 있는 함수가 반환되지 않고 이벤트 콜백함수로 호출될 때도 클로저가 발생한다.

## 클로저의 실제 활용 예

클로저를 가장 많이 사용하는 것

* 자바스크립트 라이브러리나 모듈에서 private으로 나의 변수를 보호하고 싶을 때
* static 변수를 이용하고 싶을때
* 콜백 함수에 추가적인 값들을 넘겨줘서 활용하거나 처음에 초기화했던 값을 계속 유지하고 싶을때

``` js
<div id="wrapper">
    <button data-cb="1">Add div</button>
    <button data-cb="2">Add img</button>
    <button data-cb="delete">Clear</button>
    Adding below...<br />
    <div id="appendDiv"></div>
</div>
<script type="text/javascript">
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
</script>
```

클로저를 활용한 곳을 보면 크게 두 곳으로 불 수 있다.

1. `var appendDiv` 를 미리 가져와서 한 번의 초기화만으로 이후에 함수들이 계속 접근할 수 있게 하는 부분
1. 콜백 함수들이 추가할 HTML 엘리먼트를 만들어주는 `var div`, `var img`

* 위 소스에서 어떠한 버튼이 눌리는지는 DOM에 입력된 "data-cb" 속성을 이용해 콜백함수를 호출할지 결정
* 콜백 함수의 상위 클로저에서 자바스크립트로 노드를 만들어뒀다가 콜백 함수가 호출되면 해당 노드를 복사하여 appendDIV에 지속해서 추가하는 방식

최초에 초기화된 고정적인 값이나 변수를 자주 이용하는 경우, 클로저를 통해서 최초에 초기화해두고 콜백 함수에서 지속해서 참조하는 것이 퍼포먼스상 유리하게 작용 할 수 있고, 객체의 속성이 자유롭고 쉬운 자바스크립트에서는 이러한 디자인 패턴이 아주 효율적이다.

***클로저를 가장 많이 활용할수 있는 부분은 반복적으로 같은 작업을 할 때, 같은 초기화 작업이 지속적으로 필요할 때, 콜백 함수에 동적인 데이터를 넘겨주고 싶을 때 클로저를 사용하자!"***

앞에 예제는 클로저뿐만 아니라 여러 가지로 참고하고 활용할 수 있는 예들이 포함되어 있다.

* 클로저로 한 번만 DOM을 탐색하고 appendDiv를 계속 보관하여 활용하기
* `<div>`, `<img>` 등 노드/템플릿을 자바스크립트로 만들어두고 필요할 때마다 복제 생성하여 활용하기
* 하나의 `<div>`에만 이벤트 핸들러를 설정하여 관리할 수 있는 이벤트 델리게이션 패턴
* 이벤트가 ㅂ라생한 대상 엘리먼트를 크로스 브라우저에서 가져오기
* callback 변수를 활용하여 대상에 따라 동적으로 콜백 함수 호출하기
* HTML5의 스펙에 맞는 사용자 정의 `data-속성` 사용하기
