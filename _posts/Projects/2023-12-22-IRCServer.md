---
layout: project
title:  "IRC Server"
date:   2023-12-22 19:10:12 +0900
categories: Project
overview: "overview"
---

**요약**

- c++98 버전에서 인터넷 릴레이 채팅(Internet Relay Chat) 실시간 채팅 프로토콜을 준수하여 만든 IRC server.

**역할**

- 2명 / 50%
- kqueue를 사용한 I/O MultiPlexing
- multithreading 환경의 동기화
- 팩토리 메소드 패턴을 사용한 객체설계
- 소비자 생성자 패턴을 사용한 잡큐 구현

배운 점

- 잦은 네트워크 I/O가 발생하는 IRC 특성상 multithreading 설계로 더 나은 서버의 퍼포먼스를 발휘할 수 있었습니다. multithreading에서 필요한 동기화와 설계에 대해서 공부하였습니다.
- 디자인 패턴을 사용한 객체 설계로 유지 보수 및 가독성을 높였습니다. IRC에서 사용하는 여러개의 Command들을 다루는 Command 패턴과 Factory method 패턴, 멀티 스레딩 환경에서 Task들을 순서대로 처리하는 Job queue를 구현했습니다.

**시기**

- 프로젝트 진행 기간 (2023/12/24 ~ )