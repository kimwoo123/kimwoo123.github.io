---
layout: project
title:  "IRC Server"
date:   2023-12-22 19:10:12 +0900
categories: Project
overview: "팀 프로젝트"
---

![../../../../../../public/assets/Projects/2023-12-22-IRCServer/ircserver.png](../../../../../../public/assets/Projects/2023-12-22-IRCServer/ircserver.png)

**요약**

- C++98 버전에서 인터넷 릴레이 채팅(Internet Relay Chat) 실시간 채팅 프로토콜을 준수하여 만든 IRC server.

**역할**

- 2명 / 50%
- kqueue를 사용한 I/O multiplexing
- multithreading 환경의 동기화
- 팩토리 메소드 패턴을 사용한 객체설계
- 효율적인 멀티스레딩을 위한 threadpool 구현
- 통합테스트 및 CI 구현

**배운 점**

- 잦은 네트워크 I/O가 발생하는 IRC 특성상 multithreading 설계로 싱글 스레드에 비해 1.5배에서 3배 이상 더 빠른 서버의 퍼포먼스를 발휘할 수 있었습니다. multithreading에서 필요한 동기화와 설계에 대해서 공부하였습니다.
- 디자인 패턴을 사용한 객체 설계로 유지 보수 및 가독성을 높였습니다. IRC에서 사용하는 여러개의 command들을 다루는 command 패턴과 factory method 패턴, 멀티 스레딩 환경에서 task들을 순서대로 처리하는 threadpool을 구현했습니다.
- 통합테스트를 통한 코드의 검증과 github actions를 사용한 CI환경으로 간편한 테스트 환경을 구축하였습니다.

**시기**

- 프로젝트 진행 기간 (2023/12/24 ~ 2024/2/25)

[프로젝트 깃허브](https://github.com/kimwoo123/IRC)

[자기소개 페이지](https://kimwooseok.com/about/)
