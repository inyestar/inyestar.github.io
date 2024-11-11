---
layout: post
title: Javascript Type Coercion
categories: ["javascript"]
---

### 정의
- coercion의 사전적 정의는 '강제'
- 어떤 타입을 다른 타입으로 변환하는 프로세스<br>
&nbsp;&nbsp;&nbsp;&nbsp;예) string → number, object → boolean 등
- 마음속으로 '강제 형변환'이라고 명명하고 기사를 읽고 있음..^^;
<br>
<br>

### 명시적 vs 암시적 변환
#### 명시적 변환
- 개발자가 의도를 가지고 형변환을 한 경우
- Type Casting 이라고도 함
    {% highlight js %}
    var str = '0'
    Number(str)
    // 0
    {% endhighlight %}

#### 암시적 변환
- 타입에 비교적 자유로운 자바스크립트에서 자동으로 일어나는 형변환
- 서로 다른 타입의 값 끼리 연산자를 통해 비교하거나 if와 같은 주변 컨텍스트에 의해 발생
    {% highlight js %}
    2 / '5'
    // 0.4
    null + new Date()
    // "nullTue Feb 23 2021 20:40:13 GMT+0900 (대한민국 표준시)"
    if('notboolean') console.log('hi')
    // hi
    {% endhighlight %}
- '===' 으로 표현되는 연산자 사용할 경우 암시적 형변환은 발생하지 않는다.
<br>
<br>

### 변환 유형
- 기본형과 object(변환 로직은 다르지만)는 아래 세 가지로만 변환이 가능하다.

#### String 변환
- 명시적 : String(값)
- 암시적 : 피연산자인 문자열과 '+' 연산자가 만날 경우 발생함
    {% highlight js %}
    String(123) // 명시적
    // "123"
    123 + '' // 암시적
    // "123"
    {% endhighlight %}
- Symbol 타입은 명시적으로만 문자열로 변환이 가능하다.<br>
&nbsp;&nbsp;&nbsp;&nbsp;예) String(Symbol('my symbol'))

#### Boolean 변환
- 명시적 : Boolean(값)
- 암시적 : 논리적 컨텍스트나 논리 연산자(\|\|, &&, !)에 의해 발생
- false로 간주되는 경우 : '', 0, -0, NaN, null, undefined, false
    {% highlight js %}
    Boolean(2) // 명시적
    // true
    if (2) console.log('yes') // 암시적
    // yes
    !!2
    // true
    2 || 'hello'
    // 2
    {% endhighlight %}
- 논리 연산자 \|\| 와 &&는 내부적으로 boolean 변환을 수행하지만, 피연산자의 원래 값을 반환한다.

#### Number 변환
- 명시적 : Number(값)
- 암시적 : 아래 요소들에 의해 발생함
  - 비교 연산자(>, <, ≤, ≥)
  - 비트연산자(\|, &, ^, ~)
  - 산술 연산자(-, +, *, /, %)
  - 동등 연산자 (==, !=)
  - 단항현산자 (+, -)
        {% highlight js %}
        Number('123') // 명시적
        // 123
        123 != '456' // 암시적
        // true
        4 > '5'
        // false
        5/null
        // Infinity
        true | 0
        // 1
        + '123'
        // 123
        {% endhighlight %}
- 문자열을 Number로 변환할 경우, 엔진은 먼저 문자열 앞뒤 공백이나 \n 이나 \t 문자를 잘라낸다.<br>
&nbsp;&nbsp;&nbsp;&nbsp;잘라낸 문자열이 유효한 숫자가 아닐 경우 NaN(Not a Number)을 반환한다.<br>
&nbsp;&nbsp;&nbsp;&nbsp;문자열이 공백일 경우 0을 반환한다.
- null은 0으로 변환되지만 undefined는 NaN으로 변환된다.
- Symbol 타입은 Number로 변환되지 않으며, TypeError를 던진다.
  - null과 undefined에 == 연산자를 적용할 경우 Number 변환은 일어나지 않는다. <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;null은 undefined나 null로만 일치할 수 있기 때문이다.

  - NaN는 자기 자신 혹은 다른 어떤것이라도 일치하지 않는다.
<br>
<br>

### Object의 형변환
- Object → 기본형 → final type
- ToPrimitive 메소드
- Object는 내부 메소드 ToPrimitive에 의해 기본형으로 변환된다.
- ToPrimitive 메소드는 입력 값(value)과 선호하는 변환 타입(prefferedType) 파라미터와 함께 전달된다.
- Object.prototype에 정의된 valueOf와 toString을 이용하여 Number 혹은 String으로 변환된다.
- 알고리즘
  - input이 기본형일 경우 아무것도 하지 않고 그대로 반환한다.
  - input.toString()을 호출한 뒤 결과가 기본형일 경우 그대로 반환한다.
  - input.valueOf()를 호출한 뒤 결과가 기본형일 경우 그대로 반환한다.
  - 위 두 단계에서 기본형이 나오지 않을 경우 TypeError를 던진다.
- 대부분의 내장 타입들은 valueOf 함수가 아예 없거나 있어도 자기 자신인 this를 반환하고 있다.<br>
&nbsp;&nbsp;&nbsp;&nbsp;this를 반환할 경우에 valueOf의 결과는 기본형이 아니므로 무시된다. <br>
&nbsp;&nbsp;&nbsp;&nbsp;따라서 Number 변환이나 String 변환이나 toString을 호출한 것과 같아진다.
- ==나 이진 + 연산자의 경우 default 변환(prefferedType 파라미터가 특정되지 않거나 default인 상태)을 일으킨다. <br>
&nbsp;&nbsp;&nbsp;&nbsp;이러한 경우 Date을 제외한 대부분의 내장 타입들은 default로 Number 변환을 수행한다.
- ES5에서는 toString이나 valueOf 메소드를 오버라이딩하는 방식으로 변환 로직을 수정하였으나<br>
ES6에서는 Object의 Symbol.toPrimitive 메소드를 구현하여 ToPrimitive 메소드를 완전히 대체할 수 있게 되었다.
- Boolean 변환의 경우 모든 기본형이 아닌 값들은 true로 변환됨

### References
- [https://www.freecodecamp.org/news/js-type-coercion-explained-27ba3d9a2839/](https://www.freecodecamp.org/news/js-type-coercion-explained-27ba3d9a2839/)
