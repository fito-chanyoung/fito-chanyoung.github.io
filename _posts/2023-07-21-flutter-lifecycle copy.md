---
title: "flutter - stateless & sateful 위젯"
toc: true
toc_sticky: true

tags:
  - dart
  - flutter

last_modified_at: 2023-07-21T17:45:00+00:00
---

# stateless & stateful widget

플러터 위젯은 두가지로 나눌 수 있다.

하나는 stateless widget,
다른하나는 stateful widget

둘을 구분하는 가장 좋은 방법은 개인적으로는
부모 위젯에게서 받은 데이터를 가지고 화면을 구성하기만 하는가
아니면 자신이 어떤 데이터를 관리하고 변경해야 하는거 정도인듯 하다.

그리고 코드 짜기 나름이지만, 필요하다면 상태관리를 하거나 하지 않거나로 변경할 수 있다.
물론 stateless, stateful여부를 바꾼다는것은 처음에 코드를 잘못 짠것이므로, 애초에 그렇게 되지 않게 해야겠지만..

## state

플러터에서 상태라는것은 다음과 같다.

1. 위젯에서 ui를 구성하는데 사용되면서 변경이 가능한 변수
2. 외부 서버에 존재하는 데이터(플러터의 일반적인 변수는 build 함수가 호출될때 타입등이 확정되어 있어야 한다.)

## stateless 위젯

stateless 위젯은 단순하다.

생성자로 넘겨받은 데이터(없어도 됨)를 이용해서 화면을 구성하기만 하면 stateless위젯이라고 부른다.
거의 생성자와 build 함수만 사용한다.

그럼 부모위젯에서 state가 넘어오고 그걸로 화면을 구성한다면 stateless가 아니라 stateful이지 않을까 싶지만,
전혀 아니다. 본인 위젯에서 관리하는 state가 없고 부모에 의존한다면 stateless 위젯이다.

## stateful 위젯

요 위젯은 좀 다른데, state를 관리하는 클래스와 위젯클래스로 나뉘어져 있다.
다트 프로젝트의 main.dart(변경하지 않았다면) 파일이 그 예시다.
위젯 클래스는 createState 함수를 오버라이딩 하여 state 클래스 인스턴스를 생성한다.
state 클래스는 stateless 위젯과 비슷하게 ui를 구성하는 코드를 가지고 있다.
다만, state 클래스는 ui 구성에 영향을 미치는 변수들도 가지고 있다.

그 말인즉, 플러터가 언제 화면을 다시 구성해야 할지 영향을 미칠 수 있는 위젯이라는 것이다. (중요)

왜 이런 구조를 가지고 있는가 문서를 살펴보면

> 성능 때문이다. 플러터에서는 필요할때마다 위젯을 폐기하고 재구성하는데, 이는 그렇게 부담을 주지 않는 작업이다.
> 하지만 상태(state)를 위젯처럼 필요할때마다 재구성한다면, 연산능력이 매우 많이 필요하다.
>  또한 state를 오랫동안유지한다면, 매 프레임마다 state 객체에 접근이 가능하며, 이는 플러터에서 애니메이션이 존재할 수 있는 이유 이다.
> 
> [원문](https://flutterbyexample.com/lesson/stateful-widget-lifecycle)

그렇기에 위젯과 상태 클래스가 분화 되어 있다.
즉 상태가 변경되면 위젯 인스턴스는 다시 만들어 지고,
상태 인스턴스는 유지된다는 의미.

## LifeCycle

stateful 위젯은 상태를 관리하고 있는 만큼 여러 위젯이 생성되고 폐기될때 까지 상황별로 실행될 메서드들을 가지고 있다.

위젯은
생성자 => createState => initState => didChangeDependencies => build
순서로 함수가 호출되고
deactivate => dispose
순서로 위젯이 폐기된다.

만약 상태가 변경되어서 위젯을 재구성 해야 한다면
라이프 사이클은 처음과 다르게 생성자 => createState => didUpdatedWidget => build 순으로 진행되어 위젯이 만들어 진다.

### createState

stateful 위젯 클래스를 구성해야 한다면 반드시 호출되는 함수 이다.
stateful 위젯이라 선언했다면 클래스 내부에 반드시 가지고 있어야 한다.

```
class Root extends StatefulWidget {
  const Root({Key? key}) : super(key: key);

  @override
  _RootState createState() => _RootState();
}
```

### initState

stateful 위젯이 생성될때 단 한번만 생성된다고 한다.
그런데 state 클래스 생성자가 호출된 이후 호출된다고 보는게 맞을듯 하다.
어쨋든 위젯이 생성될 때 단 한번만 실행된다.

### didChangeDependencies

initState 이후 실행된다.
그리고 클래스가 의존하는 데이터가 변경된다면 이 함수가 실행된다고 한다.
반드시 super를 호출해야 한다.

### build

didChangeDependencies, didUpdateWidget 이후 실행된다.
가장 많이(필수라서) 사용되는 라이프사이클 메서드.

반드시 위젯을 리턴시켜줘야 한다.

### didUpdateWidget & setState

부모 위젯 혹은 데이터가 변경이 생겨 위젯을 다시 렌더링해야 할때라면 didUpdateWidget이 호출된다.
주의 할 점은 state 객체는 유지된 상태에서 위젯 객체가 다시 만들어 지는 경우에 호출된다는 것이다.

didUpdateWidget 메서드는 이전 위젯 정보를 파라미터로 받는다.

stateful 위젯의 상태를 변경하려면 setState 함수를 호춣하면 된다.
setState 함수는 VoidCallback, 즉 파라미터가 없는 함수만 파라미터로 넘겨줄 수 있다.
setState는 build함수의 호출을 야기한다.

**절대 didUpdateWidget 함수 내부에서 setState함수를 호출하지 말것**
**setState => build => didUpdateWidget => setState... 무한 호출이 발생한다**

### deactivated

flutter 3개월동안 만져봤는데 이제서야 있는걸 처음 알았다. 아래 dispose를 주로 쓰고 이건 안쓴다.
상태 객체가 트리에서 제거된 이후 다시 삽입되거나 다른 트리로 이동할 수 있기때문에 존재한다고 한다.

### dispose

state 객체가 영원히 제거된 경우 호출된다.
