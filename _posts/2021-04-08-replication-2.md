---
title: "레플리케이션"
toc: true
toc_sticky: true

categories:
  - Mongo
tags:
  - mongod
  - certificate
  - replication

last_modified_at: 2020-04-13T12:45:00+00:00
---

몽고디비는 비동기 레플리케이션을 지원한다고 한다.

그럼 레플리케이션은 무엇일까?

## 레플리케이션,replication

안정성을 높히기 위해 데이터를 복사하여 저장하는것을 레플리케이션이라고 한다.
데이터를 복제하는것은 영속성을 위한 데이터베이스들이라면 대부분 신경써야하는것인데,

언제 데이터베이스가 다운될지 모르기 때문이다.

레플리케이션을 활용해 다른 서버에 같은 데이터를 저장하고 있었다면 메인 데이터베이스가 다운되더라도 서비스를 지속할수 있다.
즉 availability와도 연관이 깊은 개념.

하지만 싱글 데이터베이스 = 스탠드얼론 데이터베이스라면? 데이터베이스가 다운되면 데이터에 접근이 불가능하다.

### 몽고디비에서 레플리케이션

몽고디비는 3개이상의 노드가 같은 데이터를 저장하고 있다.
그중 하나는 프라이머리 노드라해서 어플리케이션과 직접 소통하며 데이터를 갱신하는 노드다.
다른 노드들중 일부은 세컨더리 노드라고 하는데, 프라이머리 노드와 통신하며 데이터를 싱크하는 예비 노드들이다.
또한 아비터라는 노드들도 있는데, 페일오버에서 리더를 선정해는 선거에만 관여하는 노드들이다.

히든노드라는것도 존재한다.
히든노드는 어플리케이션이 접근하지 못하는 감쳐진 노드 혹은 읽기 전용 노드가 필요할때 사용한다.
혹은 데이터 싱크 시간을 일부러 늦춰두기도 하는데 이때는 딜레이 노드라고 부르기도 한다.
딜레이 노드는 백업파일 대신 데이터 문제를 해결하는데 도움을 준다. 가령 실수로 drop한 콜렉션을 복구한다거나....

이때 어느 한쪽(주로 프라이머리 노드)에서 데이터 갱신이 발생한다면 다른쪽(주로 세컨더리 노드)도 마찬가지로 갱신되어야 한다.

이때 몽고디비는 비동기를 활용하는데, 플랫폼이 다를수 있기 때문이다.

그런데 이때 주로사용되는 노드(프라이머리)가 다운된다면?
세컨더리 노드들이 election을 통해 선정된 노드 하나가 프라이머리 노드를 대신한다.
이를 Failover라고 한다.

failover이후 기존 프라이머리 노드가 복구되면 다시 세컨더리 노드로 편입된다.

이런 레플리케이션은 두가지 동작방식이 존재한다.

- binary 레플리케이션
  데이터 파일이 변경되었을때 실제로 변한 바이트들을 확인하고 바이너리 로그에 작성하고 세컨더리 노드들은 바이너리 로그에 따라 데이터를 변경하는 방식.
  세컨더리 노드가 해야할 것이 명확하지만 바이너리 로그를 확인해야 한다. 몽고디비 버전, 운영체제, 64 혹은 32비트 인지도 전부 통일해야한다.

- statement 레플리케이션
  명령어를 oplog에 작성하여 공유하고 이를 세컨더리노드들이 데이터에 적용하는 방식이다. 몽고디비가 사용하는 방식이며, $inc 등의 쿼리를 update로 바꾸는등의 작은 작업이 선행된다.

상대적으로 비교하자면 바이너리 방식은 statement 방식에 비해

1. 세컨더리 노드에 전달되는 데이터가 적다
2. 필요한 작업량이 적어 속도가 빠름
3. 로그파일에 바로 반영이 된다.

하지만 statement 방식은 바이너리 방식이 비해 호환성이 좋다.

## 리더선정

리더 선정은 RAFT 프로토콜을 사용한다고 한다.

리더 선정은 몽고디비 인스턴스의 변화(인스턴스 다운, 추가등)이 발생하면 즉시 발생한다고 한다.
**리더선정은 항상 과반수를 얻어야 당선이 된다. 또한 투표에 참가하는 노드수는 최대 7이다.**
7개이상의 노드가 참여하면 응답시간이 늘어나고 데이터 영속성에 악영향을 미치기 때문이다.

아비터 노드는 가급적이면 지정하지 않는것이 좋을듯하다.
데이터 일관성 관점에서 혼란을 초래할수 있을듯 하기 때문.

