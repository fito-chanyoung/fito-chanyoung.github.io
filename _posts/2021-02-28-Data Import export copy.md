---
title: "몽고비디 아틀라스 - 데이터 임포트/익스포트"
toc: true
toc_sticky: true

categories:
  - Mongo
tags:
  - mongos
  - certificate
last_modified_at: 2021-02-28T14:51:00+00:00
---

아틀라스는 다른 환경(운영체제)에서 데이터 임포트/익스포트를 보장한다.
그전에 포맷을 정해야 한다.

다른 샤드, 레플리카에 데이터를 옮기는 목적이라면 BSON,
데이터를 읽어야 하는 경우라면 JSON을 사용해댜 된다는 의미다.

데이터를 내보낼때는 URI를 사용할수 있다.
`SRV` 스트링을 사용하며 좀더 안전한 입출력을 제공한다고.
대략 아래와 같은 형태라고 한다.

```
monodb+srv://user:password@clusterURI.mongodb.net/database
```

저기서 유저, 비밀번호, 클러스터 URI, 입출력하고자 하는 데이터베이스 정보만 바꾸면 된다.

## JSON

### mongoimport

기본적인 사용법

```bash
mongoimport --uri "<atlas cluster URI>"
            --drop "<file name>"
            --collection "<collection name>
```

### mongoexport

기본적인 사용법

```bash
mongoexport --uri "<atlas cluster URI>"
            --colection=<collection name>
            --out=<filename>.json
```

## BSON

### mongorestore

기본적인 사용법 drop 옵션으로 해당 콜렉션을 제거한 다음 파일들을 올린다.(에러 방지용)

```bash
mongoimport --uri "<atlas cluster URI>" --drop "<collection name>"
```

### mongodump

기본적인 사용법

```bash
mongodump --uri "<atlas cluster URI>"
```
