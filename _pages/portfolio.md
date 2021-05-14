---
title: "포트폴리오"
permalink: /portfolio/
layout: single
author_profile: true
---

## Your tube

Your tube는 유튜브(Youtube)의 좋아요 목록을 조회하고 관리할 수 있는 서비스 입니다.

> 개발당시 : Express, React, Google Oauth, Elastic Search, MySQL
> 리팩토링 : Typescript, SASS, React, Express, MySQL, Google Oauth

유튜브 좋아요를 누른 동영상을 찾기 어려워 만들게 되었습니다.
코드스테이츠를 수강중일때 작성한 프로젝트중 하나이며 개발 당시에는 백엔드를 담당하여 엘라스틱 서치 엔진을 이용한
검색 기능 구현을 담당했습니다.

이후 클라이언트 및 백엔드 서버의 리팩토링을 혼자서 진행했습니다.

구현한 기능들은 다음과 같습니다.

1. 모바일 반응형 웹
   기존에 작성한 반응형 웹을 SASS를 이용하여 다시금 재구성 하였습니다.
   중구난방으로 흩어져있던 브레이크 포인트를 SASS를 이용해 하나의 파일에서 관리하게 제작하였습니다.

   ![image](/assets/images/yourtube-responsive)

2. 유튜브 좋아요 리스트
   유튜브 좋아요 리스트를 불러옵니다.
   데이터베이스에 저장된 데이터를 가져오기 때문에, 처음에 로그인한 유저는 동영상 리스트를 데이터 베이스에 저장하는 싱크 작업을 거치게 됩니다.

   동영상, 동영상 설명 부분을 클릭하면 특정 동영상을 시청할 수 있으며, x 버튼을 통해 좋아요 목록에서 제거할 수 있습니다.

   ![image](/assets/images/yourtube-list)
   ![image](/assets/images/yourtube-delete)

3. 다크모드
   해당페이지는 자체적인 다크모드를 지원합니다.

   ![image](/assets/images/yourtube-dark-reactive)

4. 동영상 조회
   키워드를 입력하면 본인의 유튜브 좋아요 리스트들중 제목과 설명에서 해당 키워드를 가지고 있는것들에 대해 리스트를 보여줍니다.

   ![image](/assets/images/yourtube-search)

5. 로그아웃
   세팅 모달창의 로그아웃 버튼을 클릭하면 로그아웃을 할 수 있습니다.

   ![image](/assets/images/yourtube-logout)
