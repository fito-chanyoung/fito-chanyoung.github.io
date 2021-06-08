---
title: "자바스크립트 톺아보기-1"
toc: true
toc_sticky: true

categories:
  - javascript

last_modified_at: 2020-06-7T14:36:00+00:00
---

이번에는 자바스크립트에 대해 알아본것들을 정리해 보고자 한다.

## 자바스크립트

우선 넷스케이프 커뮤니케이션즈 코퍼레이션의 브렌던 아이크(Brendan Eich)가
처음에는 모카(Mocha)라는 이름으로, 나중에는 라이브스크립트(LiveScript)라는 이름으로 개발한
프로그래밍 언어이다(by 위키피디아)

모든 브라우저에 인터 프리터가 내장되어 있기때문에 브라우저에서 사용하는 언어라 볼수
있을듯 하다.

혹시 넷스케이프 커뮤니케이션즈 코퍼레이션이란 회사가 지금의 오라클인것을
알고 있다면 자바스크립트는 자바와 비슷한것이 아닐까 생각할 수 있지만,
전혀 그렇지 않다. 자바는 JVM에서 돌아가는 프로그램을 제작하기 위한 언어라면
자바스크립트는 브라우저의 스크립트 엔진에서 동작하는 프로그램을 만들기 위한
언어이다.

또한 자바가 OOP를 얼마나 잘 사용하는지가 중요하다면
자바스크립트는 OOP에 비교적 불친절한 언어이다.

자바스크립트는 프로토 타입 체이닝을 통해 상위 함수를 끌어다 사용하는 형식이다.
어떻게 보면 메서드 오버로딩, 오버라이딩이 부족한 면모를 보인다.
(똑같은 이름의 함수를 다르게 작성하면 되지 않을까 생각할텐데, 함수 재정의가
아닌 같은 이름의 다른 함수를 만드는것이라 생각하면 좋다.)

심지어 ES6에 들어서야 Class 키워드를 사용할수 있었으나
private, public과 같은 필드는 여전히 지원이 잘 되지 않고
불안정한 모습을 보인다.

그리고 클래스 키워드의 실상은 역시나 프로토타입 체이닝을 이용한 신텍스슈가일 뿐이다.

개인적으로는 변수의 타입이 지정하지 않아도 될뿐만아니라
함수형 프로그래밍을 지향하고 있기때문에 굉장히 자유분방한 언어.

## 스코프

자바스크립트는 스코프가 특이하다.
지금은 잘 사용되지 않는 var키워드때문에 더욱 헷갈린적이 있다.

### 블럭 스코프

보통 다른 언어에서처럼 중괄호를 기준으로 변수 접근 여부가 결정된다.

### 펑션스코프

어떠한 함수 내부를 기준으로 접근이 가능하다. 따라서 var 키워드는
블럭 스코프라면 접근하지 못할 변수에 접근이 가능하다

때문에 간혹 var 키워드를 사용하여 접근해서는 안될 변수에 접근하는
문제가 발생하기도 한다.

이를 해결하기 위해 간혹 즉시실행 함수(IIFE)를 사용하기도 했다.

예제

```js
var x = 1;
{
  var x = 2;
}
console.log(x); // x를 재선언 하며 2로 다시 초기화 해버렸다.

let x = 1;
{
  let x = 2;
}
console.log(x); //1 블록 내부의 지역변수는 외부에서 접근이 불가능.
```

위처럼 var 키워드는 재선언이 가능한 모습도 같이 불수 있기 때문에
어지간 하면 let 키워드를, 만약 변수가 초기화 이후 변하지 않을거라면
const 키워드를 사용하는것이 좋다. (단 배열, 오브젝트등의 레퍼런스
타입은 const 키워드를 사용해도 영향이 없다.)

## 클로저

함수와 함수가 선언된 어휘적 환경의 조합. 자바스크립트 언어가 어떻게
사용가능한 변수의 범위를 지정하는지 이해해야 한다.

위에서 블록 스코프는 함수 외부에서 내부의 변수에 접근이 불가능하다고
언급했다. (지역변수는 함수가 종료되면 사라지기에!)

반대로 말하자면 어떤 함수 내부에서 새로운 함수를 실행시켜야 하는경우
외부 함수의 지역변수에 접근이 가능하다는 의미이다.

```js
function makeFunc() {
  var name = "Mozilla";
  function displayName() {
    alert(name);
  }
  displayName();
}
```

