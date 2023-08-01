---
title: "flutter - 스크롤"
toc: true
toc_sticky: true

tags:
  - dart
  - flutter

date: 2023-07-31
last_modified_at: 2023-08-01T14:44:00+00:00
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
- Column 위젯 안에 Expanded 위젯을 넣으면 size 정해지지 않았다고 에러가 나온다.

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
  렌더링 되는 위젯은 스크롤이 이동하면서 위젯이 그려져야 할 순간에 렌더링 된다.

- ListView.separated
  위 builder itemBuilder 함수를 받는것은 동일하다. 하지만 separatorBuilder함수가 렌더링 하는 위젯 사이사이에 Divider와 같은 위젯을 렌더링 해야 하는 메서드이므로, itemBuilder와 같은 파라미터를 필요로 하는 separatorBuilder함수를 반드시 필요로 하며, 몇개의 위젯을 그려하 하는지 결정하는 itemCount 파라미터
  총 3개의 파라미터가 필수 파라미터이다.

  ListView.builder와 마찬가지로 렌더링 되는 위젯은 스크롤이 이동하면서 위젯이 그려져야 할 순간에 렌더링 된다.

  중간중간 배너를 넣어야 할때 유용할듯 하다.

## SingleChildeScrollView 위젯

하나의 child 위젯을 받아서 스크롤이 가능하다면 스크롤이 가능한 위젯이다.
그뜻은 child의 길이가 화면을 넘어서지 않는다면 스크롤 에니메이션이 작동하지 않는다는 뜻이다.

리스트뷰와 다르게 모든 **하위 위젯을 한번에 렌더링** 된다는 점에서 차이가 있다.
성능저하가 발생할 수도 있다는 뜻.

### parameter: physics

만약 화면을 넘어서지 않아도 스크롤 에니메이션이 작동하길 원한다면, physics 파라미터에 AlwaysScrollableScrollPhysics()를 넘겨주면 된다.

만약 스크롤을 막길 원한다면
NeverScrollableScrollPhysics()를 넘겨주면 된다.

만약 IOS의 스크롤 에니메이션을 원한다면
BouncingScrollPhysics()를 넘겨주면 된다.

만약 안드로이드의 스크롤 에니메이션을 원한다면
ClampingScrollPhysics()를 넘겨주면 된다. (완전히 같지는 않다.)

### parameter: clipBehavior

- none: 스크롤 영역을 벗어나도 그대로 보여준다.
- hardEdge: 스크롤 영역을 벗어나면 잘라낸다.
- antiAlias: 스크롤 영역을 벗어나면 잘라낸다. 하지만 잘라낸 부분을 부드럽게 처리한다.
- antiAliasWithSaveLayer: antiAlias와 비슷하다. 하지만 레이어를 저장하고 처리한다.

hardEdge 에서 antiAliasWithSaveLayer 까지 갈수록 이미지가 부드러워 지지만, 처리하는데 드는 비용이 늘어난다.
즉 성능이 떨어진다.

## GridView 위젯

위젯배치를 한방향이 아닌 두방향으로 하는게 가능하게 해주는 위젯이다.

### .count 생성자

위젯을 메인, 크로스 축으로 몇개씩 배치할지 정해줄 수 있다.
crossAxisCount: 크로스 축으로 몇개씩 배치할지 정해준다.
mainAxisCount: 메인 축으로 몇개씩 배치할지 정해준다.
children: 렌더링할 위젯 리스트.

화면에 보이지 않더라도 children내의 모든 위젯을 렌더링 한다.

위젯간 간격을 주려면
crossAxisSpacing: 크로스 축 간격
mainAxisSpacing: 메인 축 간격
위 파라미터를 사용하자.

### .builder 생성자

화면에 렌더링 되는 위젯만 렌더링해야 할때, 퍼포먼스가 중요할때 사용하자.

gridDelegate: 그리드 내부 타일의 레이아웃을 제어한다. 즉 이 위젯의 렌더링 방법을 정의한다.
itemBuilder: 위젯을 렌더링하는 함수 context와 index를 파라미터로 받는다.

