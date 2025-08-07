---
layout: post
title: "8월 6일 수요일 TIL(CSS, JavaScript)"
date: 2025-08-06 18:00:00 +0900
categories: July_week6
---

# 8월 일 요일 TIL 작성

## 1. 학습 주제
- CSS
- JavaScript


## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### CSS
CSS는 HTML의 디자인을 덮어 씌우는 것, cascading stylesheet

#### **Cascading의 의미**

'Cascading'은 '위에서 아래로 흐르는'이라는 의미로, 여러 개의 스타일 규칙이 충돌할 때 어떤 규칙을 우선적으로 적용할지 결정하는 **우선순위** 원리를 뜻한다.  예를 들어, 같은 HTML 요소에 대해 여러 CSS 규칙이 존재할 경우, 특정 순서(인라인 스타일 > ID 선택자 > 클래스 선택자 > 태그 선택자 등)에 따라 최종 스타일이 결정된다.

#### **노드(Node)란?**

HTML의 DOM Tree에서 각각의 계층에서 요소들을 노드라고 한다. 노드를 생각하면 **계층 구조**를 생각해야 한다. 또한 상위 노드를 부모 노드, 하위 노드를 자식 노드 라고 할 수 있다.

웹 브라우저는 HTML 문서를 읽어 **DOM(Document Object Model)**이라는 트리 구조의 모델을 만든다. 이 트리 구조를 이루는 모든 개별적인 객체(HTML 요소, 속성, 텍스트 등)들을 통칭하여 노드라고 한다.

#### **노드의 종류**

노드는 크게 세 가지로 나눌 수 있다.

- **요소 노드(Element Node):** `<body>`, `<p>`, `<div>`와 같은 HTML 태그, DOM Tree의 가장 중요한 구성 요소이다.
- **속성 노드(Attribute Node):** `href="..."`, `class="..."`와 같이 HTML 요소의 속성들이다.
- **텍스트 노드(Text Node):** `<p>`태그 안에 있는 "안녕하세요"와 같은 실제 텍스트 내용이다.


#### 속성 선택자, 가상 선택자

`li[title]` 과 같은 `태그[속성]` 을 통해서 특정 태그의 특정 속성을 선택하여, 스타일을 지정할 수 있다.

**가상 선택자**

구체적인 요소의 지정이 아니라, 요소의 순서 등을 통해서 스타일을 지정

#### CSS (media, font)

`@media` 출력 매체에 따라서 CSS 기능을 정의 할 수 있는 기능이다. 출력 메체는 screen(화면), print(인쇄) 등을 선택할 수 있다. 또한 미디어 쿼리를 이용해서 폭, 높이 등 CSS 기능의 적용 조건을 지정할 수 있다. (반응형 디자인을 만들 수 있다.)

`@font-face` 를 통해서 시스템에 설치되지 않는 폰트를 등록하고 이용할 수 있게 한다. 웹페이지에 있는 폰트를 사용자 컴퓨터에 다운받게 한다.

#### CSS 표준 단위

px, pt, pc, mm, vh, % em 등 여러 단위가 있다. 사용하기 편한 것으로 선택 가능.

단, 하나의 단위로 통일해서 프로젝트를 진행하는 것이 좋다.

### 자바 스크립트

#### JQuery 사용

https://www.jsdelivr.com/package/npm/jquery 을 통해서 jQuery 사용 가능

jsDelivr 에서 jQuery를 검색하여 HTML 붙여넣기를 통해서 jquery를 사용할 수 있다. 또한 jquery를 사용하면 `$( )` 를 통해서 선택자를 통해 쉽게 선택하고 스크립트를 적용할 수 있다.

#### JavaScript 실습 시작(js_01_basic.js)

```jsx
let a = 10; // 변수
const b = '가나다'; // 상수
var c = true; // 프로퍼티(windows, global 객체), 즉 전역변수!
```

자바 스크립트는 사용자 브라우져에서 다운로드하여 실행해야 하기에 코드의 내용을 숨길 수가 없다. 코드가 공개 된다.