## 레플리카 세팅하기

로컬에 설치된 스탠드얼론 노드를 레플리카로 구성하기 위해서는 다음 과정이 필요하다.

- 각 mongod 프로세스가 사용할 서로다른 conf 파일
- 각 mongod 프로세스들이 클라이언트와 다른노드를 인증할때 사용할 키 파일

전자는 기존 스탠드얼론 conf 파일을 복사한다음 수정할것이다.

### conf 파일 생성, 수정하기

기존 mongod.conf 파일은 다음과 같다.

```yaml
systemLog:
  destination: file
  path: /opt/homebrew/var/log/mongodb/mongo.log
  logAppend: true
storage:
  dbPath: /opt/homebrew/var/mongodb
net:
  bindIp: 127.0.0.1
```

해당 파일을 아래와 같이 수정했다.

```yaml
# mongod1.conf
systemLog:
  destination: file
  path: /opt/homebrew/var/log/mongodb/mongo1.log
  logAppend: true
storage:
  dbPath: /opt/homebrew/var/mongodb1
net:
  bindIp: 127.0.0.1,localhost
  port: 27011
security:
  authorization: enabled
  keyFile: /opt/homebrew/var/mongodb/pki/clusterKey
replication:
  replSetName: example-set
processManagement:
  fork: true
```

이걸 약간 수정해서 2,3번 노드용 파일도 생성해 두었다.

```yaml
# mongod2.conf
systemLog:
  destination: file
  path: /opt/homebrew/var/log/mongodb/mongo2.log
  logAppend: true
storage:
  dbPath: /opt/homebrew/var/mongodb2
net:
  bindIp: 127.0.0.1,localhost
  port: 27012
security:
  keyFile: /opt/homebrew/var/mongodb/pki/clusterKey
replication:
  replSetName: example-set
processManagement:
  fork: true


# mongod3.conf
systemLog:
  destination: file
  path: /opt/homebrew/var/log/mongodb/mongo2.log
  logAppend: true
storage:
  dbPath: /opt/homebrew/var/mongodb2
net:
  bindIp: 127.0.0.1,localhost
  port: 27012
security:
  keyFile: /opt/homebrew/var/mongodb/pki/clusterKey
replication:
  replSetName: example-set
processManagement:
  fork: true

```

백그라운드 프로세스를 새로 생성하기 위해 fork 옵션을 주었다.

이제 터미널에서 스토리지에 명시된 mongodb1, mongodb2, mongodb3 폴더를 생성하도록 하자.

### 키파일 생성하기

```
openssl rand -base64 741 > /opt/homebrew/var/mongodb/pki/clusterKey
chmod 600 /opt/homebrew/var/mongodb/pki/clusterKey
```

위 명령어로 키파일을 만들고 권한을 수정해 주었다.

600 권한을 준 이유는 몽고d만 키파일을 읽을수 있지만 다른 그룹등에서는 접근을 하는걸 방지하기 위해서다.

### 몽고d 실행 및 컨피그

`mongod -f` 명령어를 이용해서 mongod1.conf등 conf 파일들을 실행하도록 하자.
다음 mongod --port 27011로 접속하여 레플리카셋을 초기화 하고 새로운 유저를 만들어야 한다.

```bash
> rs.initiate()
> use admin
# 루트권한의 유저를 admin db에 생성해야 한다.
> db.createUser(...)
```

다음 재접속을 시도해야 한다. authorization 옵션이 true 이고 새로운 유저를 생성했으므로 유저네임과 비밀번호를 타이핑 해야한다.

```bash
$ exit
$ mongo --host "${replSetName}/192.168.103.100:27011" -u "${생성한 유저네임}"  --authenticationDatabase "admin"
```

conf 파일을 작성할때 replSetName부분에 작성한 레플리카 셋의 이름을 작성하며 유저네임을 입력해야 한다.

```bash
# 예시. replSetName은 example-set으로 작성하고, 루트 유저의 네임은 m103-admin으로 작성해둔 상태.
$ mongo --host "example-set/192.168.103.100:27011" -u "m103-admin"  --authenticationDatabase "admin"

```

이후 비밀번호를 타이핑하면 접속이 될텐데, 이후 rs.status()를 입력해보면 레플리카셋의 상태를 알수 있다.
하지만 레플리카 셋에 호스트가 추가되어 있지 않으므로, 직접 추가해야한다.

```
> rs.add({"example-set":27012})
> rs.add({"localhost":27013})
```