[MDN - 클로저](https://developer.mozilla.org/ko/docs/Web/JavaScript/Closures)의 예제이다.
위 코드를 실행시킨다면 브라우저에서 `Mozilla`라는 경고메세지가 출력된다.

사실 위 예제는 아래가 원본이다.

```js
function makeFunc() {
  var name = "Mozilla";
  function displayName() {
    alert(name);
  }
  return displayName;
}

//추가
let func = makeFunc();
func();
```

위 코드도 처음에 작성한 코드와 같은 기능을 한다.
해석하자면 makeFunc이 새로운 함수를 만들어 리턴하는걸
func 이라는 변수에 저장하고 실행한 것이다.

어떻게 보면 앞서 말했던 상황과 전혀 일치하지 않는다.
분명 함수가 종료되어 지역변수에 접근이 불가능 해야 한다.

하지만 리턴된 함수를 실행하면 지역변수에 접근이 되는듯하다.
이런 자바스크립트만의 "lexical"(어휘적 범위 지정(lexical scoping) 과정에서 변수가 어디에서
사용 가능한지 알기 위해 그 변수가 소스코드 내 어디에서 선언되었는지 고려한다는 것을 의미 - MDN)
를 이해하면 함수를 작성할때 어떤 변수들에 접근이 가능한지 이해하기 쉽다.

다시한번 말하지만, 이런 패턴을 보이는 환경의 조합을 클로저라고 한다.

이런 특징을 이용하여 자바스크립트의 class에서 캡슐화된 private 변수를 만들어낼수 있다.

또한 아래와 같은 코드도 클로져를 통해 가능하다.

```js
var greetCurried = function (greeting) {
  return function (name) {
    console.log(greeting + ", " + name);
  };
};

var greetHello = greetCurried("Hello");
greetHello("Heidi"); //"Hello, Heidi"
greetHello("Eddie"); //"Hello, Eddie"

// 출처 : https://www.sitepoint.com/currying-in-functional-javascript/
```

위와 같은 방식을 커링이라고 한다.

커링은 클로져를 여러 개의 인자를 가진 함수를 호출 할 경우,
파라미터의 수보다 적은 수의 파라미터를 인자로 받으면 누락된 파라미터를 인자로 받는 함수를 제작하는 방법이다.

## ES6

앞서 언급하지는 않았지만 자바스크립트는 ECMAscript의 표준 사항을 가장 잘 구현한 언어라고
알고 있다.
지금까지는 ES5라고해서 ECMAscript의 표준 5버전을 주소 사용해 왔다.

ES5의 특징은 다음과 같다.

- "use strict"
- String.trim()
- Array.isArray()
- Array.forEach()
- Array.map()
- Array.filter()
- Array.reduce()
- Array.reduceRight()
- Array.every()
- Array.some()
- Array.indexOf()
- Array.lastIndexOf()
- JSON.parse()
- JSON.stringify()
- Date.now()
- Property Getters and Setters
- New Object Property Methods

- Property access [ ] on strings
- Trailing commas in array and object literals
- Multiline string literals
- Reserved words as property names

그러나 이제는 ES6라고 해서 표준 버전 6이 2015년도에 등장하였고, 많이 사용된다.
주요 업데이트 사항은 다음과 같다.

- Class, let, const 키워드
- 화살표 함수
- Spread operator, Rest parameter, Default parameter
- Promise
- for ... of
- 리터럴 템플릿
- 모듈 임포트
- 구조분해 할당
- 기타 Number등의 메서드

### class

드디어 다른 언어에서 사용되는 클래스 키워드가 도입되었다.
하지만 여전히 public, private, protect등의 필드는 제대로 지원되지 않는다.
(매우 불안정하지만 존재하기는 한다.)

필드가 없기 때문에 생성자 내부에서 클래스가 가져야할 변수들을 선언하고 초기화 한다.

### 화살표 함수

익명함수를 사용할때 보다 간편하게 사용할수 있는 함수 선언 방법이다.

```js
// 기존
function tester() {
  console.log("test");
}

// 화살표 함수
let tester = () => console.log("test");
```

별도의 이름이 지정되지 않기때문에 변수에 함수를 저장하는 방식으로 사용하거나 한다.
주로 콜백함수에서 요긴하게 사용되는 방식이다.

다만 **스코프 문제가 발생한다.** 화살표 함수로 교체된 함수는 더이상 기존함수가
가리키던 this를 인식하지 못한다. 오브젝트의 메서드로 this를 바인딩 해줘야 하는
방법으로 해결해야 한다.

### Spread operator, Rest parameter, Default parameter

스프레드 오퍼레이터는 말 그대로 내용물을 펼치는것이라 이해하면 쉽다.

스프레드 문법은 주로 함수에 매개변수를 집어넣거나, 배열을 확장하거나 등의 용도로 사용한다.

```js
// MDN 예제들

function myFunction(x, y, z) {}
let args = [0, 1, 2];
myFunction(...args); // myFunction(0,1,2)와 같다.

let parts = ["shoulders", "knees"];
let lyrics = ["head", ...parts, "and", "toes"];
// ["head", "shoulders", "knees", "and", "toes"]

let obj1 = { foo: "bar", x: 42 };
let obj2 = { foo: "baz", y: 13 };

let clonedObj = { ...obj1 };
// Object { foo: "bar", x: 42 }

let mergedObj = { ...obj1, ...obj2 };
```

다만 스프레드 오퍼레이터는 순회 가능한 것들만 사용이 가능하다. 일반적인 객체는
iterable 선언을 해야 한다.

---

레스트 파라미터는 어떻게 보면 스프레드 오퍼레이터와 비슷하다.
하지만 사용서는 전혀 다른데..

**함수의 입력이 불규칙할 경우를 위해 사용한다.**

```js
// 역시나 MDN 예제
function myFun(a, b, ...manyMoreArgs) {
  console.log("a", a);
  console.log("b", b);
  console.log("manyMoreArgs", manyMoreArgs);
}

myFun("one", "two", "three", "four", "five", "six");

// Console Output:
// a, one
// b, two
// manyMoreArgs, [three, four, five, six]
```

위와 같이 myFun이라는 함수는 입력을 받을수 있는 파라미터의 갯수가 2가지였다.
하지만 manyMoreArgs라는 레스트 파라미터를 선언하여 추가적인 매개변수를
배열로 전부 받아온것을 확인할 수 있다.

> 이때 배열은 겉보기에는 배열처럼 보이지만 Array의 인스턴스다.

### 프로미스

프로미스는 말그대로 어떠한 비동기 요청의 실행을 약속한다. 다만
결과가 성공적인지는 보장하지 않늗다.

기존의 비동기 처리 방식은 콜백을 사용했다. 하지만 어느 콜백이
어느 부분을 처리하는지 알기 힘든 가독성이 떨어지는 **콜백헬** 현상이 발생하곤
하였다.

이런 가독성이 떨어지는 콜백헬 현상을 대체하기 위해 프로미스를 사용하는 추세이다.

**그렇다고 콜백이 사용되지 않는것은 아니다.**
**콜백은 프로미스가 이벤트를 처리하는 방법등으로 여전히 중요하게 사용된다.**

프로미스는 대기, 이행 거부 3단계로 나뉘며, then과 catch로 비동기 요청의 완료 혹은
오류에 대해 처리가 가능하다. 이때 콜백함수를 전달받아 실행하게 된다.

프로미스들은 프로미스를 리턴한다. 프로미스는 메서드 체이닝처럼 프로미스들을 체이닝
할수 있는데, 반환된 프로미스를 바탕으로 then, catch에서 비동기가 처리되는 형식이다.

```js
fetch('somURL',{some attributes}).then(response => response.json())
.then(data => console.log(data)).catch(err=> alert(err))
/*
어떤 주소로 요청을 보낸다. fetch 함수는 프로미스를 이용하기 때문에 then 혹은
catch로 요청 성공 및 에러를 핸들링한다.

2번째 then은 요청에 대한 응답을 전송받는경우 전송 데이터에 포함된 데이터를 추출하여 리턴한다.
3번째 then은 2번이 리턴한 프로미스에서 추출된 데이터를 콘솔에 출력한다.
만약 상위 과정에서 어떤 에러라도 발생한다면 then구문은 즉시 프로미스를 리턴한다.
대신 catch 구문에서 에러 프로미스를 넘겨받아 처리하게된다.
*/

```

### for ... of

기존의 for ... in이 오브젝트의 키를 바탕으로 순회를 하였다면
for .. of 구문은 오브젝트의 벨류를 대상으로 순회를 진행한다.

하지만 역시 순회 가능한(iterable)한 객체, 문자열들이 가능하다.
일반 오브젝트로 사용한다면 iterable하게 만들어주는 작업이 필요하다.

### 리터럴 템플릿

리터럴 템플릿은 문자열 속에서 간편에서 변수의 값을 집어넣는 방법이다.

따옴표류를 사용하지 않고 백틱(`)을 사용하여 문자열을 작성한다.
또한 표현하고자 하는 변수를 ${}를 이용해 명시하면 된다.

```js
let value = "50";
let text = `철수의 나이는 ${value}이다`;

console.log(text); //철수의 나이는 50이다.
```

### 모듈 임포트

require를 사용하지 않는다.
ES6에서는 `imort ... as ... from ...`를 사용한다.

우선 복수 객체를 내보내고, 임포트가 가능하다. 또한 후술할 구조 분해할당
을 이용하여 원하는것만 임포트하여 사용할 수 있다. 또한 as 뒤에
원하는 별명을 붙여 해당 별명을 통해 임포트한 것에 접근이 가능하다.

기존의 방식인 commonjs와 비교하여 필요한 부분만 가져올수 있기 때문에
좀더 효율적인 메모리 사용이 가능하다.

### 구조분해 할당

배열이나 객체에서 일일히 변수를 선언하고 값을 할당하는 방식을 대체하는 방법이다.

```js
// MDN 예제
let a, b, rest;
[a, b] = [10, 20];
console.log(a); // 10
console.log(b); // 20

[a, b, ...rest] = [10, 20, 30, 40, 50];
console.log(a); // 10
console.log(b); // 20
console.log(rest); // [30, 40, 50]

({ a, b } = { a: 10, b: 20 });
console.log(a); // 10
console.log(b); // 20

// Stage 4(finished) proposal
({ a, b, ...rest } = { a: 10, b: 20, c: 30, d: 40 });
console.log(a); // 10
console.log(b); // 20
console.log(rest); // {c: 30, d: 40}
```