#### **호이스팅(Hoisting)과 `var`**

자바스크립트에서 `var`로 선언된 변수는 코드가 실제로 실행되기 전에 해당 스코프의 맨 위로 끌어올려진 것처럼 동작하는데 이 과정을 **호이스팅**이라고 한다.

즉, 엔진은 코드를 다음과 같이 해석한다.


```jsx
// 코드 실행 전, var 변수 선언이 최상단으로 끌어올려짐
var c;
var console;

// 코드 실행 시작
c = true;
//...

// 그러나 이미 `console`이라는 변수가 선언되어버렸기 때문에,
// 기존의 내장 객체인 `console`은 덮어씌워지고 'undefined' 상태가 됩니다.
// 이 상태에서 `console.log`를 호출하면,
// 'undefined'에 '.log'라는 프로퍼티가 없으므로 에러가 발생합니다.
```

`console.log(console)`이 실행되기 전에 `var console`의 선언이 먼저 처리되므로, 원래의 `console` 객체는 이미 새로운 변수로 덮어씌워지게 된다. 따라서 기존 `console` 객체의 `log` 메서드를 찾을 수 없어 에러가 발생한다.

#### JS의 데이터 타입

```jsx
console.log(10 + 20); // 상수 풀 사용

// 자바 스크립트 데이터 타입들
console.log(typeof 10); // 'number' 로 타입이 출력됨.
console.log(typeof 10.1); // 정수, 실수 구분없이 'number' 출력됨.
console.log(typeof '12345'); // 'String' 타입으로 출력됨.
console.log(typeof true); // boolean 타입으로 출력됨.
console.log(typeof function () {}); // 'function' 타입을 가짐
// 데이터 타입으로 함수를 가지고 있으면 함수 지향형이라고 한다.
console.log(typeof ((i) => 1)); // 람다식 'function' 데이터 타입
console.log(typeof {}); // object 타입으로 출력됨. 자바스크립트에서 객체를 만드는 방법(new 필요 없음)
console.log(typeof []); // object 타입, js에서 배열
console.log(typeof aaaa); // undefined 타입, 아직 초기화가 되지 않았다는 의미
```

JS는 모든 값에 대해서 타입을 추론한다. 그렇기에 let으로 변수를 정의해도 알아서 타입을 추론하고 타입에 맞게 변수를 저장한다. 

undefined는 이렇게 타입을 추론하는 것에서 아직 초기화, 타입 추론이가 되지 않았다는 의미이다..?

#### **`undefined`의 정확한 의미**

`undefined`는 **'값이 할당되지 않은 상태'**를 의미하는 자바스크립트의 **원시 타입(Primitive Type)** 중 하나.  `typeof` 연산자가 `undefined`를 반환하는 경우는

1. **선언했지만 값을 할당하지 않은 변수:**JavaScript
    
    `let myVar;
    console.log(typeof myVar); // 'undefined'`
    
2. **존재하지 않는 변수나 객체 속성:**JavaScript
    
    `console.log(typeof nonExistentVar); // 'undefined'`
    

따라서 `undefined`는 **'초기화되지 않았다는 의미'**로 이해하는 것이 더 정확하다.

#### **자바스크립트의 동적 타이핑**

자바스크립트는 **동적 타이핑(Dynamically Typed)** 언어. 이는 **변수를 선언할 때 타입을 명시할 필요가 없고, 값이 할당될 때 그 값의 타입에 따라 변수의 타입이 결정**된다.

> "자바스크립트는 모든 값에 대해서 타입을 추론한다"는 말은, 정적 타이핑 언어(Java, C++)처럼 컴파일 시점에 타입을 결정하는 것이 아니라, 코드가 실행되면서 값이 할당될 때마다 타입이 유동적으로 정해진다는 것을 뜻합니다.
> 
> 
> `let`으로 변수를 선언하면, `undefined` 상태로 메모리에 올라갔다가 이후에 어떤 값을 할당하느냐에 따라 타입이 결정됩니다. `undefined`는 타입 추론의 결과가 아니라, **아직 타입이 결정될 값이 할당되지 않았음을 나타내는 값**이라고 할 수 있습니다.
> 

