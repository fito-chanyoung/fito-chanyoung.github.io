---
title: "flutter - Dio, Retrofit"
toc: true
toc_sticky: true

tags:
  - dart
  - flutter

date: 2023-08-04
last_modified_at: 2023-08-04T14:31:00+00:00
---

# Dio

웹에는 네트워크 통신을 위해 Axios가 있다면 플러터에는 Dio가 있다.
아주 간단한 사용법

```dart
import 'package:dio/dio.dart';

final dio = Dio();

void getHttp() async {
  final response = await dio.get('https://dart.dev');
  print(response);
}
```

특징으로는 response.data를 파싱해서 object로 넘겨준다는 것이다.

## 인터셉터

인터셉터는 요청과 응답을 가로채서 특정 작업을 할 수 있도록 해주는 큐이다.
따라서 인터셉터를 잘 활용한다면 헤더를 추가하거나 에러를 처리하는 등의 작업을 할 수 있다.

개인적으로는 앱 실행시 헤더에 토큰을 추가하는 작업등에서 사용했다.

## baseUrl 설정하기

Axios와 달리 설정하는것이 조금 까다롭다.

```js
const axiosClient = Axios.create({
  baseURL: "https://api.example.com",
});
```

```dart
class BaseDio {
  Dio dio = Dio(
      BaseOptions(baseUrl: Environment.baseUrl, listFormat: ListFormat.csv));

  BaseDio.withBaseUrl(String? baseUrl) {
    if (baseUrl != null) {
      dio = Dio(BaseOptions(baseUrl: baseUrl));
    }
  }

  static final BaseDio _instance = BaseDio.withBaseUrl(null);

  factory BaseDio() {
    return _instance;
  }
}

// 다른 dart file
final dio = BaseDio().dio;

```

위와 같은 방식으로 baseUrl이 설정된 dio를 사용할 수 있다.
또한 인터셉터를 사용해서 헤더를 추가할 수 있다.

````dart

# retrofit

분명 Dio로도 충분히 통신을 할 수 있지만, Dio를 좀 더 편하게 사용할 수 있도록 도와주는 라이브러리가 retrofit이다.

요청과 응답에 사용될 class를 선언하고 어노테이션을 붙여준 다음 abstract 클라이언트를 선언만 해주면
builder가 자동으로 코드 생성을 해주고 사용할 수 있게 해준다.

모델 클래스, 클라이언트 클래스, 요청과 응답 클래스를 선언해야 한다.

## 모델 클래스 사용법
1. JsonSerializable 어노테이션을 붙여준다.
2. 클래스 프로퍼티를 선언한 다음, 생성자를 만들어준다.
3. `factory` 키워드를 사용해서 `fromJson`를 만들고 Map을 리턴하는 `toJson`을 만들어준다.
4. `part` 키워드를 사용해서 만들어질 g.dart 파일을 선언한다.
6. `flutter pub run build_runner build` 명령어를 실행한다.
   - 이미 실행중이라면 4번째 순서에서 자동으로 g.dart 파일이 생성된다.




### 모델 예시

```dart
import 'package:json_annotation/json_annotation.dart';

// 이런 파일이름.g.dart 파일이 있어야 생성이 가능하다.
part 'dummy_model.g.dart';

@JsonSerializable()
class DummyModel {
  final String? name;
  final String? job;

  DummyModel({this.name, this.job});

  //
  factory DummyModel.fromJson(Map<String, dynamic> json) =>
      _$DummyModelFromJson(json);

  Map<String, dynamic> toJson() => _$DummyModelToJson(this);
}
````

## 요청과 응답 클래스 사용법

모델클래스와 똑같다.

### 요청 및 응답 클래스 예시

```dart
import 'package:json_annotation/json_annotation.dart';
import 'package:pocket_message/.../dummy_model.dart';

part 'get_dummy.g.dart';

@JsonSerializable()
class GetDummyModelQueries() {
  final String? name;
  final String? job;

  GetDummyModelQueries({this.name, this.job});

  //
  factory GetDummyModelQueries.fromJson(Map<String, dynamic> json) =>
      _$GetDummyModelQueriesFromJson(json);

  Map<String, dynamic> toJson() => _$GetDummyModelQueriesToJson(this);
}

@JsonSerializable()
class GetDummyModelResponse{
  List<DummyModel> models;

  GetDummyModelResponse({required this.models});

  factory GetDummyModelResponse.fromJson(Map<String, dynamic> json) =>
      _$GetDummyModelResponseFromJson(json);
  Map<String, dynamic> toJson() => _$GetDummyModelResponseToJson(this);
}
```

## 클라이언트

이 클래스는 JsonSerializable 어노테이션을 붙여주지 않는다.
`@RestApi` 어노테이션을 붙여주고, `abstract` 키워드를 붙여준다.
`@RestApi` 어노테이션을 붙여주면 `part` 키워드를 사용해서 g.dart 파일을 선언해줘야 한다.

또한 통신을 하기 위해서 dio를 인자로 받는 생성자를 만들어준다.

### 클라이언트 예시

주의할점. 아래 코드는 BaseUrl 정보가 설정된 dio를 받아서 사용한다. 그러므로 url 정보가 들어가있지 않다.

```dart
// 클라이언트 선언

import 'package:dio/dio.dart';
// 기타 모델 임포트
import 'package:retrofit/http.dart';

part 'dummy_client.g.dart';

@RestApi()
abstract class GetDummyClient {
  factory GetDummyClient(Dio dio) = _GetDummyClient;

  @GET("/dummy/{id}")
  Future<GetSentPackageResponseModel> getSentPackage(@Path() String id);

  @POST("/dummy")
  Future sendDummy(@Body() SendDummyBody body);
}

// 클라이언트 호출

... await GetDummyClient(dio).getSentPackage("id");

```

### 리스폰트 데이터 다듬기 with generic

간혹 리스폰스가 몇겹의 껍데이 오브젝트로 감싸져 있는 경우가 종종 있다.

이럴때 완벽하지는 않지만 리스폰스에서 필요한 데이터만 추출해서 사용하고 싶다면, 아래와 같이 사용하면 된다.

```dart
@JsonSerializable(
  genericArgumentFactories: true,
)
class ResponseModel<T> {
  T body;

  ResponseModel({
    required this.body,
  });

  static _fromJson<T>(Object? json) {
    if (json is Map<String, dynamic>) {
      if (json.containsKey('response')) {
        return json['response'] as Map<String, dynamic>;
      }
    }

    throw Exception("Unknown type");
  }

  factory ResponseModel.fromJson(
      Map<String, dynamic> json, T Function(Object? json) fromJsonT) {
    final body = _fromJson<T>(json);

    return _$ResponseModelFromJson<T>({...body}, fromJsonT);
  }
  Map<String, dynamic> toJson(Object Function(T) toJsonT) =>
      _$ResponseModelToJson(this, toJsonT);
}

// 클라이언트

 Future<ResponseModel<GetPM10HourResponse>> getPM10Hour(
      @Queries() GetPM10HourQueries queries);


// 클리이언트 호출
 final res = await PublicApiClient(dio).getPM10Hour(query);

    setState(() {
      observatoryName = res.body.items;
    });

```

위 코드는 response => body => 데이터인 형태에서 body => 데이터로 바꿔주는 코드이다.
아쉽게도 res.items와 같이 접근하는 방법은 찾지 못했다.
