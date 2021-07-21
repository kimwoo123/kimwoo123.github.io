---
layout: post
title:  "네트워크 트랜스포트 레이어"
date:   2021-07-21 20:10:12 +0900
categories: CS
overview: "[컴퓨터 네트워킹 하향식 접근]을 읽으며 정리한 내용"
---

### 트랜스포트 레이어

---

트랜스포트 계층은 서로 다른 호스트에서 동작하는 애플리케이션 프로세스 간의 논리적 통신을 제공한다. 논리적 통신은 애플리케이션의 관점에서 보면 프로세스들이 동작하는 호스트들이 직접 연결된 것처럼 보인다는 것을 의미한다.

- 트랜스포트 계층은 바로 아래층인 네트워크 계층으로부터 세그먼트를 수신한다.
- 네트워크 애플리케이션의 한 부분으로서 프로세스가 소켓을 가지고 있다.
- 소켓은 네트워크의 출입문 역할을 한다.
    - 소켓은 여러개 존재할 수 있다.
    - 여러개의 소캣을 구별해서 데이터를 처리하기 위해 세그먼트의 필드를 검사한다.
    - 출발지 포트번호 필드와 목적지 포트번호 필드가 이와 같다.
        - 각각의 포트번호는 0 ~ 65536(16비트) 의 정수이다.
        - 그 중에서 1023 까지의 포트번호를 Well-know port number 라고 하여 사용을 제한하고 있다.
        - 예) HTTP = port: 80, FTP = port: 21
    - 소켓들은 자신만의 포트번호를 할당받는다
    - 트랜스포트 계층은 세그먼트 안의 목적지 포트번호를 검사하고 일치하는 소켓으로 세그먼트를 보낼 수 있다.
    - 데이터는 소켓을 통해 해당되는 프로세스로 전달된다.
    - 이러한 작업을 하향식으로 다중화(multiplexing), 상향식으로 역다중화(demultiplexing) 라고 한다.


### TCP

---

- TCP  서비스 모델은 연결지향형 서비스와 신뢰적인 데이터 서비스를 포함한다
- 연결 지향형 서비스
    - 애플리케이션 계층 메시지를 전송하기 전에 TCP는 클라이언트와 서버가 서로 전송 제어 정보를 교환하도록 한다.
    - 핸드셰이킹 과정이 클라이언트와 서버에 패킷이 곧 도달할 것이니 준비하라고 알려주는 역할을 한다.
    - 핸드 셰이킹 단계를 지나면 TCP연결이 두 프로세스의 소켓 사이에 존재한다고 얘기한다.
    - 이 연결은 두 프로세스가 서로에게 동시에 메시지를 보낼 수 있기에 전이중 연결이라고 한다.
- 신뢰적인 데이터 전송 서비스
    - 통신 프로세스는 모든 데이터를 오류 없이 올바른 순서로 전달하기 위해 TCP에 의존한다.
    - TCP는  애플리케이션의 한쪽이 바이트 스트림을 소켓으로 전달하면, 손실하거나 중복되지 않게 수신 소켓으로 전달한다.
- 또한 TCP는 혼잡제어 방식, 즉 통신하는 프로세스의 직접 이득보다는 인터넷의 전체 성능 향상을 위한 서비스를 포함한다.
    - 네트워크가 혼잡상태에 이르면 프로세스 속도를 낮춘다.
    - 네트워크 대역폭을 공평하게 공유할 수 있게끔 제한한다.

- TCP 연결 과정
    - 세방향 핸드셰이크로 연결을 설정한다.
    - 프로세스는 소켓을 통해서 데이터의 스트림을 전달한다.
    - 데이터가 소켓을 통해 전달되면 이제 데이터는 클라이언트의 TCP에 맡겨진다.
    - TCP는 초기 세방향 핸드셰이크 동안 준비된 버퍼의 하나인 연결의 송신버퍼로 데이터를 보낸다.
    - 세그먼트의 크기(세그멘트 에서의 애플리케이션 계층 데이터의 크기)는 최대 세그먼트 크기(MSS)로 제한된다.
    - MSS는 일반적으로 로컬 송신 호스트에 의해 전송될 수 있는 가장 큰 링크 계층 프레임의 길이 (MTU)에 의해 일단 결정된다.