#### **자바스크립트의 값 저장 방식**

자바스크립트는 값을 저장할 때 **원시 타입(Primitive Type)**과 **참조 타입(Reference Type)**을 구분합니다.

- **원시 타입 (Primitive Type):** `string`, `number`, `boolean`, `null`, `undefined`, `symbol`과 같은 값들은 **스택(Stack)** 메모리에 **실제 값**이 직접 저장됩니다.
- **참조 타입 (Reference Type):** `object` (객체, 배열, 함수 등)는 **힙(Heap)** 메모리에 실제 값이 저장되고, 스택 메모리에는 그 값을 가리키는 **메모리 주소(참조 값)**가 저장됩니다.

따라서, 자바스크립트는 모든 값을 객체로 만들고 힙에 저장하는 것이 아니라, 원시 타입과 참조 타입을 구분하여 저장합니다. 파이썬은 모든 데이터를 객체로 취급하며, 변수가 객체의 참조를 저장한다는 점에서 차이가 있습니다.

**`근데 강사님은 스택에 변수명과 주소가 저장되고, 힙 메모리에 값이 저장된다고 함. 모든 값이. 어느것이 맞는 말일까? 숨겨진 의미가 있을 수도?`**


### 자바 스크립트의 데이터 타입

- Number
- String
- boolean
- function (함수 지향형은 함수를 데이터 타입으로 가질 수 있다.)
- object
- undefined

### 자동 타입 변환, 형변환

```jsx
console.log(1 + '2'); // 12로 출력됨 -> 근데 이건 문자열
console.log('1' + 2); // 위와 동일
// '+'는 문자열 더하기와 숫자 더하기 둘 다 있는데, 문자열 더하기가 우선됨.
console.log('1' / 2); // 0.5로 나옴 -> 자바가 알아서 타입을 바꾼다. 정확히 사용해야, side effect가 없다.
console.log('1' * 1 + 2); // * 연산자 때문에 '1'이 숫자로 바뀌고 2를 더해 3이 출력됨.

// 형변환
console.log(Number('1') + 2);
console.log(Number(true) + 2);
console.log(Number('abcd')); // NaN(Not A Number)
console.log(Number('123abcd')); // NaN(Not A Number)
console.log(isNaN(Number('123abcd'))); //NaN 값인지를 확인, true 출력됨.
```

자바스크립트에서는 연산자 등으로 계산이 발생할 때, 자동으로 형변환이 된다. 이것은 사용자가 의도하지 않은 side effect가 발생할 수 있다.

따라서 Number(’1’) 과 같이 명시적으로 형 변환을 해줄 수 있다.

### 비교연산자

```jsx
console.log(123 == '123'); //true,  == 는 자료형이 아닌, 값만 비교한다.
console.log(123 === '123'); //false,  ===는 자료형과 타입까지 같은지 비교한다.
```

### 템플릿 문자열

```jsx
// 템플릿 문자열
console.log(`1+2=${1 + 2}`); //파이썬의 f-string 같은 느낌, {} 로 표현식 삽입 가능
```

### 구조 분해 할당

```jsx
// 구조분해할당 : 여러 변수를 배열 형태로 한 번에 할당하기
let arr = [1, 2, 3];
let [num1, num2, num3] = arr;
console.log(num1, num2, num3);

let [n1 = 0, n2 = 0, n3 = 0] = [1, 2]; //변수에 기본값 설정 가능
console.log(n1, n2, n3); // 마지막 n3 는 undefined

let { p1, p2, p3 } = {
    //객체에 대한 구조분해할당
    p1: 1,
    p2: 'str',
    p3: true,
};
console.log(p1, p2, p3);
```

