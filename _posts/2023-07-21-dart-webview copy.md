---
title: "flutter - 웹뷰"
toc: true
toc_sticky: true

tags:
  - dart

last_modified_at: 2023-07-21T16:05:00+00:00
---

# 웹뷰

웹뷰란 모바일 어플리케이션에서 웹 콘텐츠를 보여주기 위해 임베디드된 웹 브라우저이다.

웹뷰를 사용함으로써 웹 컨텐츠를 보여줄수 있다는 장점이 있긴하지만
개발할때 웹뷰에서만 생각치 못한 이슈를 마주치는 등 개발적인 이슈가 존재했던걸로 기억한다.

물론 확인이 필요하겠지만, 개인적으로는 웹뷰 !== 웹브라우저 일수도 있다는 것이다.

# 플러터에서 웹뷰 사용하기

[pub get]("https://pub.dev/packages/webview_flutter")에서 문서를 참고한다.
4.0 버전 기준으로 작성해 두었다.

기본적인 사용방법은 다음과 같다.

```
// main.dart
import 'package:web_view/screen/home_screen.dart';
...

// 웹뷰를 사용할 위젯
import 'package:flutter/material.dart';
import 'package:webview_flutter/webview_flutter.dart';

final homeUrl = Uri.parse('url 주소');

class HomeScreen extends StatelessWidget {
  WebViewController controller = WebViewController()
    ..setJavaScriptMode(JavaScriptMode.unrestricted)
    ..loadRequest(homeUrl);

  HomeScreen({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        backgroundColor: Colors.orange,
        title: ...,
        centerTitle: true,
        actions: [
          IconButton(
            onPressed: () {
              controller.loadRequest(homeUrl);
            },
            icon: const Icon(
              Icons.home,
            ),
          ),
        ],
      ),
      body: WebViewWidget(
        controller: controller,
      ),
    );
  }
}
```

위와 같이 웹뷰도 위젯으로 사용할 수 있다.
즉 ui에서 원하는 영역에서만 사용이 가능하다.
다만 웹뷰 컨트롤러와 위젯을 다음과 같이 선언해 주어야 한다.

## 자바스크립트 허용하기

해당 패키지는 기본적으로 자바스크립트 사용설정이 되어 있지 않다.
그러므로 자바스크립트를 사용하기위해서는 위 코드와 같이
`..setJavaScriptMode(JavaScriptMode.unrestricted)`
함수 호출을 통해 자바스크립트 활성화를 해주어야 한다.

## 주소 설정하기

주소를 넘겨줄때 주의할점은 loadRequest 함수는 Uri객체를 필요로 한다는 점이다.
그렇기 때문에 string 타입을 딸랑 넘겨주면 오류를 뿜뿜하게 된다.
얌전히 Uri.parse를 통해 Uri 객체를 생성한뒤 파라미터로 넘겨주자.
