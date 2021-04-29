---
title: "JS 기본 자료구조 - Queue, Stack"
toc: true
toc_sticky: true

categories:
  - Javascript
tags:
  - basic
  - data structure
  - linked list
  - hash table
  - graph
  - tree
last_modified_at: 2020-03-19T16:23:00+00:00
---

**매우 주관적인 포스트로서 사실과 다를수도 있습니다. 개인적인 정리 차원입니다.**

아주 아주 간단하고, 기초적인 자료구조에 대하여 내가 알고, 복습한 것들을 정리해 보자

우선 내가 노트에 끄적여 본 대략적인 큐와 스택의 구조도

내 머리속 큐와 스택의 개요

​![](https://postfiles.pstatic.net/MjAyMDAzMTlfMTcy/MDAxNTg0NjAxNDI1Mjcy.FEgpYpKtbEgnEww8LeK9XVL7WJco0lh5LC5cmDzVPsMg.zYSb4TG5ol2UWsT9WXydi_i0VWZiUESvYgq0ReQBuIcg.JPEG.scm0222/SE-f749f27b-f1bf-40e1-b7b4-e43154e24769.jpg?type=w966)

​

## 큐(Queue)

큐는 실생활에서 대기줄같은 곳에서 잘 볼수 있다. 큐는 FIFO(First In First Out)를 요구하는 곳이라면

매우 매우 흔해서 큐 만 보게 될것이다.(스택을 사용하면 뭐 문 닫아야지..)
​![](https://postfiles.pstatic.net/MjAyMDAzMTlfMTc0/MDAxNTg0NTk4MTA5Njk0.N4uvjfcwfym0ISM_qkN7TrRv0HixAlwn1Z5-diS0OqQg.N5HNI-HTukO2apmP_9E5qYgDArHpeGDCBttE6g9Rpnwg.PNG.scm0222/408-4088615_students-waiting-in-line-clipart-free-clipart-long.png?type=w966)
​

즉 처음의 인풋에 대하여 가장 빠른 응답을 보장해준다.

큐의 예제, 일상생활에서 매우 흔하게 접할수 있다. - PNGkey

이 큐를 만약 코드로 작성한다면 필요한 것은

1. 대기열 맨 마지막에 요청을 집어 넣는 함수

2. 맨 앞에서 요청을 처리하는 함수(제거하는 함수)

3. 2번 직후 대기열을 재정비 하는 함수(선택)

4. 대기열(배열)

5. 처리해야할 위치, 마지막 입력의 위치 저장 변수

이정도?

​

코드로 짠다면 의사코드로

```js
function pseudoQueue(input){
  if(array.full)
    return queue full error
  array.push_back(input);
}

function pseudoDequeue(){
  if(array.empty)
    return queue empty error
  array.shift()//js 문법
  //아니면
  for(
  arr[n] = arr[n+1]
  )
}
```

이렇게 되지 않을까?

peseudoQueue는 파라미터로 어떤 값을 받는데, 큐가 꽉찬 경우에는 더이상 큐에 집어 넣지 않고, 큐에 자리가 있는 경우에만

큐에 인풋을 집어 넣는다. 마지막 원소의 위치를 가리키는 인덱스의 값을 증가시키거나 혹은 배열 위치 조정 함수가 있는경우에는 가만히 있는다.

​

pseudoDequeue는 아무 파라미터 없이, 큐에서 처리해야할 인덱스가 가리키는 위치 혹은 처음 인덱스의 위치의 값을 처리한다.

이에 큐의 배열을 위치 조정을 해주거나, 처리해야할 인덱스의 값을 변경시켜 다음 작업 위치를 조정하는 함수 호출 혹은 처리 위치 인덱스 +1을 해준다.

​

만약 저 큐가 링크드 리스트 같은 거면 값을 삭제할때 for문 돌 필요 없이 위치 조정을 하고 delete로 날려버리면 되지 않을까 싶다.

하지만 저런 일직선 큐는 다시 처음 부분으로 돌아와야 하는 상황이 존재할 수도 있다. 또한 우선 순위로 적업해야 하는 경우도 생각 할수도 있다.

​

마지막 원소 위치 인덱스 변수

마지막으로 큐에 저장된 원소의 위치를 저장한다. 값이 저장되고 간단하게 +1을 해주거나, 위치 조정 함수가 값을 변경 시킨다.

​

처리해야할 위치 인덱스 변수

처리 함수가 처리해야할 원소의 위치를 가리키고 있다. 처리 함수가 해당 원소를 처리한 이후 간단하게 +1 을 해주거나, 위치 조정 함수가 있는경우 존재 하지 않을 수도 있다(항상 처리함수는 0번째 원소를 가져오게 된다)

​

### 원형큐

만약 큐를 작성했는데, 인풋의 처리가 단순히 iterator에 값을 1을 증가 시켜서 작업하는 경우, 큐의 뒤는 꽉찼는데, 앞은 비어있는 상황이 생길수도 있다. (마지막으로 입력된 인풋의 위치 = 배열의 끝 , 처리해야할 인풋의 위치 = 배열의 끝보다 작음) 이러면 자리는 남았는데 더이상 인풋을 수용할수 없다. 그러므로, 마지막으로 입력된 인풋의 위치를 처음으로 되돌리며, 차후에 처리해야할 인푸의 위치도 처음으로 되돌려야 한다.

단순히 마지막으로 입력된 인풋의 위치가 배열의 끝이라면 위치를 0으로 바꿔버릴수도 있다.

​

하지만 그러지 말고 차라리 나머지 계산자를 이용하여 배열의 크기로 나눈 나머지를 저장하여 이용하면 매우 편리하며, 코드와 동작도 일관적이다.

[image](https://postfiles.pstatic.net/MjAyMDAzMTlfMTAg/MDAxNTg0NTk5NDM2NjYw.SQjUpbPAg64_grntur-_TfqaI4ecFKy1hAbJcByMb3Qg.zEUu6xzLajn4G9B56IjjDC4hMOjQCObDCp_QepBT5Gkg.GIF.scm0222/ring_queue.gif?type=w966)
링 큐 혹은 원형 큐의 동작 모슴 - Wikipedia

이런 경우에는 배열이 다 찬 경우가 있는데 이때 처리해야하는 인덱스가 처음이고, 마지막으로 들어온 인덱스의 위치가 제일 마지막인 경우를 처리해야 한다. 단순히 == 를 이용하여 펜딩 시키면 된다.

​

### 우선큐 priority queue

우선 큐 방식은 우선도라는 변수를 두어, 상위 우선도가 맨 마지막에 들어와도, 우선적으로 처리하는 큐이다.

그러므로, 제일 낮은 우선도의 작업은 큐의 맨 앞에 있더라도 계속 대기해야 하는 상황이 발생할 수 도 있다.

단순히 다음 인덱스로 넘어갈때 마다 우선도를 체크하여 우선 순위가 발견되면 그 작업을 실행하고, 큐를 정리하고,

다시 일반 큐 작업을 실행 시키면 된다. 저장된 위치는 변하지 않는다.

​

여기에 소팅 알고리즘등 으로 우선 순위를 탐색하는 시간을 줄일 수도 있을 것이다.

​

​

## 스택

​

스택은 실생활에서도 역시 찾아 볼수 있는데, 인터넷 뒤로가기, 메모리, 진열 상품, 장난감에서도 볼수 있다.

자주 사용되는 유우명한 예가 있는데

​

**하노이 타워**가 그것이다.

​![](https://postfiles.pstatic.net/MjAyMDAzMTlfMjIw/MDAxNTg0NjAwMjc0MTA1.nJetdx-MdPpMSG6ccDbIUbA7mQa-l2VgVhIMk5JULYQg.ouLBeSvaXOpMgwlwA4WWp3jssSoe3pFojayVUrJZmnQg.JPEG.scm0222/Tower_of_Hanoi.jpeg?type=w966)
하노이 타워 -Wikipedia

하노이의 탑은 왼쪽에 있는 링들을 크기 순서대로 큰것을 아래쪽으로, 작은 것을 위쪽으로 정리하여 제일 오른쪽으로 옮겨야 하는 게임? 퍼즐? 인것이다. 보다싶이 먼저 제일작은 링부터 위에서 부터 꺼내야 제일 아래쪽의 링을 꺼낼수 있다. 또한 링은 옮길수는 있지만, 반드시 링에 꽂아야만 하며, 작은 링위에 큰 링이 올 수 없는 걸로 기억하고 있다.

​

이것도 풀어내는 알고리즘도 있지만, 우선 스택에 대하여 설명하자면, 스택은 FILO(First In, Last Out)이 특징이다.

그러므로 맨 아래에 있는 것은 언제 처리될지, 사용될지 미지수이며, 가장 높은 곳에 있는것은 최우선으로 처리된다.

​

우리가 없으면 매우 지루할 정도로 사용하는 인터넷의 뒤로 가기도 스택을 사용하며, 메모리에서 이름 그자체의 스택도 프로그램의 실행에 이 스택을 사용한다. 함수 호출을 계속하거나(특히 재귀함수), 매우 큰 함수 호출을 하면(아마도) 스택 오버 플로우라며 프로그램의 동작이 중지될 것이다.

​

그러므로, 사용할 함수, 변수들은

1. push

2. pop

3. 스택으로 사용할 배열

4. 쌓인 인풋의 높이를 저장하는 인덱스

​

이정도로 충분하다.

​

push

이 함수는 스택에 쌓인 높이를 저장하는 인덱스 보다 1 큰 값으로 인풋으로 들어온 원소를 집어 넣는다. 스택의 높이를 1 추가한다.

만약 스택이 꽉찬 경우에는 더이상 스택에 추가하지 않는다.

​

pop

이 함수는 스택에 제일 마지막 원소를 스택에서 제거 한다. 옵션으로 그값을 반환하기도 한다. 스택의 높이를 1 만큼 낮추며, 스택이 비어있는 경우에는 동작하지 않는다.

​

스택 높이 저장 인덱스

이 변수는 쌓인 스택의 높이를 저장하고 있다. 이 인덱스를 기반으로 값을 집어 넣을지 뺄지를 함수에서 결정하게 된다.

​

````
function push(item){
  if(index > array.height)
    return stack overflow error
  array.push(item)
  index ++ ;
}

function pop(){
  if(index < 1)
    return stack eampty error
   return delete(array.top())//array.top 은 가장 뒤에 원소를 리턴함과 동시에 그자리를 비움
}
​```
만약 배열에 push, pop을 자체적으로 제공하는 언어가 있다면 그냥 그거 쓰면 끝.
````