구조 분해 할당은 여러 변수를 한 번에 할당할 때 사용한다. 특히 객체에 대한 구조분해할당은 api에서 데이터를 받았을 때 변수에 할당하기 매우 편하다.

### loop(반복문)
- `for (let i = 0; i < arr.length; i++)` : 기본적인 반복문
- `for (let i in arr)` : in 을 사용한 반복문, 이때 i는 arr의 요소가 아니라 인덱스이다.
- `for (let item of arr)` : of를 사용하면 직접적으로 배열의 요소를 하나씩 받는다.
- `arr.forEach(function (item) {console.log(item)})` : 함수형 프로그래밍 처럼 사용, 안에 익명 함수 사용 가능하다. '화살표 함수'라고도 한다.

#### 함수형 프로그래밍
map(전체 요소에 연산), filter(조건), reduce(잘 모름), some(요소 중 한 개라도 조건 만족하는가), every(전체 요소가 조건 만족하는가)

### Scope

JavaScript에서 this 키워드가 가리키는 대상과 var, let의 스코프 차이를 정리했다. 특히, this가 어디서 호출되느냐에 따라 값이 달라진다는 점이 중요했다.

Call Stack.에서 Context를 볼 때 자신이 알 수 없는 변수, 함수를 확인하면 그것이 상위의 Context에 있는지 확인한다. -> 이 과정이 Scope Chaining, 만약 최상위 까지 없다면, Undefined로 정의한다.

1. 전역(Global) 스코프의 this
    브라우저 환경에서 전역 코드(function이나 object 안에 있지 않은 코드)에서 this는 항상 **window** 객체를 가리킨다.

    - var로 선언된 변수는 window 객체의 속성으로 등록된다.

      - var name = '가'; -> window.name이 '가'가 된다.

      - this.name이나 window.name 모두 '가'가 출력된다.

    - let이나 const로 선언된 변수는 window 객체의 속성으로 등록되지 않는다.

      - let name1 = '나'; -> window.name1은 undefined가 된다.

2. 함수 안에서의 this
일반 함수를 호출했을 때, this는 window 객체를 가리킨다.

- function test()와 같이 선언된 함수는 window.test()와 동일하게 호출된다. 따라서 함수 내부의 this.name은 전역 변수인 window.name을 찾아 '가'를 출력한다.

- 이때, test 함수 내부에 선언된 var name = 'A'는 함수 스코프 내의 지역 변수이므로 console.log(name)은 'A'를 출력한다.

3. 객체(Object)의 메서드 안에서의 this
객체의 메서드(속성으로 정의된 함수) 안에서 this는 해당 메서드를 호출한 객체를 가리킨다.

- test2.test()처럼 호출하면, test 메서드 안의 this는 test2 객체를 가리킨다.

- 따라서 console.log(this.name)은 test2 객체의 name 속성인 '다'를 출력하게 된다.

4. bind()를 사용한 this 변경
Function.prototype.bind(thisArg) 메서드는 함수의 this 값을 강제로 지정해주는 역할을 한다.

- test: function() { ... }.bind(this)

  - 여기서 bind(this)의 this는 test2 객체가 선언된 전역 스코프의 this를 의미하므로, window 객체를 가리킨다.

  - 결과적으로 test2.test()를 호출해도 test 메서드 안의 this는 window가 되어, this.name은 '가'를 출력하게 된다.

이처럼 this의 값은 함수를 어떻게 호출하느냐에 따라 달라지며, 이를 명확히 이해하는 것이 중요하다는 것을 알게 되었다.

### JavaScript의 함수 매개변수
JS 는 function을 통해서 함수를 정의할 수 있다.

이때 **호이스팅**에 의해서 같은 이름을 가진 함수가 2개 이상 정의되면 가장 마지막에 있는 것으로 정의되고, 에러가 발생하지는 않는다. 같은 파일 내에서는 실제 코드를 실행하기 전에 변수, 함수등을 전부 스캔하고 context를 만들기 때문에 이런 일이 발생한다.