## ReorderableListView 위젯

children: 위젯 리스트
**위젯 리스트는 key프로퍼티가 필수**

### parameter: onReorder

위젯을 재배치할때 호출되는 함수. 이전 인덱스 번호와 새로운 위치의 인덱스를 파라미터로 받는다. 주의할점은 **oldIndex, newIndex 모두 이동이 되기 전에 산정된다.**

### .builder 생성자

itemBuilder: 위젯을 렌더링하는 함수 context와 index를 파라미터로 받는다.

다른 builder 생성자들과 마찬가지로 렌더링이 필요할때만 렌더링한다.

이때 파라미터 index는 위젯의 인덱스가 아닌, 위젯 리스트의 인덱스이다.

## CustomScrollView 위젯

2개이상의 ScrollView 위젯을 동시에 적용하거나 할때 사용한다.

SliverAppBar와 조합하면 스크롤을 올렸을때 앱바가 사라지는 효과를 줄 수 있다.

### parameter: slivers

리스트 형태의 위젯을 받는다. SliverList, SliverGrid, SliverAppBar 등이 있다.

Sliver 위젯들만 내부에 들어갈 수 있다.

## SliverList 위젯

### parameter: delegate

ListView 생성자와 비슷하다.

SliverChildListDelegate: 위젯 리스트를 받는다.
SliverChildBuilderDelegate: 위젯을 렌더링하는 함수 context와 index를 파라미터로 받는다. 다른 builder들과 마찬가지로 위젯 렌더링이 필요할때만 렌더링한다.

## SliverGrid 위젯

delegate: SliverList의 delegate와 같음.
gridDelegate: GridView의 gridDelegate와 같음. 다만 Sliver 키워드가 붙어있어야 한다.

## SliverAppBar 위젯

### parameter: floating

default: false
위로 스크롤 하는 동안 앱바가 내려오는 효과를 주려면 true 값을 준다.

### parameter: pinned

default: false
true 값을 주면 스크롤을 내려도 앱바가 사라지지 않는다.

### parameter: snap

default: false

floating이 true일때만 동작한다.
스크롤을 내리다가 손을 떼면, 스크롤이 앱바를 완전히 내려놓거나, 다시 올려놓는다.

### parameter: stretch

default: false
true 값을 주면 맨위에서 스크롤을 내리면 앱바가 커지는 효과를 준다.
== 남는 공간을 채우는 효과를 준다.

### parameter: expandedHeight

앱바의 최대 높이를 정해준다.

### parameter: collapsedHeight

앱바가 밀려 들어가는 높이를 정해준다.

### parameter: flexibleSpace

FlexibleSpaceBar 위젯을 받는다.
앱바에 텍스트를 넣거나, 이미지를 넣거나 할때 사용한다.

앱바의 전체 높이를 차지한다.

## SliverPersistentHeader 위젯

sliver 사이에 레이블, 제목 등 헤더를 넣을때 사용한다.

### parameter: delegate

직접 SliverPersistentHeaderDelegate를 확장한 클래스를 제작해서 사용해야 한다.

## SliverPersistentHeaderDelegate 클래스

build, maxExtent, minExtent, shouldRebuild를 오버라이딩 해야 한다.

### build (BuildContext context, double shrinkOffset, bool overlapContent)

헤더를 어떻게 만들것인지 정의해야 하기 때문에 위젯을 리턴해야 한다.

### maxExtent

위젯의 최대 높이

### minExtent

위젯의 최소 높이

### shouldRebuild(covariant SliverPersistentHeaderDelegate oldDelegate)

위젯이 재생성 되어야 하는지를 정의한다.
build가 실행되었을때의 이전 delegate를 파라미터로 받는다.
this로 현재 delegate에 접근이 가능.

# 다른 Sliver 위젯들

[공식 문서](https://api.flutter.dev/flutter/widgets/CustomScrollView/slivers.html) 참고
