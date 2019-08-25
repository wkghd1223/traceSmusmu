# jQuery 소스코드 분석

jQuery는 웹을 개발할 때 필요한 DOM파싱, 이벤트 처리, Ajax 같은 기능을 아주 쉽게 작성할 수 있게 도와주는 라이브러리다.

## 1. jQuery 1.0 소스 코드 구조
jQuery 소스코드의 전체 구조를 먼저 파악해 보자

### 1.1. jQuery 함수 객체
jQuery 라이브러리는 다음 예제와 같이 jQuery() 함수의 정의부터 시작한다. 이 함수는 여러가지 기능을 하는데 _new_ 연산자를 이용해 jQuery 객체를 생성하는 기능이다.

    function jQuery(a, c){
        // Shortcut for document ready (because $(document).each() is silly)
        if (a && a.constructor == Function && jQuery.fn.ready )
            return jQuery(document).ready(a);
        
        // Make sure that a selection was provided
        a = a || jQuery.context || document;

        // Watch for when a jQuery object is passed as the selector
        if (a.jquery)
            return $( jQuery.merge(a, []));
        
        // Watch for when a jQuery object is passed at the context
        if ( c && c.jquery )
            return $( c ).find(a);

        // If the context is global, return a new object 
        if (window == this )
            return new jQuery(a, c);

        // Handle HTML strings
        var m = /^[^<]*(<.+>)[^>]*$/.exec(a);
        if ( m ) a = jQuery.clean( [ m[1] ] );

        // Watch gor when an array is passed in
        this.get( a.constructor == Array || a.length && !a.nodeType && a[0] != undefined && a[0].nodeType ? 
            // Assume that it is an array of DOM Elements
            jQuery.merge(a, []) :

            // Find ths matching elements and save them for later
            jQuery.find(a, c)
        );

        // See if an extra function was provided
        var fn = arguments[ arguments.length - 1];

        // If so, execute it in context
        if ( fn && fn.constructor == Function )
            this.each(fn);
    }

### 1.2. 변수 $를 jQuery() 함수로 매핑
일반적으로 개발자들이 jQuery를 사용할 때 jQuery() 함수를 호출하기보다 $()와 같은 형태로 jQuery의 기능을 호출하는 것이 대부분이다. 이 것은 jQuery함수를 $변수에 매핑시켰기 때문이다.

    var $ = jQuery;

### 1.3. jQuery.prototype 객체 변경
모든 함수객체는 prototype 프로퍼티가 있다. prototype 프로퍼티가 가리키는 프로토타입 객체는 함수가 생성자로 동작할 때 이를 통해 새로 생성된 객체의 부모 역할을 한다. 따라서 다음과 같은 관계가 성립한다.
![8_prototypeOfjQuery](./img/8_jQueryPrototype.PNG)

new 연산자로 jQuery() 생성자 함수를 호출할 경우 생성된 jQuery객체는 [[Prototype]]링크로 자신의 프로토 타입인 jQuery.prototype 객체에 접근이 가능하다. 때문에 모든 jQuery 객체 인스턴스는 자신의 프로토 타입인 jQuery.prototype 객체의 프로퍼티를 공유할 수 있다.

jQuery() 함수 정의 후 jQuery.prototype 디폴트 객체를 객체 리터럴 형식의 다른 객체로 변셩하고 이 변경된 jQuery의 프로토타입을 jQuery.fn 프로퍼티가 참조하게 한다. 이렇게 하면 변경된 프로토타입 객체에 정의된 메소드를 사용할 수 있다.

    jQuery.fn = jQuery.prototype = {
        jquery : "$Rev$",
        size : function (){
            return this.length;
        },
        ...
    }

이렇게 변경하면 다음과 같은 그림의 관계가 된다.
![8_modifiedPrototypeOfjQuery](./img/8_modiprotojQ.PNG)

### 1.4. 객체 확장 - extend()메소드
jQuery 소스코드에서는 __extend()__ 메소드를 제공한다.

다음 코드는 _jQuery.extend()_ 와 _jQuery.fn.extend()_ 에 관한 것이다.

    jQuery.extend = jQuery.fn.extend = function(obj, prop) {
        if (!prop) { prop = obj; obj = this }
        for (var i in prop ) obj[i] = prop[i];
        return obj;
    }
_jQuery.extend()_ 와 _jQuery.fn.extend()_ 는 같은 함수를 참조한다.
![8_extend](./img/8_extend.PNG)

첫 번째 인자 obj 객체에 두 번째 인자 prop객체의 모든 프로퍼티를 추가한다.

jQuery.extend() 호출로 jQuery 함수 객체의 기능을 확장하고 jQuery.fn.extend() 호출로 jQuery.prototype 객체의 기능을 확장한다.

### 1.5. jQuery 소스코드의 기본 구성 요소
jQuery 소스코드는 세 부분으로 구분된다.
* jQuery 함수객체
* jQuery.prototype 객체
* jQuery 객체 인스턴스

![8_mainprop](./img/8_mainprop.PNG)

jQuery() 함수의 가장 기본적인 역할은 new 연산자로 jQuery 객체를 생성하는 것이다. 이렇게 생성된 jQuery 객체는 프로토타입 체이닝으로 jQuery.prototype 객체에 포함된 프로토타입 메소드를 호출할 수 있다. 

jQuery 함수 객체는 메소드를 포함한다. 이 메소드는 jQuery 인스턴스 객체에 특화되지 않고 범용적으로 사용되는 jQuery 코어 메소드로 구성된다.

## 2. jQuery의 id 셀렉터 동작 분석
jQuery의 가장 기본적인 기능은 HTML 문서에서 원하는 DOM객체를 선택한 후, 해당 객체의 속성을 변경하거나 효과, 이벤트등을 처리하는 것이다.

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <script src="http://code.jquery.com/jquery-latest.js"></script>
    </head>
    <body>
        <div id = "myDiv">Hello</div>
        <script>alert($("#myDiv").text())</script>
    </body>
    </html>
이런식으로 id 셀렉터를 사용한다.

### 2.1. $("#myDiv") 살펴보기
$는 jQuery 함수를 참조하기 때문에 _$("#myDiv")_ 는 _jQuery("#myDiv")_ 를 의미한다. jQuery함수에서 첫 번째 인자 a 에는 문자열 "#myDiv", 두 번째 인자 c에는 undefined가 전달된다.