### 나머지값 매개변수, 전개 구문
`...변수명` 을 통해서 매개변수의 개수를 지정하지 않고, 임의의 수의 매개변수를 넣으면 배열처럼 받아지는 방식이 있다. 이것은 불변형 프로그래밍의 기본이 된다.

또한 **전개구분으로 사용할 수 있다.** 위에서는 각각의 매개변수를 배열처럼 하나로 받았다면, 배열에 `...변수명`을 사용한다면 배열을 전개해서 사용할 수 있게 한다. 이것은 객체(혹은 문자열 등)에도 동일하게 적용되어, 객체를 풀 수 있다.

### 불변형 프로그래밍 (중요!!)
```jsx
const obj = {
    name: '홍길동',
    age: 20,
}; // 불변형으로 정의
const obj2 = { ...obj, age: 30, addr: '광주' }; //기존의 데이터를 그대로 복제하고(깊은 복제), 수정하거나 새로운 것을 추가
// 원본을 보존하고, 새로 복사한 것에서 수정을 하고 새로운 것을 추가할 수 있다.
// 왜 원본을 보존해야 할까? : 원본과 수정을 비교해서 무엇이 변했는지를 확인하기 위해서(리액트에서?) -> 노션에 정리해봄(8월 7일)
console.log(obj, obj2);
```
즉 위의 방식으로 새로운 객체를 만들 수 있다.

### 함수 지향형 프로그래밍
```jsx
function add4(a, b, ...fn) {
    let c = 0;
    fn.forEach((func) => (c += func(a, b)));
    return c;
}

console.log(
    add4(
        10,
        20,
        (a, b) => a + b,
        (a, b) => a - b,
        (a, b) => a * b,
    ),
);
```
JS에서는 function 조차도 데이터 타입으로 함수를 매개 변수 등으로 넣을 수 있고, 변수에 저장할 수 있다.(익명 함수랑 비슷한 개념인듯)

### JS의 객체

1. 객체 리터럴 방식
    가장 간단하게 객체를 만드는 방법이다. key: value 쌍으로 객체를 즉시 정의한다.

    - const calc = { a: 1, b: 2, ... }와 같이 객체를 직접 선언한다.

    - 객체에 새로운 속성을 추가하거나 기존 메서드를 재정의할 수 있다. `calc.c = 3, calc.add = ...`처럼 자유롭게 확장할 수 있다. 즉 기존에 필드를 정의해 놓지 않아도, 새로운 필드를 만드는데 제한이 없다.

2. 생성자 함수와 프로토타입
    ```jsx
    /* 생성자 */
    function calc3(a, b) {
        // 함수 안에 this. 를 통해서 생성자로 만듬
        this.a = a;
        this.b = b;
    }

    // 자바 스크립트의 상속은 프로토타입 상속? 실제 객체가 만들어지기 전에 메서드를 정의하는 걸까?
    // 생성자 함수로 만들어질 모든 객체들이 '공유'할 add 메서드를 prototype 객체에 정의 -> 메모리 효율성을 높임.
    calc3.prototype.add = function () {
        return this.a + this.b;
    };

    let obj3 = new calc3(1, 2);
    console.log(obj3.add());
    ```
    new 키워드와 함께 사용하는 함수로, 여러 객체를 찍어내듯이 만들 때 유용하다.

    - function calc3(a, b)처럼 함수를 정의하고, new calc3(1, 2)로 객체를 생성한다.

    - this는 생성될 객체를 가리키며, this.a, this.b와 같이 속성을 정의한다.

    - prototype을 사용하여 메서드를 정의하면 메모리 효율성을 높일 수 있다. calc3.prototype.add = function() { ... }처럼 정의하면, 이 생성자로 만들어진 모든 객체들이 add 메서드를 공유하게 된다. 자바스크립트의 기본 상속 메커니즘인 프로토타입 상속의 핵심 원리다.

