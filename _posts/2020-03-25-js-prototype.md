---
title: "자바스크립트 prototype, class"
toc: true
toc_sticky: true

categories:
  - Javascript
tags:
  - basic
  - prototype
last_modified_at: 2020-03-25T17:56:00+00:00
---

**매우 주관적인 포스트로서 사실과 다를수도 있습니다. 개인적인 정리 차원입니다.**

후... 한번썼던건데 뻑나서 다시써야한다.

## prototype​

자바스크립트에는 특이한 점이 있다.
​

1. 모든것은 함수로 이루어져있다. (객체도 함수로 실행되어 만들어 진다.)
2. 어떤 함수는 브로슈어 같은 prototype 객체를 가질수 있다.
3. 모든 함수는 **proto**를 가질수 있다.
   ​
  1 번은 말 그대로이다. 자바 스크립트는 함수들로 이루어져 있다고 한다.
  즉 객체도 2번,3번에 해당될수도 있다는 것이다.
  ​
  2번 모든 함수는 prototype을 가질수 있다.
  무슨뜻 인가하면 기반이 되는 객체들(대표적으로 생성자로 만들어진 객체)은 prototype을 가지게 된다.
  하지만 어떤 객체를 기반으로 새로이 만들어진 객체는 이걸 가질수 없다.
  ​
  여기서 생성자란? 함수이면서 내부적으로 객체의 메서드나 여러 프로퍼티들을 만들어 주는 함수를 말한다.
  즉 let func = new someFunciton() 이런식으로 객체를 만들어주는 함수들. 특징으로는 new를 반드시 작성해줘야 된다.
  new 가 없으면 생성자는 undefined를 리턴하고, new가 있으면 this를 넘겨주어 객체가 this를 사용할수 있게 해준다.
  ​
  묵시적으로 언급이 없으면 this를 반한하되, 프로그래머가 명시하면 다른것을 반환해준다.
  ​
  2번의 예를 들어보자면 자바스크립트에서 변수를 toString으로 형변환이 가능하다.
  하지만 변수자체에는 toString을 선언하고, 구현하지 않았다.
  ​
  이유는 우선

4. 모든 변수, 객체들은 Object가 기반으로 만들어 지고 있으며(String도 역시 Object 기반, Object도 어떤것을 기반으로 만들어짐)
5. 모든 변수, 객체들은 자신이 가지고 있지 않은것은 상위 객체로 올라가면서 찾아볼수 있다.
  이유는 아래에 정리해보자.
6. 모든 함수들은 **proto**를 가진다.
  이건 최상위 노드도 null값 이지만 가지고 있다. 자신의 기반인 객체의 prototype을 가리키고 있다.
  만약 아무것도 기반으로 하지 않고
  새로운 함수인 func를 만들게 되면, func는 Object의 prototype 객체를 **proto**로 가리키고 있다.
  ​
  후술하겠지만 이 **proto**는 상위 객체가 뭘 가지고 있는지 알려주는 역활이다.
   ​

### prototype vs **proto**

우선 prototype은 뭔지 정리해보자
prototype은 어떤 객체가 생성자로 인해서 생성이 되면 생겨나는 객체인것 같다. 아니 new 키워드만 사용해도 된다.
let obj ={a:2}; 이렇게 선언을 해보면 obj에 prototype은 존재하지 않는다.
​
하지만 let func = Func(){ ... } 이렇게 만들고 나서 obj = new func() 이렇게 작성하면 func에 prototype이 생겨난다.
그러므로 어떤 객체가 new 키워드로 인해 다른 변수에 저장이 되는 순간 그 객체를 기반으로
새로운 객체가 생성이 된것이기 때문에, prototype이 생겨나게 된다.
​
prototype은 지금까지 공부한것을 적어보자면, 이렇게 어떤 객체를 기반으로 객체가 만들어 질때 생겨나는 객체이며,
기반이 되는 객체의 프로퍼티들과, 함수목록을 적어놓고 있는 객체이다.(생성자도 같이 있음)
(아무래도 functional 코드 처럼 하지 않고 function shared처럼 같은 코드는 주소만 알려주게 하여 메모리를 절약하는 용도이지 않을까?)
​
그리고 **proto**는 저 prototype을 가리키고 있다. (하위 객체에서 상위 객체의 함수, 변수같은 프로퍼티를 들여다 보고 있다)
그래서 하위 객체가 자신이 없는 함수나 변수를 이용하고자 한다면, **proto**를 이용하여 상위객체의
함수, 변수 등등을 들여다 보게 된다. 그 상위 개체도 찾고자 하는 것이 없다면 **proto**를 이용하여 더 상위 객체의 것을 들여다 본다
이런 특성을 프로토타입 체인이라고 한다.
​
다만 프로토타입 체인은 가장 먼저 발견되는 변수나 함수를 이용한다. 만약에 같은 변수가 하위객체에 있고, 자신도 가지고 있는경우에는
하위객체의 것을 이용하고 자신의 것은 이용되지 않는다.(가려진다고 한다.)
​
보다 싶이 String으로 새로운 객체인 string을 만들어 줬다. string의 **proto**는 기반이된 String.prototype을 가리키고 있다.
​

### constructor 생성자

생성자는 위에서 new 키워드로 새로운 객체를 만들어 낼때 뿐만 아니라, 그 함수가 불리는 순간 호출된다.
하지만 new 키워드가 없다면 새로운 객체는 생성되지 않는다.
​
생성자는 new 키워드가 있으면 프로그래머가 this를 적지 않았어도 this를 반환해 주어 객체가 this를 이용 가능하게 해준다.
new 가 없으면 undefined를 넘겨준다. 언급이 있다면 this말고 다른것도 넘겨줄수 있다.
​
나는 보통 이런 형식을 많이 봐왔다.

