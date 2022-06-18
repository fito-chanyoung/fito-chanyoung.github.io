---
title: "dart 기초 - 조건문과 연산자"
toc: true
toc_sticky: true

tags:
  - dart

last_modified_at: 2022-06-18T16:00:00+00:00
---

# 연산자
다른 프로그래밍 언어들과 마찬가지로 숫자, 문자, 동일여부 확인등
여러 연산자들이 존재한다.

## 숫자

### 사칙연산
덧셈, 뺄샘, 곱셈, 나눗셈(몫과 나머지)를 구할때 사용한다.
기초적인건 제외하고 설명한다.

`X=` 방식
int 형 변수 뒤에 작성되며 뒤에 작성되는 값만큼 사칙연산을 진행한다.

```
int number = 10;
number += 5; // 15
number -= 5; // 10
number *= 5; // 50
number /= 5; // 타입오류! 나눗셈은 double로 타입이 변한다.
```
위에서 볼수 있다싶이, 변화된 값을 다시금 변수에 저장한다.

그리고 += 한정으로 String 타입의 변수도 사용가능하다.

이와중 덧셈과 뺄셈은 추가적인 방법이 존재한다.
`++`, `--` 방식이 그것인데,
이는 반복문으로 패스

아 그리고 / 와 %는 서로 몫을 구하거나 나머지를 구할때 사용한다.

### null관련
연산자에 넣어야할지 좀 고민스럽긴 한데 적어두는건 좋아보여서 적는다.

변수에 null이 허용된 경우, 변수에 null이 저장되어있는지 여부에 따라
값을 부여하거나 해야할 경우가 생긴다.

이때는 `??=` 연산자? 를 이용하여 null인 경우에 집어넣을 값을 명시해 주면 된다.
```dart
double? number = null;
number ??= 5.0 // number => 5.0
```

### 논리및 비교연산
주로 if조건문, 반복문에 사용된다.

결과값으로는 true / false를 리턴한다.

== : 피연산자 서로 동일
!= : 피연산자 서로 불일치
<= : 좌측 피연산자 보다 우측 피연산자가 크거나 같음
>= : 위와 반대

&& : 좌측 `조건문`과 우측 `조건문`이 모두 참인경우 참을 리턴
|| : 좌우측 조건문들중 하나만 참이여도 참을 리턴
!  : true를 false로, false를 true로 만듬

```dart
int num1 = 5; 
int num2 = 6;

print(num1 < num2); // true
print(num1 > num2); // false
print(num1 <= num2); // true
print(num1 >= num2);  //false
print(num1 == num2); //false
print(!(num1 == num2)) // true

print((num1 < num2) && (num1 > num2)); // false
print((num1 < num2) || (num1 > num2)); // true
print((num1 < num2) && !(num1 > num2)); // true
```

### 타입비교
타입을 서로 비교하려면 is를 연산자 처럼 사용하면 된다.
다만 우측 피연산자는 int, double등 키워드가 와야한다.

```dart
...
print(number1 is int) //
```
# 조건문
## if
여러 프로그래맹에서 사용하는 조건문의 그것.
else if, else를 이용해 분기를 할수 있다.

```dart

if(조건문) {

} else if(조건문) {

} else {

}
```

## switch & case
if문과 비슷한 키워드
switch & case가 if, else if 이고
else가 default로 바뀐 형태
```dart
switch(number %3){
  case 0: // 윗줄과 여기까지 if(number % 3)과 동일
    ....
    break; // 반드시 break를 넣어서 다른 코드 실행을 방지해주자. else if문과 동일
  case 1:
    ....
    break;
  default:  // else 문과 동일
    ...
    break;
}
```