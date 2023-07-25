---
title: "flutter - 라우트"
toc: true
toc_sticky: true

tags:
  - dart
  - flutter

last_modified_at: 2023-07-25T13:53:00+00:00
---

# Navigator

플러터에서 페이지를 이동하려면 Navigator를 사용해야 한다.

Navigator.of(context)를 통해 가장 가까운 빌드 컨텍스트에서 네비게이터를 반환 받을 수 있다.
이 반환받은 네비게이터를 통해 페이지를 이동할 수 있다.

## push

새로운 페이지를 스택에 쌓는다.

```dart
Navigator.of(context).push(
  MaterialPageRoute(
    builder: (BuildContext context) => NextPage(),
  ),
);
```

## pop

스택에서 페이지를 제거한다.

```dart
Navigator.of(context).pop();
```

## pushNamed

새로운 페이지를 스택에 쌓는다.

다만 이전에 라우트에 이름을 지정해야 한다.

```dart
MaterialApp(
  routes: {
    '/next': (BuildContext context) => NextPage(),
  },
);

---
...
Navigator.of(context).pushNamed('/next');
```

## popAndPushNamed

스택에서 페이지를 제거하고 새로운 페이지를 스택에 쌓는다.

다만 이전에 라우트에 이름을 지정해야 한다.

```dart
MaterialApp(
  routes: {
    '/next': (BuildContext context) => NextPage(),
  },
);

---
...
Navigator.of(context).popAndPushNamed('/next');
```

## pushReplacement

스택에서 페이지를 제거하고 새로운 페이지를 스택에 쌓는다.

```dart
Navigator.of(context).pushReplacement(
  MaterialPageRoute(
    builder: (BuildContext context) => NextPage(),
  ),
);
```

## pushReplacementNamed

스택에서 페이지를 제거하고 새로운 페이지를 스택에 쌓는다.

다만 이전에 라우트에 이름을 지정해야 한다.

```dart
MaterialApp(
  routes: {
    '/next': (BuildContext context) => NextPage(),
  },
);

---
...
Navigator.of(context).pushReplacementNamed('/next');
```

## pushNamedAndRemoveUntil

스택에서 페이지를 제거하고 새로운 페이지를 스택에 쌓는건 동일하다.
하지만 파라미터로 받는 predicate를 만족할 때까지 스택에서 페이지를 제거한 다음 새로운 페이지를 스택에 쌓는다.

```dart
MaterialApp(
  routes: {
    '/next': (BuildContext context) => NextPage(),
  },
);

---
...
Navigator.of(context).pushNamedAndRemoveUntil(
  '/next',
  (Route<dynamic> route) => false,
);
```

## pushAndRemoveUntil

pushNamedAndRemoveUntil 와 비슷하다. routeName을 받지 않는점이 다르다.

```dart

Navigator.of(context).pushAndRemoveUntil(
  MaterialPageRoute(
    builder: (BuildContext context) => NextPage(),
  ),
  (Route<dynamic> route) => false,
);
```

## popUntil

스택에서 조건에 맞을때 까지 페이지를 제거한다.

```dart
Navigator.of(context).popUntil(
  (Route<dynamic> route) => false,
);
```

## canPop

스택에 페이지가 존재하는지 확인한다.
주로 pop하지 직전에 확인하여 pop을 할지 말지 결정한다.

간혹 스택에 페이지가 존재하지 않을때 pop을 하면 에러가 발생한다.

```dart
if(Navigator.of(context).canPop()){
  ....
}
```

# Navigator.<>(context)

Navigator.of(context).<> 와 똑같다.

### 주의 사항

플러터 공식 문서에는 다음과 같은 설명이 존재한다.

> named routes 는 딮링크를 사용할 수 있지만, 그 동작방식은 navigator 그 자체이기 때문에
> 딮링크를 받으면 유저가 어느 페이지에 있던 상관없이 해당 페이지로 이동하게 될 것이다.

> 또한 플러터는 브라우저에서 어플리케이션으로 이동할때 named routes 를 사용할 수 없다.
> 이런 이유로 인해 named routes 는 대부분 어플리케이션에서 사용하지 않는걸 추천한다.

## 딮링크

딮링크는 모바일 환경에 놓인 유저가 웹사이트 혹은 다른 앱에서 URL을 클릭했을 때, 앱을 실행시키고 특정 페이지로 이동하도록 돕는 기술 이라고 한다. 그니까 구글에서 쿠팡 검색결과를 클릭하면

1. 쿠팡앱이 켜지면서 해당 검색페이지를 보는 것
2. 쿠팡앱이 없으면 설치 페이지로 이동되는것

이 바로 딮링크를 사용한 예제이다.

기존에는 URI 스킴이라는 것을 사용해서 `app://...`과 같은 링크를 사용했지만,
점차 동일한 이름을 사용하는 앱들이 많아지면서 충돌이 발생하게 되었다.

그 결과 딮링크를 사용하기 위해 URI 스킴 대신 소유권을 증명할 수 있는 도메인을 사용하기 시작했다.
그래서 현재는 `https://example.com/...`과 같은 링크를 사용하게 되었다.

찾아보니 안드로이드 및 IOS 진영에서 각각 도메인을 등록하고 검증하는 방법을 제공해 주고 있다고 한다.
