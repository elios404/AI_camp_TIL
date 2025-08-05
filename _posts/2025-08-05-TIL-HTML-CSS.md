---
layout: post
title: "8월 5일 화요일 TIL(HTML, CSS)"
date: 2025-08-05 18:00:00 +0900
categories: July_week
---

# 8월 5일 화요일 TIL 작성

## 1. 학습 주제
- HTML
- CSS

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### HTML과 CSS가 화면에 보이는 과정
1. HTML & CSS 파싱 (Parsing)
HTML Parser: 브라우저는 먼저 HTML 파일을 위에서 아래로 읽으면서(파싱), <html>, <head>, <body> 같은 태그들을 분석

    CSS Parser: 동시에 CSS 파일도 읽어서(파싱), 셀렉터(h1, .class, #id)와 스타일(color: red;, font-size: 16px;)을 분석

2. DOM Tree & CSSOM Tree 생성
    DOM (Document Object Model) Tree: HTML 파서가 분석한 내용을 바탕으로, HTML 문서의 구조를 트리 형태의 객체 모델로 만든다. 각 HTML 태그는 노드(Node)가 되어 부모-자식 관계, 계층 관계를 형성한다. 이 부분이 우리가 자바스크립트로 접근하고 조작하는 DOM 부분이다.

    CSSOM (CSS Object Model) Tree: CSS 파서가 분석한 내용을 바탕으로, CSS 규칙들을 트리 형태로 만든다. 이 트리는 상속 관계에 따라 부모 노드의 스타일이 자식 노드에 적용되는 구조를 가진다. 즉 CSS는 상속과 비슷한 기능처럼 작동한다.

3. Rendering Tree 생성
    DOM Tree + CSSOM Tree: 브라우저는 DOM Tree와 CSSOM Tree를 합쳐서 **렌더링 트리(Rendering Tree)**를 만든다.

    이 과정에서 DOM Tree의 각 노드에 CSSOM Tree의 스타일 정보가 결합, 화면에 보이지 않는 요소들(예: display: none; 속성이 적용된 요소)은 이 렌더링 트리에서 제외한다.

4. Layout (또는 Reflow)
    레이아웃(Layout) 단계에서는 렌더링 트리를 기반으로 각 요소들의 정확한 위치와 크기를 계산한다.

    "이 버튼은 웹 페이지의 어디에 위치하고, 가로세로 크기는 얼마인가?"와 같은 정보를 계산하는 단계, 이 과정은 Reflow라고도 불린다.

5. Painting (또는 Repaint)
    페인팅(Painting) 단계에서는 레이아웃 단계에서 계산된 위치와 크기 정보를 바탕으로, 실제 화면에 픽셀을 그리는 단계이다.

    텍스트, 색상, 테두리, 그림자 등 시각적인 부분들을 화면에 나타내는 단계, 이것을 Repaint라고도 부른다.

6. Display
    최종적으로 페인팅 과정을 거친 결과물이 사용자의 화면에 나타나는 단계이다.

이 과정들을 거쳐야 사용자가 웹 브라우저에서 HTML과 CSS로 작성된 웹 페이지를 볼 수 있게 된다.

### RESTful API, ajax 등과 위 6단계 사이의 관련성
이 과정에서 DOM, CSSOM tree를 수정하면 앞에 HTML을 다시 받거나 수정하지 않아도 렌더링이 진행된다. → 계속 이야기 했던, 데이터만 보내서 화면을 갱신하는 방법

js를 통해서 DOM handling을 하고, 이게 일어나면 렌더링이 다시 된다. 이 경우 HTML을 갱신, 즉 새로고침을 하지 않아도, 데이터만 보내고 이를 통해서 DOM tree를 변경하는 것으로 브라우져가 화면을 재 렌더링 하도록 하여 페이지를 다시 보여준다. **중요!! DOM Handling**

이전에는 데이터를 보낼때 XMLHTTPRequest를 통해서 XML으로 보냈지만, 이 경우 js로 데이터를 읽고 js로 사용하기가 불편하다. 따라서 js에서 사용하기 쉬운 형태인 json 형태로 보내는 것으로 변경되었다.

### HTML

#### HTML 구성 

요소(태그) : 화면에 출력되는 각각의 요소 객체

속성 : 요소에 출력되지 않는 부가적인 특성

엔티티 : &로 시작해서 ; 로 끝나는 마크업과의 충돌이 발생하는 문자를 대신 출력하는 방법, &nbsp; 같은 것, 소문자로 작성해야한다.

#### HTML의 특징

- 브라우저에 의해서 처리된다.
- 운영체계의 영향을 받지 않음. 운영체제에 의존성 없음 → **큰 장점**

#### HTML 태그

- 문서 및 메타 : `<head>,<body>,<title>,<meta>,<link>,<script>,<style>`
- 문단 : `<p>, <div>, <span> ,<ul>, <ol>, <li> `

    div는 크기 조절 가능, 한 줄을 전부 차지한다. block 모델
    span은 inline 모델, 크기 조절 불가 + content 의 사이즈에 의존
- 구조 : `<header>,<section>,<nav>,<article>,<aside>,<footer>` 등, 페이지의 어느 부분에 어떤 역할을 하는 것인지를 명확하게 표현, div는 너무 광범위한 표현
- 그 밖 :`<table>,<a>` 등

#### form 의 input 속성 (가장 일반적으로 사용 가능한 속성)

- radio/check
- date, time
- number
- color
- email

**form에서 label 사용**
HTML5에서 form 의 input에 대해서 label을 적는 것이 필수는 아니다. 그러나 name을 통해서 둘을 매핑을 하고, 입력이 어떤 값을 입력 받는지 알려주는 것이 좋다.

#### table 관련

vscode에서는 zen 코드 활용하여 tbody>tr*2>td*2 (tab)으로 쉽게 html 구조를 만들 수 있다.

`colspan="크기"` 를 통해서 크기 만큼의 열을 하나의 칸으로 합쳐서 표현할 수 있고 `rowspan="크기"`를 통해서 크기 만큼의 행을 하나의 칸으로 합쳐서 표현할 수 있다.

### CSS

재정의 하는 것이다. → 무엇을? 이미 스타일이 정의된 태그를

선언 방법

- 외부 선언 : 외부에 css 파일을 생성
- 내부 선언 : 내부에 `<style>` 태그 통해서 헤더 부분에 생성
- 요소 선언 : 태그의 속성을 통해서


### 3교시 (html_basic.html 실습)

HTML에 있는 여러 태그들을 잘 알면 좋을 듯

- details / summary : 요약을 클릭하면 구체적인 정보 표시
- mark : 강조하는 태그
- time : 단순히 시간과 관련된 것이라는 것을 알려줌
- meter : 진행도 바 같은 것을 0~1사이의 값으로 표현(백분위로)
- progress : 진행도 바를 개발자가 지정한 범위로 표현하고 싶을 때(값으로)

#### form 과 관련된 코드들

- datalist : select와 비슷하지만, 개수가 많아서 보기 어려울 때 검색하여 선택이 가능하다 → 편할 듯
- select :  갯수가 많지 않은 것은 이거로 사용하는게 편함
- password : 비밀번호 입력할 때 사용
- text에서 정규식(regular expression) : pattern 속성을 통해서 어떻게 입력 받아야 하는지를 지정
이것은 다양하게 변형하여 이용 가능
- email : 이메일이 형식에 맞게 입력되었는지 확인
- range : 슬라이드 바로 값을 선택할 수 있게 한다. oninput을 통해서 중간에도 값을 확인할 수 있다.

여러 HTML 태그들을 html_basic.html 파일을 통해서 확인할 수 있다!

### CSS

css_basic.html 파일을 통해서 css 실습

margin, padding, position(relative, absolute) 개념 학습

포지셔닝을 편하기 할 때 여러 개를 담을 부모를 만들고 이를 바탕으로 `position=absolute`로 위치를 조절하는게 좋을 때가 있다.

absolute는 - 좌표가 있다. → 부모 밖으로 나갈 수 있다.

기본적으로 블록 모델은 위에서 아래로 흐름이 구성된다. 이렇게 블럭 모델을 가로로 흐르게 하기 위해서는 `display: inline-block;`을 사용한다.

`display = flex;` 를 통해서 박스 모델을 인라인 블럭 처럼 사용할 수 있다.이때 flex가 적용된 요소들은 부모 사이즈에 맞춰서 알아서 전체적인 비율과 다른 박스들과의 비율을 맞춰서 크기가 조정된다. 또한 컨텐츠가 박스 밖으로 나가지 않고, 박스의 크기를 조절한다.

#### 웹페이지를 디자인할 때 생각할 것

1. 레이아웃을 정한다. → 와이어 프레임? 의 크기를 지정한다.
2. 공통 CSS 를 정의한다. 폰트, 배경 색 등등
3. 타켓 해상도를 만들고, 화면이 여러 해상도에서 어떻게 보이는지 확인해라.

CSS에서 부모의 스타일이 바뀌면, 자손 또한 그 내용을 상속받는다.

**link에서 mime을 쓰는 이유**

기본적으로 `<link rel="stylesheet" href="style.css" type="text/css" />`를 통해서 외부의 css파일을 HTML에 적용시킬 수 있다.

`link` 태그의 `type="text/css"`는 이 링크가 CSS 파일임을 명시적으로 알려주는 역할을 한다. HTML5 에서는 `rel="stylesheet"`를 통해서 자동으로 확인할 수 있지만, 기본적으로 명시적으로 속성을 추가해 줄 수 있다.

### CSS 작성 흐름

가장 상위 basic.css를 작성

그 이후 각 패키지, 각 페이지? 더 작은 흐름에 대해서 각각의 css 페이지를 작성

이때 각 css 파일은 상위의 파일을 상속받는 식으로 작성한다. 이 과정에서 import하는 방식이 더 좋을 수도

CSS는 적용될 때 위에서 아래로 적용, 오버라이드 된다. 즉 같은 객체에대해서는 가장 마지막에 작성된 스타일을 따라간다. 따라서 가장 외부, 가장 부모의 스타일 부터 작성하고, 자식을 작성해야한다.

### CSS 선택자

- 클래스 선택자 : .클래스 이름
- 아이디 선택자 : #아이디 이름

선택자에는 우선권이 있다. 복합 선택자 보다는 단일 선택자가 더 우선된다.

```HTML
<style>
    @import url('style.css');  /* css 파일을 넣는 또 다른 방법*/
    /*이렇게 넣는 것이 더 좋은 이유는 상속 방식을 사용할 수 있기 때문이다.*/

    /* 이건 그냥 예시, 그리 추천하지 않는 UI 스타일*/
    @import url('style.css') screen and (min-width: 200px) and (max-width: 400px);

    @media screen and (min-width: 200px) and (max-width: 400px) {
        #media_query {
            color: blue;
            font-weight: bold;
        }
    }

    /* 기본 선택자 */
    .class01 { /* 클래스 선택자*/
        background-color: silver;
    }
    #fruit { /* 아이디 선택자*/
        background-color: transparent;
    }

    /* 복합 선택자 */
    ul li {
        color: blue;
    } /* 자손(아래 전부) : 1칸 띄어서 */
    ul > li {
        background-color: pink;
    } /* 자식(바로 아래만) : > 사용 */
    li#기준 ~ li {
        color: plum;
    } /* 바로 뒤의 형제들(특정 아이디 뒤에 자신과 같은 레벨에 있는 속성들) : 자손에게는 적용 X */
    li#기준 + li {
        color: green;
    } /* 바로 뒤의 형제(1개만) */
</style>
```


## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시

src_react/basic의 html_basic.html, css_basic.html, css_basic2.html, style.css 코드를 참조할 수 있다.

## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
