---
title: "flutter - 스크롤"
toc: true
toc_sticky: true

tags:
  - dart
  - flutter

last_modified_at: 2023-07-31T16:40:00+00:00
---

# 안내사항

인프런 강의를 들으면서 / 작업을 진행하면서 알게되는 스크롤링 관련 지식을 작성중

# 스크롤링

플러터 앱에서 스크롤링을 작성하려면 웹 프로그래밍과 꽤 다르다.

웸에서는 일부 속성으로 인한 경우를 제외하고 스크롤링은 그냥 할 수 있는 편이라고 생각한다.

하지만 플러터는 그렇지가 않았다.

## 시행착오(오버플로우 에러)

- 아무것도 모를때는 웹에서처럼 화면을 넘기면 스크롤이 생기는줄 알았다.
  하지만 막상 앱을 실행해 보면, 디버그 출력창 아래에 몇 픽셀이 넘어가 있다고 에러가 나온다.
- Column 위젯 안에 Expanded 위젯을 넣으면 height가 정해지지 않았다고 에러가 나온다.

## 팩트

- 플러터는 스크롤 가능한 영역을 지정해주고 그 안에 위젯을 배치해야 스크롤이 가능하다.
- column, flex 위젯 등 일부 레이아웃 관련 위젯은 height가 명시되지 않는다.
  혹시라도 위 위젯 안에서 expanded등 일부 위젯을 사용하는데, column위젯의 크기만큼 사용하고자 한다면 IntrinsicHeight위젯을 활용하던가 해야 한다.

  (Row, flex 위젯은 비슷하게 가로 길이가 정해지지 않을듯 하다. IntrinsicWidth 위젯 활용해야 할듯)

## 추축

- Scrollable 내부의 위젯은 미리 렌더링을 하지 않는다.
  ListView 위젯으로 확인해보니 builder 함수에서 print 함수로 index를 출력시켜 보면 스크롤이 내려가면서 위젯이
  렌더링 되어야 하는순간에 print 함수가 호출된다.

## ListView 위젯

자주 사용하는 스크롤 관련 위젯이라고 한다.
children을 넘겨주는 방식 외 2가지 방식이 존재한다.

- ListView.builder
  반드시 위젯을 리턴해야 하는 itemBuilder(required BuildContext context, required int index) 함수를 반드시 필요로 한다.
  또한 itemCount로 itemBuilder 함수를 몇번 실행할지?(아님 위젯을 그릴지?) 결정할 수도 있다.

- ListView.separated
  위 builder itemBuilder 함수를 받는것은 동일하다. 하지만 itemBuilder가 렌더링 하는 위젯 사이사이에 Divider와 같은 위젯을 렌더링 해야 하는 메서드이므로, itemBuilder와 같은 파라미터를 필요로 하는 separatorBuilder함수를 반드시 필요로 하며, 몇개의 위젯을 그려하 하는지 결정하는 itemCount 파라미터
  총 3개의 파라미터가 필수 파라미터이다.