```js
let foo = function(val){ ....... }
....
let someObj = new foo(value);
...
```

그러면 someObj는 **proto**로 foo의 prototype 객체를 가리키게 된다.
궁금증: 아마 이때 prototype 객체가 만들어 지지 않을까? 그리고 prototype 객체는 foo에 저장되어 있지 않을까? 이경우에는 constructor가 함수를 가리키고 있다고 봐야 할까? -> 그렇게 볼수 있다.
​
아무튼 이 생성자를 이용해서 새로운 객체를 만들어 주게 된다.
​

### Object.create()

Object.create() 함수는 파라미터로 어떤 객체를 받는다.
​
그리고 그 객체의 프로퍼티(값은 말고)를 복사하여 새로운 객체를 리턴한다.
그러니까 객체들의 키는 유지 된다. 하지만 생성자는 실행되지 않아 생성자로 여러 변수를조작하는 경우에
문제가 될수 있다. 또한 new 키워드 보다 느리다.
​
이 방법으로 프로토타입을 복사하여 저장하는 방법은 일종의 상속이다.
이 Object.create()는 프로토타입을 복사하여 다른 프로토 타입에 저장하는 경우, 복사한 객체의 **proto**에서 이 프로토 타입을 참조하게 된다.
​
따라서, 해당 객체는 자신의 고유한 함수, 변수 뿐만 아니라, 상속받은 객체의 것 까지 사용이 가능하다.
일종의 부모 자식 관계가 형성 되었다고 볼수 있다.
(정정내용: 상속받은 인스턴스의 변수 등을 활용하려면, call, apply등으로 컨텍스트 공유가 필요하다)
​
그러니까, 상속을 사용하는데, pseudo classcial한 방법으로 하고 싶은 경우에는 이 방법을 쓰자.
​
단, prototype을 복사하여 상속을 한경우에는 생성자가 존재하지 않는다.
생성자는 항상 존재 한다. 아래 let student= .()... 함수가 생성자가 된다. 다만 상식적으로 생성자와 인스턴스가 연결이 되지 않는 부분이 있다.

```js
let person = function (value) {
  this.name = value;
};

let student = function (value) {
  this.grade = value;
};
/* 정정한다. student에서 person의 name을 사용하고 싶은 경우에는 call, apply를 this로 넘겨줘야 사용가능하다.
 student 내부에서 person.call(this, someValue); 를 추가로 작성해야 하며, someVlaue 는 인자로 받아야 한다
*/
//student.prototype = Object.create(person.prototype) 여기
//student.prototype.constructor === null //typeError;

let kim = new person();
let me = new student();
//student.prototype = Object.create(person.prototype) 혹은 여기서 해도 됨
```

그러니, 생성자를 자신으로 추가하여 진짜 상속을 완료하자. 안그러면 student를 호출하면 person으로 만들어진다.

이뜻은 뭔가 하면, 상식적으로 student로 만들어진 새로운 인스턴스는 student를 기반으로 만들어져 있다고 생각을 하게 될텐데, 인스턴스.prototype.constructor 는 student가 아닌 person을 가리키고 있다는 뜻이다.

나중에 생성자를 이용해야 하는 경우에 문제가 생길수 있다. 또한 상식적으로 말이 되지 않는 부분이다.

student.prototype.constructor = student;

## Class (ES6)

클래스는 ES6에서 추가되었다.
​
C++에서 배운 클래스와 유사하다.
단, public, private는 다른 방식으로 지정해야 한다. (ES2019 에서 prefix 인 #을 사용하면 private 처럼 사용이 가능하나, 불안정 한것 같다.)

```js
class name(){
  constructor(){  //여기서 클래스의 변수를 초기화 한다.
  this.value;
  }

  somefunction(){
  }

  somefunction(){

  }
}
```

여기에는 보다싶이 private, public이 없다. 대신에 생성자 안에서 여러 변수를 초기화 하거나, 여러 작업을 실행할 수 있다.
그러므로, private한 변수 등등이 필요하다면 생성자 안에서 this를 이용하여 사용하면 된다. (캡슐화)
​
만약 클래스의 초기값을 파라미터로 넘겨줄 경우에는 new name(val)을 선언하고
생성사에도 이 값을 넘겨주면 된다.

```js
class name(val){
  constructor(val){
    this.value = val;

// 이 클래스의 상속은 매우 간단하다. 하지만 아직 여러 클래스를 동시 상속하는 방법은 모른다.
​
// extends로 간단하게 상속이 가능하다.

class person extends name (){
  constuctor(){
    super()
....
}
```

대신, 부모 클래스의 생성자를 호출해야하는 경우에는 자신의 생성자 앞에 super()를 사용한다. 파라미터가 필요한 경우에는
인자들을 넘겨주면 된다. this는 부모클래스가 가질수 있는것을 전부 가지고 있다.
​
그리고 이 클래스에서 부모 함수를 덮어씌워(오버로딩) 새롭게 자신의 함수를 만들 수 있다.
​
그리고 this로 이 함수를 불러 올 수 있지만, super.function()으로 불러 오는것이 좋다 한다.
직접보니, this를 이용한 경우 콜스택 오버플로가 일어나기도 했다.
​
내생각에는, super를 이용하여 부모 클래스의 함수를 구분하여 사용하는 것으로 판단된다.
