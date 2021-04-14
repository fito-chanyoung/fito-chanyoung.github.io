---
title: "레플리케이션-2"
toc: true
toc_sticky: true

categories:
  - Mongo
tags:
  - mongod
  - certificate
  - replication

last_modified_at: 2020-04-14T21:00:00+00:00
---

## 레플리케이션 셋 관련 명령어

레플리케이션 셋은 여러 명령어를 가지고 있다.
앞선 포스트에서 보지못한 일부 명령어를 정리해 보자

### rs.status

레플리카셋 노드들의 상태를 출력한다.
rs.status를 타이핑한 순간이 아닌 허트비트를 기준으로 상태를 출력하기 때문에 약간의 차이가 있을수 있다.

### rs.isMaster

rs.status와 유사하지만 조금더 짦은 아웃풋을 볼수있다.
같은 내용이 아닌 레플리카셋에서 노드들의 역활에 대한 내용을 볼 수 있다.

### rs.printReplicationInfo

**현재 노드**사 더달둘링 oplog를 리턴한다.
처음과 마지막 오피로그의 타임 스탬프를 확인할 수 있다. **어떤 쿼리가 실행되었는지 알 수 없다.**

### rs.remove

레플리카셋에서 멤버를 제거하는 메서드.
하지만 바로 사용할 수는 없고 세컨더리 노드에 접근하여 어드민 db에서 shutdownServer 함수를 실행해야 한다.

사용법은 rs.add와 유사한듯 하다

### rs.addArb

세컨더리 노드가 아닌 아비터 노드를 추가하는 명령어.

### rs.conf

레플리카셋의 설정 도큐먼트 전체를 리턴하는 명령어.
레플리카셋의 멤머, 이름 등의 설정 전부를 확인할 수 있다.

### rs.reconfig

매개변수로 전달받은 데이터를 이용해 프로세스를 재시행하지 않고도 레플리카 셋의 설정을 변경하는 메서드.
전달받는 데이터에는 레플리카 셋에 대한 모든 정보를 담고 있어야 한다.

따라서 rs.conf를 이용한 데이터를 변조해 다시 사용하는편이 좋다.

예시

```bash
> cfg = rs.conf()
> cfg.member[3].hidden = true
> cfg.member[3].priority = 0
> cfg.member[3].votes = 0
> rs.reconfig(cfg)
```

몽고디비 쉘에서 특정 세컨더리 노드를 투표에 참여하지 않는 노드로 수정했다.
