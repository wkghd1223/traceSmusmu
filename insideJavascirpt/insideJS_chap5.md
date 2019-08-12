# 실행 컨텍스트와 클로저
살펴볼 내용
1. 실행 컨텍스트(Execution context)
2. 활성객체(Activation Object)와 변수객체(Variable Object)
3. 스코프 체인(Scope Chain)
4. 클로저(closure)

## 1. 실행 컨텍스트 개념
C언어에서 콜 스택에 들어가는 실행 정보와 비슷하다. ECMAScript에서는 실행 컨텍스트를 '실행 가능한 코드를 형상화하고 구분하는 추상적 개념'으로 기술한다.
<br>이를 앞의 콜 스택과 연관 짓는다면. __실행가능한 자바스크립트 코드 블록이 실행되는 환경__ 이라고 할 수 있다. 여기서 실행가능한 코드블록은 대게 함수가 된다. ECMAScript는 실행 컨텍스트가 형성되는 경우를 __세 가지__ 로 규정하고있다.
1. 전역코드
2. _eval()_ 함수로 실행되는 코드
3. 함수 안의 코드를 실행할 경우

보통의 경우 함수로 실행컨텍스트를 만들고 이 코드 블록 안에 변수 및 객체, 실행가능한 코드가 있다. 이 코드가 실행되면 실행 컨텍스트가 생성되고 실행 컨텍스트는 스택안에 차곡차곡 쌓이고 제일 위에 위치한 실행 컨텍스트가 현재 실행되고있는 컨텍스트이다.
>ECMAScript에서의 실행 컨텍스트 생성<br>
>현재 실행되는 컨텍스트에서 이 컨텏트와 관련 없는 실행 코드가 실행되면, 새로운 컨텍스트가 생성되어 스택에 들어가고 제어권이 그 컨텍스트로 이동한다.

    function ExContext1(){
        //
    };
    function ExContext2(){
        Excontext1();
    };
    ExContext2();

![execution_context](./img/execution_context.PNG)

스택의 구조로 실행된다.

## 2. 실행 컨텍스트 생성 과정
이번 장에 중요한 개념
* 활성객체와 변수객체
* 스코프 체인
---

    function execute(param1, param2){
        var a =1,b=2;
        function func(){
            return a+b;
        }
        return param1+param2+func();
    }
    execute(3, 4);

---
### 2.1 활성 객체 생성
