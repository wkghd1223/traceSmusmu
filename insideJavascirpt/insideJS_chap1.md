# 자바스크립트
## 1. 자바스크립트란
웹 브라우저에서 동작하는 스크립트 언어

초기에는 대부분의 로직은 서버에서 담당했다. 그러나 시대의 흐름에 따라 웹 서버에서 담당하던 부분을 웹 브라우저가 담당하는 부분이 많아지며 javascript의 위치도 높아졌다.

## 2. 자바스크립트의 활용범위
기본적으로 자바스크립트는 웹페이지를 개발하는 용도의 언어이다. 그러나 node.js로 서버의 개발, 또 브라우저OS를 통해 그 OS위에서 구동하는 어플리케이션 개발 등 여러가지의 용도로 사용된다.

## 3. 자바스크립트의 핵심 개념
### 3.1. 객체
자바스크립트는 기본데이터타입, null, 그리고 undefined를 제외한 모든 것이 객체이다.
### 3.2. 함수
자바스크립트는 함수도 객체이다. 함수는 일급 객체(First class Object)로 다뤄진다.
### 3.3. 프로토타입
모든 객체는 숨겨진 링크인 프로토타입을 가진다. 이 링크는 객체를 생성한 생성자의 프로토타입 객체를 가리킨다. 자료구조를 작성하는데 편의를 제공한다.
> ECMAScript에서는 [[prototype]]이라고 표현한다.
### 3.4. 실행 컨텍스트와 클로저
자바스크립트는 실행 컨텍스트를 만들고 그 안에서 실행이 이루어진다. 이 컨텍스트는 유효범위를 갖는데 이 과정에서 클로저를 구현할 수 있다.

## 4. 자바스크립트와 객체지향 프로그래밍
자바스크립트는 클래스를 지원하지 않는다. 그러나 객체지향 프로그래밍이 가능하다.

## 5. 자바스크립트와 함수형 프로그래밍
자바스크립트는 함수형 프로그래밍이 가능하다. 이는 프로그램의 높은 수준의 모듈화를 가능하게 한다. 일급 객체로서의 함수의 특성과 클로저를 활용하여 이를 가능하게 하는데 그 때문에 가독성이 떨어지기도 한다.

## 6. 자바스크립트의 단점
자바스크립트는 굉장히 유연한 언어이다. 이것은 장점이 되기도 하지만 단점이 되기도 하는데 컴파일 타임에서 잡지못한 타입 불일치 같은 오류는 런타임 오류로 발견된다.