3. ES6 클래스 문법 (Class)
    ```jsx
    // 자바의 클래스 형태를 도입
    class Polygon {
        constructor(height, width) {
            //생성자
            this.height = height;
            this.width = width;
        }
        // set area(param)
        get area() {
            return this.height * this.width;
        }

        print() {
            console.log('면적:' + this.area);
        }
    }

    let poly = new Polygon(10, 20);

    // 상속
    class Square extends Polygon {
        constructor(length) {
            super(length, length);
        }
    }

    let squ = new Square(10);
    ```

    자바(Java)와 같은 언어의 클래스와 유사한 문법으로, 내부적으로는 프로토타입 기반으로 동작한다.

    - class 키워드를 사용해 객체의 설계도를 만든다.

    - **constructor**는 객체가 생성될 때 호출되는 생성자 함수이며, this로 속성을 정의한다.

    - get 키워드는 메서드를 속성처럼 접근할 수 있게 한다. get area()로 정의된 메서드는 poly.area처럼 소괄호 () 없이 속성처럼 호출할 수 있다. 이는 계산된 값을 속성처럼 사용하고 싶을 때 유용하다.

    - extends 키워드를 사용해 상속을 구현한다. Square extends Polygon처럼 작성하여 부모 클래스의 기능을 물려받을 수 있다.

    - **super()**는 부모 클래스의 생성자를 호출하여 부모의 속성(this.height, this.width)을 초기화하는 데 사용된다.


### JavaScript 비동기 처리와 Promise

1. 비동기 문제 발생과 해결
    문제: $.get('msg.json', ...) 요청을 보낸 후, 응답이 도착하기 전에 다음 코드인 console.log('2.', result)가 먼저 실행된다. 따라서 result는 빈 값으로 출력된다.

    해결: 요청이 완료된 후에 특정 코드를 실행하도록 순서를 보장해야 한다. jQuery의 .done()이나 Promise 기반의 .then()을 사용하면 된다.

2. Promise 기반의 비동기 처리
    Promise는 비동기 작업의 최종 완료 또는 실패를 나타내는 객체이다. 비동기 작업이 "성공하면" then()을 실행하고, "실패하면" catch() 또는 fail()을 실행하도록 약속하는 방식이다.

    .then() / .done(): 비동기 작업이 성공적으로 끝났을 때 실행할 콜백 함수를 등록한다. 여러 개를 이어서 체이닝(chaining)하여 순차적으로 코드를 실행할 수 있다.

    .catch() / .fail(): 비동기 작업 중 오류가 발생했을 때 실행할 콜백 함수를 등록한다.

3. 비동기 처리 도구들
    - jQuery의 .get().done(): jQuery에서 제공하는 비동기 처리 방식이다. .done()과 .fail()을 사용해 성공/실패 시 동작을 정의한다.

    - Axios의 .get().then(): jQuery보다 최신 라이브러리로, Promise 기반의 HTTP 통신을 쉽게 할 수 있게 해준다. .then()과 .catch()를 사용한다.

    - Fetch API: 자바스크립트에 내장된 기본 비동기 통신 기능이다. 별도 라이브러리 없이 사용할 수 있으며, Promise를 반환한다. 응답을 .json() 메서드로 한 번 더 처리해줘야 실제 데이터를 얻을 수 있다는 특징이 있다.

    - async/await: Promise를 더 간결하고 동기적인 코드처럼 작성할 수 있게 해주는 문법이다. async 함수 안에서 await 키워드를 사용하면 비동기 작업이 완료될 때까지 기다렸다가 다음 코드를 실행하게 된다.

    Promise 개념은 비동기 코드를 작성할 때 필수적이므로, then과 catch를 사용해 순서를 보장하는 방법을 잘 익혀야 한다는 것을 알게 되었다.


## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시

src_react/basic의 js_01~js_06 파일 참조

## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 

프론트 엔드 어렵다.. 자바 스크립트, 생각보다 자바 파이썬과 많이 다른 느낌인 것 같다.