- 세그먼트 구조

    ![../../../../../public/assets/2021-07-21-Network-transport-layer/tcpheader.jpg](../../../../../public/assets/2021-07-21-Network-transport-layer/tcpheader.jpg)

포트 주소

- 0 ~ 65535 까지의 16비트 정수이다. 그중에서 0 ~ 1023까지의 포트 번호를 **잘 알려진 포트 번호**라고 하여 사용을 엄격하게 제안하고 있다

순서 번호 필드(sequence number field), 확인응답번호 필드(acknwledgement number field)

- 신뢰적인 데이터 전송 서비스 구현을 위한 순서 번호
- 메모리의 크기 만큼의 숫자를 갖는다
- 송신자에서 수신자로 가는 데이터 패킷의 순서번호를 붙이기 위해 사용된다. 수신자 패킷의 순서번호의 격차는 수신자로 하여금 손실된 패킷을 검사하게 한다.

윈도우 사이즈

- 수신자가 받아드려는 바이트의 크기를 나타내는 데 사용된다. 통신을 할 때 정해진 윈도우 사이즈 만큼을 한번에 전송하고 상대방이 처리했는지 확인 후에 다음 데이터를 전송한다.

플래그

- ACK 비트는 확인 응답 필드에 있는 값이 유용함을 가리킨다. 성공적으로 수신된 세그먼트에 대한 확인응답을 포함한다
- RST, SYN, FIN 비트는 연결 설정과 해체에 사용된다.
- PSH 비트가 설정될 때 이것은 수신자가 데이터를 상위 계층에 즉시 전달해야 한다는 것을 가리킨다
- URG 비트는 이 세그먼트에서 송신 측 위 계층 개체가 긴급으로 표시하는 데이터임을 가리킨다.

### UDP

---

- UDP는 비연결형이므로 두 프로세스가 통신을 하기 전에 핸드셰이킹을 하지 않는다.
- UDP는 비신뢰적인 데이터 전송 서비스를 제공한다.
- 메세지가 수신 소켓에 도착하는 것을 보장하지 않는다. 순서도 바뀔 수 있다.
- 혼잡제어 방식을 포함하지 않는다.
- 따라서 UDP의 송신 측은 데이터를 원하는 속도로 하위계층으로 보낼 수 있다.
- Destination Socket만 있다면 정보를 보낼 수 있기 때문에 비연결형
- 멀티플렉싱, 에러디텍팅을 한다
- 왜 udp는 차단되는가

    Why is UDP traversal problematic?
    For UDP flows, the first outgoing packet on a 5-tuple will be used by the firewall as a start-ofsession indicator. But UDP does not have an end-of-session indicator, so the firewall has only
    two ways to close a pinhole: timing out the pinhole after the interior host does not send traffic
    for several seconds or the interior host generates a fatal ICMP error. Because there is no
    reliable way to determine that a session is being stopped, the firewall has a much harder job. It
    could implement an ALG and be aware of whatever semantics are imposed by the higher-level
    code on top of UDP. It could also rely on a set of well know application servers to inform it of
    sessions as they start and end, but that suffers from many challenges like application servers
    hosted independently of the network on which they are used.
    Using an ALG, a firewall can determine when the call is terminated and close any dynamic
    mappings created for the media session. But the problem is session signaling between the
    WebRTC application running in the browser and the Web server could be using TLS, in which
    case the ALG no longer has access to the signaling. Moreover, WebRTC does not enforce a
    particular session signaling protocol to be used, so firewalls using ALGs would fail to inspect the
    signaling to identify the 5-tuple used for each media stream. Furthermore, the session signaling
    and the peer-to-peer media may traverse different Firewalls.
    In the absence of an ALG or help from application servers, there is no way to deterministically
    tell that this session has been idle for a while or has ended. The net effect is that a firewall that
    does a good job with general UDP traffic is much more resource intensive than the same
    functionality for a set of TCP sessions. “Resource intensive” strongly correlates to “expensive”
    and “brittle”. This tends to cause network operators to block all UDP traffic except for the
    protocols that they absolutely have to have.


![../../../../../public/assets/2021-07-21-Network-transport-layer/network_stack.jpg](../../../../../public/assets/2021-07-21-Network-transport-layer/network_stack.jpg)

![../../../../../public/assets/2021-07-21-Network-transport-layer/TCPIP.jpg](../../../../../public/assets/2021-07-21-Network-transport-layer/TCPIP.jpg)
