---
title: "몽고디비 서버 툴들"
toc: true
toc_sticky: true

categories:
  - Mongo
tags:
  - mongod
  - certificate
  - server tools

last_modified_at: 2020-04-13T11:36:00+00:00
---

## 몽도디비 서버 툴

패키지를 다운받았을때 기본으로 확인가능한 툴중 일부에 대해 정리한다.

m1 맥북에서는 다음과 같은 패키지들이 확인되었다.

```bash
/opt/homebrew/bin > stable
$ find ./ -name "mongo*"
.//mongodump
.//mongod
.//mongotop
.//mongoexport
.//mongos
.//mongoimport
.//mongostat
.//mongo
.//mongorestore
.//mongofiles
```

이중 몽고 스탯, 덤프 , 리스토어, 익스포트, 임포트에 대해 정리해 보려 한다

### 몽고 스탯

몽고스탯은 `스탯`이라는 이름이 들어가 있다.
관련되어 제공하는 기능은 `상태(status)`를 확인 가능하다.

실행중인 mongod, mongos 관계없이 특정 통계를 보여준다고.

사용법은 다음과 같다.

```bash
# 특정 포트에서 몽고d, 몽고s 가 실행중인경우
$ mongostat --port <포트번호>
```

그럼 아래와 같은 터미널 화면이 등장한다.

```bash
insert query update delete getmore command dirty used flushes vsize  res qrw arw net_in net_out conn                time
    *0    *0     *0     *0       0     0|0  0.0% 0.0%       0 10.2G 229M 0|0 1|0   109b   39.5k    4 Apr 13 11:45:32.704
```

매초마다 상태를 간략하게 보여준다.

앞의 6개 컬럼(insert ~ command)은 초당 특정 혹은 전체 명령어의 수, 뒤의 7개 컬럼(dirty ~ arw)은 초당 메모리 통계치를 출력한다.
res는 resonant memory의 용량에 대한 정보인데, resonant memory가 무엇인지 모르겠다.
net_out, net_in은 당연히 네트워크 요청과 응답의 양.

### 몽고 리스토어 & 몽고 덤프

두 유틸리티 툴은 하나의 페어로 보면 될듯.

두 유틸리티는 덤프 파일을 내보내고 다시 가져올때 사용한다.
덤프파일은 당연히 BSON 포맷.

1. 데이터 덤프

```bash
# 몽고 연결과 비슷함
$ mongodump --port ${포트번호} -u ${유저이름} -p ${비밀번호} --authenticationDatabase  ${유저 저장 데이터베이스=admin} --db  ${덤프할 db 이름} --collection  ${덤프할 컬렉션 이름}

# 예시
$ mongodump "mongodb+srv://YOUR_CLUSTER_NAME.9bya3.mongodb.net" -u "m001-student"  --authenticationDatabase "admin" --db "sample_training" --collection trips
Enter password:
```

몽고에 접속하는것과 비슷하다.
다른점은 유저 정보가 기록된 데이터베이스를 명시(보통 admin 데이터베이스)해야 하며, 덤프할 db, 콜렉션 이름까지 명시해야한다.

사실 컬렉션 이름까지 명시할 필요는 없긴하지만, 명시하지 않는경우라면 데이터베이스의 모든 컬렉션이 덤프되기 때문.

이후 터미널에서 ls를 입력하면 dump 폴더가 생성되어 있고, 내부에는 덤프된 db 폴더를 확인할 수 있다.
json, bson 파일 한개씩 확인 가능한데,

bson 파일은 콜렉션 문서이고 json 파일은 콜렉션에 대한 메타데이터 파일이다.

```json
# 덤프파일 메타데이터 예시
{
  "indexes": [
    {
      "v": {
        "$numberInt": "2"
      },
      "key": {
        "_id": {
          "$numberInt": "1"
        }
      },
      "name": "_id_"
    }
  ],
  "uuid": "9c05c2d2ce9e490b8090b210af57a345",
  "collectionName": "trips"
}
```

2. 데이터 리스토어
   몽고 덤프로 덤프된 데이터는 mongorestore을 이용해 다시 컬렉션으로 만들수 있다.

```bash
# 아틀라스 덤프파일을 로컬 몽고에서 복구
$ mongorestore --drop -u "m103-admin" --authenticationDatabase "admin" dump/
```

현재 터미널의 경로에서 dump 폴더 내부의 데이터들을 이용해서 콜렉션을 복구한다.
이때 기존 컬렉션이 존재한다면 --drop 옵션으로 인해 기존 컬렉션을 삭제한 다음 컬렉션을 생성하게 된다.

### export & import

덤프파일, 리스토어가 BSON 포맷으로 데이터를 내보내고 다시 가져왔다면
export, import는 json 포맷 혹은 CSV 포맷으로 데이터를 조작한다.

하지만 다른 차이점도 존재하는데, **export는 명시하지 않는다면 디렉터리를 생성하지 않는다. 대신 stdout으로 내보낸다.**
따라서 어떤 디렉터리, 파일에 데이터를 작성해야 할지 명시해야 한다.

몽고덤프와 유사하지만 -o 옵션으로 디렉터리 경로, 파일명을 전달해주면 된다.

```bash
$ mongoexport --port ${포트번호} -u ${유저이름} -p ${비밀번호} --authenticationDatabase  ${유저 저장 데이터베이스=admin} --db  ${덤프할 db 이름} --collection  ${덤프할 컬렉션 이름} -o ${저장할 파일까지 경로}
```

이런 데이터를 import 하는것도 역시 restore와 유사하다.

```bash
$ mongoimport  -u ${유저이름} --authenticationDatabase  ${유저 저장 데이터베이스=admin} ${json 파일까지 경로}
```

다만 이렇게 작성하면 컬렉션 이름이 명시되지 않아 파일이름을 컬렉션 이름으로 사용하게 된다.
