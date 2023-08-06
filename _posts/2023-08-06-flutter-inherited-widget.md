---
title: "flutter -inherited widget"
toc: true
toc_sticky: true

tags:
  - dart
  - flutter

date: 2023-08-06
last_modified_at: 2023-08-06T13:54:00+00:00
---

# Inherited Widget

플러터의 상태관리는 리액트와 비슷한 방식으로 상태를 관리한다.
프롭스를 통해 상태를 전달하고, 상태가 변경되면 리렌더링을 한다.

그렇기 때문에 최하위 컴포넌트에서도 먼 조상의 상태를 사용할 수 있긴 하지만,
그 조상의 상태가 변경되면 그 상태를 사용하는 모든 컴포넌트가 리렌더링 되어야 한다.

패키지의 크기가 작다면 그렇게 신경쓸 문제는 아니겠지만, 패키지의 크기가 커지고 조상과컴포넌틔 사이에
위젯이 많아지기 시작한다면 이야기는 달라진다.

언제 사용되지도 모를 상태를 전달받아서 또 전달해야 하기 때문에, props 관리가 힘들어질것이다.

이때 다양한 상태관리 툴이 있지만 기본적으로 사용할 수 있는 것이 Inherited Widget이다.

## 사실 많이 보았던 것

Inherited Widget은 사실 많이 보았던 것이다.
app.Theme.of(context), MediQuery.of(context), Navigator.of(context) 등등
이 모두 Inherited Widget을 사용하고 있다.

## Inherited Widget의 특징

Inherited Widget은 buildContext를 통해 상태에 접근하고 수정할 수 있다.
하지만 buildContext의 특성상 .of를 통해 상위 buildContext에 해당상태를 접근할 수 있어야 한다.

즉 하나의 위젯 내부에서 Inherent Widget을 선언라고 .of로 접근하려면 에러가 발생한다는 의미.

## Inherited Widget의 사용법

먼저 구현하는 방식을 살펴보자.
[공식문서](https://api.flutter.dev/flutter/widgets/InheritedWidget-class.html)코드를 보면

```dart
class FrogColor extends InheritedWidget {
  const FrogColor({
    super.key,
    required this.color,
    required super.child,
  });

  final Color color;

  static FrogColor? maybeOf(BuildContext context) {
    return context.dependOnInheritedWidgetOfExactType<FrogColor>();
  }

  static FrogColor of(BuildContext context) {
    final FrogColor? result = maybeOf(context);
    assert(result != null, 'No FrogColor found in context');
    return result!;
  }

  @override
  bool updateShouldNotify(FrogColor oldWidget) => color != oldWidget.color;
}
```

1. InheritedWidget을 상속받는다.
2. 생성자를 만든다. 생성자에는 반드시 super.child를 받아야 한다.
3. static으로 of(필수), maybeOf(선택)를 구현한다.
4. updateShouldNotify를 구현한다. 해당 함수는 상태가 변경되었으면 이 상태에 의존하고 있는 위젯들에게
   알려주는 역할을 한다. 그러므로 상태가 변경되었을 때만 true를 반환하도록 구현해야 한다.

그리고 나서 공통적으로 사용할 위젯의 부모위젯에서 child 위젯을 InheritedWidget으로 감싸준다.

```dart
class FrogColorApp extends StatefulWidget {
  const FrogColorApp({
    required this.color,
    required this.child,
  });

  final Color color;
  final Widget child;

  @override
  _FrogColorAppState createState() => _FrogColorAppState();
}

class _FrogColorAppState extends State<FrogColorApp> {
  late Color color;

  @override
  void initState() {
    super.initState();
    color = widget.color;
  }

  void changeColor() {
    setState(() {
      color = color == Colors.green ? Colors.blue : Colors.green;
    });
  }

  @override
  Widget build(BuildContext context) {
    return FrogColor(
      color: color,
      child: widget.child,
    );
  }
}
```

그럼 하위 위젯에서는 of를 통해 상태에 접근할 수 있다.

```dart
class Frog extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final FrogColor frogColor = FrogColor.of(context);
    return Container(
      color: frogColor.color,
      child: Text('Hello World!'),
    );
  }
}
```

## Provider 패키지

근데 Provider 패키지를 알고 난 다음에 Inherited Widget을 알게 되었는데,
이거 둘이 비슷한 것 같다.

심지어 스택오버플로우를 보니 [답변중에 이런것](https://stackoverflow.com/a/57158684)도 있다.

```
Provider is not a must, but should.

First of all, it's promoted by Flutter Team and flexible enough to handle almost any state-management solution.
...
```

inheritWidget의 class 구현, 하위 위젯 전체 리빌드 vs 특정 위젯 리빌드의 특징 차이가 있다고 한다.
개인적으로도

1. 불필요한 위젯 리빌드를 막고 필요한 위젯에서만 리빌드가 가능하다.
2. of, updateShouldNotify 메서드를 구현하지 않고 필요한 기능만 구현하면 된다.
3. 두가지 사용 방법이 매우유사하다.

라는 점에서 provider를 사용할것 같다.
