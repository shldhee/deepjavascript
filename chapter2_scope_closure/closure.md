# 클로저

> 특정 함수가 참조하는 변수들이 선언된 렉시컬 스코프(lexical scope)는 계속 유지되는데, 그 함수와 스코프를 묶어서 클로저라고 한다.

클로저가 나타나는 가장 기본적인 환경은 스코프 안에 스코프가 있을 때, 즉 function 안에 function이 선언되었을 때이다.

``` js
function outer () {
    var count = 0;
    var inner = function () {
        return ++count;
    };
    return inner;
}
var increase = outer();

console.log(increase());  // 1
console.log(increase());  // 2
```

순서대로 코드 분석하면서 클로저를 살펴보자.

1. `count`변수는 `outer()` 함수의 로컬 변수이다. 따라서 원칙적으로 `outer()` 함수 내부에서만 접근할 수 있다.
1. `outer()`함수 내부에 다시 함수를 하나 선언하여 `inner`변수에 할당
1. `inner`변수에 할당한 함수는 `outer`함수의 로컬 변수인 `count`에 접근하여 1만큼 증가시키고 이 값을 반환
1. `outer()`함수 반환값으로 `inner`변수를 지정하면서 함수 정의를 마쳤다.
1. 방금 정의한 `outer()` 함수를 호출하어 글로벌 변수인 `increase`에 할당
1. `increase`변수에 할당되는 값은 `outer()`함수의 반환값으로 지정한 `inner`변수이다.
1. 따라서 `increase` 변수를 함수로 호출하면, 결과적으로 `inner`함수가 호출되어 `count`변수의 값을 1 증가시킨다.

* `count`변수는 `outer()` 함수의 로컬 변수이므로 일반적인 방법으로 외부에서 접근이 불가하다.

1. `count`변수에 접근하는 또 다른 함수 `inner`를 `outer()`함수의 반환값으로 지정하고
1. 이를 글로벌 영역에 있는 `increase` 변수에 할당함으로써
1. `outer()` 함수 외부에서도 `increase` 변수를 통해 `count` 변수에 접근할 수 있게 되었다.

클로저의 응용

``` js
function outer () {
    var count = 0;
    return {
        increase : function () {
            return ++count;
        },
        decrease : function () {
            return --count;
        }
    };
}
var counter = outer();
console.log(counter.increase()); // 1
console.log(counter.increase()); // 2
console.log(counter.decrease()); // 1

var counter2 = outer();
console.log(counter2.increase()); // 1
```

1. 이전 예제와 비슷하지만 `outer()` 반환값이 함수가 아니고 새로운 객체이다.
1. `counter`변수에 `outer()`반환 값이 객체를 할당.
1. counter.increase, coutner.decrease로 접근이 가능하여 `count`변수를 참조 할 수 있다.
1. 또 다른 변수 `counter2`는 함수가 호출될 때 별도의 스코프가 생성되여 `count`변수가 따로따로 저장된다.

`outer()` 생성한 모든 변수들이 동일한 `count`를 공유하는 static 변수처럼 만들려면?
아마 글로벌 변수를 사용하는것이 좋지만 많은 개발자들이 글로벌 변수 사용을 자제하고 있으니
IIFE와 클로저를 응용해보자.

``` js
var countFactory = (function () {
    var staticCount = 0;
    return function() {
        var localCount = 0;
        return {
            increase: function() {
                return {
                    static: ++staticCount,
                    local: ++localCount
                };
            },
            decrease: function() {
                return {
                    static: --staticCount,
                    local: --localCount
                };
            }
        };
    };
}());
var counter = countFactory(), counter2 = countFactory();
console.log(counter.increase()); // 1, 1
console.log(counter.increase()); // 2, 2
console.log(counter2.decrease()); // 1, -1
console.log(counter.increase()); // 2, 3
```

* `counter` 예에서 다시 IIFE로 스코프를 하나 더 추가하여 최상위에 `staticCount` 변수를 추가
* `static`으로 사용할 로컬 변수를 선언한 다음, 앞의 예제에서 사용했던 함수를 반환한다.
* 해당 함수가 실행되면 정의된 객체를 반환하여 `counter`별로 `localCount`변수를 사용할 수 있고
* 공용으로 `staticCount` 변수를 사용할 수 있다.

***클로저는 `outer()`와 같은 외부 함수에서 `inner`와 같은 내부 함수를 반환하여 다른 곳에서 해당 함수를 호출할 때 발생한다는 것을 알 수 있다.***

## 클로저 쉽게 이해하기

소스가 실행됨에 따라 스코프 체인이 내부적으로 어떻게 구성되었는지 살펴보면서 클로저를 이해해보자.

*클로저의 기본적인 예*

``` js
function sum(base) {
    var inClosure = base;
    return function (adder) {
        return inClosure + adder;
    };
};
var fiveAdder = sum(5); //inClosure = 5, return function (adder)
fiveAdder(3); // inClosure = 5 + adder = 3 , === 8
var threeAdder = sum(3); // inClosure = 3, return new function (Adder)
```

sum()함수의 스코프체인 구조

``` js
global scope
fiveAdder
ThreeAdder {
            <-sum (base)
            base
            inClosure {
                        <-function (adder)
                        adder
                        }
            }
```

`fiveAdder`는 실제로 사용하게 되는 함수 `adder`를 할당받게 되어 오른쪽의 `adder` 함수를 가지고 있게 된다.
그리고 `adder`가 반활될 때는 위와 같이 스코프 체인을 생성하여 `fiverAdder` 함수가 호출될때 이 스코프 체인을 사용하게 된다.
`fiveAdder`에서 할당받는 함수 `adder`는 `sum()`함수에 첫번째 리턴 반환값인 익명함수다.

* 이제부터 `fiveAdder`를 통해 함수를 호출하게 되면 위의 스코프 체인을 따르게 된다.
* 모든 스코프 체인은 글로벌 영역에서 끝난다.
* 글로벌 영역에서 `fiveAdder`가 가지고 있는 것은 다시 `Adder`를 참조하는 스코프체인이 아니라 함수 `Adder`에 대한 레퍼런스, C언어로 말하자면 포인터를 가지고 있는 것이다.
* 나중에 `fiveAdder`를 통하여 함수를 호출하게 되면 위의 `fiveAdder`가 레퍼런스를 가지고 있는 오른쪽 `Adder`에 해당하는 스코프 체인을 사용
* 이 함수 `Adder`가 사용하는 스코프 체인은 함수 `Adder`를 가리키는 레퍼런스가 사라질 때까지 계속 남아있게 되어 스코프가 유지된다.

위와 같이 sum()함수가 호출되면서 함수 표현식이 실행되면 함수 threeAdder라는 새로운 스코프 체인을 생성
sum() 함수를 호출할 때마다 새로운 함수와 그 함수가 참조하는 스코프 체인이 각각 생성된다.

``` js
fiveAdder.toString();
threeAdder.toString();

fiveAdder !== threeAdder
```

`toString()` 함수로 보이는 값은 같지만, 두 개의 변수는 같지 않다.
`sum()`함수를 호출할 때마다 같은 모양의 함수들이 매번 새롭게 나오지만, 두 함수가 같지 않은 이유는 두 함수가 할당받은 스코프 체인, 숨겨져 있는 클로저가 다르기 때문

참고
new Function과 function (){}를 사용하는 경우 내부적으로 비슷하다고 했다.
비슷하다고 한 이유는 new Function으로 생성한 함수는 별도의 스코프 체인을 할당받지 않고 로컬 변수에만 접근할 수 있어서 클로저를 활용할 수 없기 때문이다.
