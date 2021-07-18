---
layout: post
title:  "Network Packet"
date:   2021-06-29 23:10:12 +0900
categories: CS
overview: "네트워크 패킷 분석"
---


같은 내용으로 구현한 페이지를 Client Side Rendering과 Server Side Rendering으로 나눠서 구현한 경우의 패킷을 분석해봤다.
결과적으로 크게 다른 점이 없었다. Line Based text의 경우와 etag의 차이가 있었지만 네트워크 패킷의 차이는 없었다.

## WireShark 패킷 분석

---

CSR 패킷의 전문

```
Frame 152786: 364 bytes on wire (2912 bits), 364 bytes captured (2912 bits) on interface \Device\NPF_{64C4933D-6B3A-4E7F-9021-131A950549F5}, id 0
Ethernet II, Src: EFMNetwo_2e:72:b0 (58:86:94:2e:72:b0), Dst: LCFCHeFe_2a:ef:65 (8c:8c:aa:2a:ef:65)
Internet Protocol Version 4, Src: 13.124.193.201, Dst: 192.168.0.2
Transmission Control Protocol, Src Port: 8500, Dst Port: 52885, Seq: 925, Ack: 1893, Len: 310
Hypertext Transfer Protocol
Line-based text data: text/html (1 lines)

```

SSR 패킷의 전문

```
Frame 85797: 497 bytes on wire (3976 bits), 497 bytes captured (3976 bits) on interface \Device\NPF_{64C4933D-6B3A-4E7F-9021-131A950549F5}, id 0
Ethernet II, Src: EFMNetwo_2e:72:b0 (58:86:94:2e:72:b0), Dst: LCFCHeFe_2a:ef:65 (8c:8c:aa:2a:ef:65)
Internet Protocol Version 4, Src: 13.124.193.201, Dst: 192.168.0.2
Transmission Control Protocol, Src Port: 8200, Dst Port: 60181, Seq: 1, Ack: 461, Len: 443
Hypertext Transfer Protocol
Line-based text data: text/html (1 lines)

```

### Hypertext Transfer Protocol (HTTP)

---

HTTP로 전달되는 패킷의 내용은 HTTP Message와 Entity 부분으로 나눠진다.

앞으로 적을 스니펫에서는 같은 행의 CSR 방식과 SSR 방식을 비교하여 기술하고 같을 경우는 하나의 방식만을 적을 것이다.

[HTTP 완벽가이드 - ch15. 엔터티, 인코딩 (Entities and Encoding)](https://www.slideshare.net/cinari4/http-ch15-entities-and-encoding)

**HTTP Message**

```
Hypertext Transfer Protocol
    HTTP/1.1 200 OK\r\n
        [Expert Info (Chat/Sequence): HTTP/1.1 200 OK\r\n]
            [HTTP/1.1 200 OK\r\n]
            [Severity level: Chat]
            [Group: Sequence]
        Response Version: HTTP/1.1
        Status Code: 200
        [Status Code Description: OK]
        Response Phrase: OK
    Server: nginx/1.14.0 (Ubuntu)\r\n
    Date: Wed, 30 Jun 2021 05:16:16 GMT\r\n
```

- HTTP version: response의 HTTP version을 알려준다
- status code: 요청에 알맞는 3자리 숫자를 반환한다

[HTTP 상태 코드 - HTTP MDN](https://developer.mozilla.org/ko/docs/Web/HTTP/Status)

- Response Phrase: status code에 정의되어 있는 뜻의 여부

**Entity**

HTTP Message를 컨테이너 라고 표현하면 Entity는 화물이다.

가공되지 않은 데이터를 담고 있다

```
Hypertext Transfer Protocol

    Content-Type: text/html; charset=UTF-8\r\n
    Transfer-Encoding: chunked\r\n
    Connection: keep-alive\r\n
    Content-Encoding: gzip\r\n
```

- Content-Type: 데이터 타입을 설명해준다
- charset: 리소스의 인코딩 방식을 설명해준다
- Transfer-Encoding: 사용자에게 entity를 안전하게 전송하기 위해 사용하는 인코
- Content-encoding: 압축 혹은 인코딩 여부를 설명한다
- Connetction: 전송이 완료된 후 네트워크 접속을 유지할지 말지 제어한다. keep-alive의 경우 Content-Length 혹은 chunked encoding이 필요하다
- chucked Transfer-Encoding: 데이터를 실시간으로 제공하여 동적인 컨텐츠를 사용하여 응답을 전송할 수 있다. 단점은 웹 브라우저가 콘텐츠 크기를 알지 못하기 때문에 적절한 다운로드 진행률 표시가 어렵다.

CSR의 경우에는 ETag가 추가되어 있었다.

```
ETag: W/"60c1b1db-12a"\r\n

특정 버전의 리소스를 식별하는 식별자이다. 웹 서버가 내용을 확인하고 변하지 않았으면, 웹 서버로 full 요청을 보내지 않기 때문에, 캐쉬가 더 효율적이게 되고, 대역폭도 아낄 수 있다.

```

- CSR과 SSR의 차이일까?
SSR인 경우에도 사용한다. 캐시 적중에 관한 문제인 것 같다

**Line based text data**

CSR의 경우 최초 접속시 JS및 Static file을 다운로드 받아서 클라이언트에서 사용한다

```
[truncated]<!DOCTYPE html><html><head><link href="/js/app.6029ce48.js" rel="preload" as="script"><link href="/js/chunk-vendors.42656474.js" rel="preload" as="script"></head><body><div id="app"></div><script src="/js/chunk-vendors.4265647
```

SSR의 경우 JS 및 Static file을 서버에서 렌더링하여 반환한다

```
<span style="color:black; font-size:40px"> server side rendering : page A</span>
```

### TCP 세그먼트

---

```
Transmission Control Protocol, Src Port: 8200, Dst Port: 61841, Seq: 190, Ack: 1021, Len: 443
    Source Port: 8200
    Destination Port: 61841
    [Stream index: 30]
    [TCP Segment Len: 443]
    Sequence Number: 190    (relative sequence number)
    Sequence Number (raw): 1961040345
    [Next Sequence Number: 633    (relative sequence number)]
    Acknowledgment Number: 1021    (relative ack number)
    Acknowledgment number (raw): 3356149293
    0101 .... = Header Length: 20 bytes (5)
    Flags: 0x018 (PSH, ACK)
    Window: 228
    [Calculated window size: 29184]
    [Window size scaling factor: 128]
    Checksum: 0x3234 [unverified]
    [Checksum Status: Unverified]
    Urgent Pointer: 0
    [SEQ/ACK analysis]
    [Timestamps]
    TCP payload (443 bytes)
```

- Source Port, Destination Port: 0 ~ 65535 까지의 16비트 정수이다. 그중에서 0 ~ 1023까지의 포트 번호를 잘 알려진 포트 번호라고 하여 사용을 엄격하게 제안하고 있다
- Sequence Number:  송신자에서 수신자로 가는 데이터 패킷의 순서번호를 붙이기 위해 사용되며, 메모리 크기 만큼의 숫자를 갖는다. 순서번호의 격차는 수신자로 하여금 손실된 패킷 검사에 도움이 된다.
- Flags: 8가지 종류의 값을 반환한다. (PSH, ACK, CWR, ECE, URG, RST, FIN)
- Acknowledgment number: Flags에 ACK 플래그가 설정된 경우, 이 필드의 값은 수신자가 예상하는 다음 시퀸스 번호이다. 이것은 모든 선행하는 바이트들의 수신에 대한 확인 응답이다. (Sequence Number + Memory)
- Window: window-size를 뜻하며, Sequence Number와 Acknowledgment number가 하나씩 오고 가는 stop-and-wati보다 빠른 처리를 위하여 한번에 window-size만큼의 Sequence Number를 보내고 그 패킷들의 전달이 확인되는 대로 윈도우를 옆으로 옮김으로서 그 다음 패킷을 전송하는 방식이다
- Checksum: 헤더 및 데이터의 에러 확인을 위해 사용되는 16비트 필드. 세그먼트의 16비트 워드단위를 1의 보수로 변환한 뒤 세그먼트의 데이터와 더한다.
- Urgent Pointer: 시퀸스 번호로부터의 오프셋을 나타낸다. 이 오프셋이 마지막 긴급 데이터 바이트를 알려준다

### IP 데이터그램

---

```
Internet Protocol Version 4, Src: 13.124.193.201, Dst: 192.168.0.2
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
    Total Length: 350
    Identification: 0xf7c8 (63432)
    Flags: 0x40, Don't fragment
    Fragment Offset: 0
    Time to Live: 46
    Protocol: TCP (6)
    Header Checksum: 0xc3e1 [validation disabled]
    [Header checksum status: Unverified]
    Source Address: 13.124.193.201
    Destination Address: 192.168.0.2
```

- Version: IPv4와 IPv6를 구별하는 필드이다
- Header Length: 헤더의 크기를 나타낸다.(헤더 + 데이터 = 데이터그램) 최소 20 ~ 60
- Differentiated Services Field: QOS에 대한 정보를 제공한다(IP, FTP를 구분하는데 유용하다)
- Total Lengh: 데이터그램의 전체길이의 Byte 단위를 표시한다
- Identification: 데이터그램을 단편화 했을 경우 식별자 번호를 검사해서 모든 조각을 수신했음을 확인한다.
- Flags: 데이터그램의 단편화에 대한 정보를 가지고 있다.
- Fragment Offset: 단편화된 조각이 분실되었는지 확인하기 위해, 원본 데이터에서 조각의 위치를 명시한다
- Time to Live: 네트워크에서 데이터그램이 무한히 순환하지 않도록 한다(라우팅 루프). 라우터가 데이터 그램을 처리할 때마다 감소하고 0이되면 폐기한다.
- Protocol: 이 필드는 전송 계층의 프로토콜을 명시한다. 6의 경우 TCP, 17은 UDP이다.
- Header Checksum: 라우터가 수신한 데이터그램의 비트 오류를 탐지하는 역할을 한다
- Source Address, Destination Address: 출발 IP와 목적지 IP를 명시한다.

### 이더넷

---

```
Ethernet II, Src: EFMNetwo_2e:72:b0 (58:86:94:2e:72:b0), Dst: LCFCHeFe_2a:ef:65 (8c:8c:aa:2a:ef:65)
    Destination: LCFCHeFe_2a:ef:65 (8c:8c:aa:2a:ef:65)
        Address: LCFCHeFe_2a:ef:65 (8c:8c:aa:2a:ef:65)
        .... ..0. .... .... .... .... = LG bit: Globally unique address (factory default)
        .... ...0 .... .... .... .... = IG bit: Individual address (unicast)
    Source: EFMNetwo_2e:72:b0 (58:86:94:2e:72:b0)
        Address: EFMNetwo_2e:72:b0 (58:86:94:2e:72:b0)
        .... ..0. .... .... .... .... = LG bit: Globally unique address (factory default)
        .... ...0 .... .... .... .... = IG bit: Individual address (unicast)
    Type: IPv4 (0x0800)

```

- EFMNetwo: 국민 공유기 iptime을 뜻합니다
- Address: 이더넷에서 사용하는 주소는 MAC 주소이다. MAC 주소를 이더넷 주소라고 칭하기도 한다.
- Type: 도착 어댑터에서 어댑터는 어떤 네트워크 계층 프로토콜로 데이터를 전달해야 할지 구분할 필요가 있다. 때문에 데이터링크 계층 프로토콜들은 자신만의 표준 타입 번호를 갖고 있다.
- LG bit, IG bit: 멀티캐스트 통신인지 유니캐스트 통신인지를 구별하는 숫자. 0일 경우 unicast 통신임을 나타낸다

[EUI [정보통신기술용어해설]](http://www.ktword.co.kr/abbr_view.php?m_temp1=5573)

- 네트워크 계층 5단계에 속하지 않는 ethernet 패킷은 무엇이고 프레임과의 차이는 뭘까

IP 패킷은 하나 이상의 라우터를 지나서 네트워크로 라우팅 되기 때문에, 필자의 경우 공유기라는 또 하나의 라우터를 경유하여 네트워크로 라우팅 되는 과정에서 IP와 frame 사이에 ethernet이 추가되었다.

### 프레임

---

```
Frame 152786: 364 bytes on wire (2912 bits), 364 bytes captured (2912 bits) on interface \Device\NPF_{64C4933D-6B3A-4E7F-9021-131A950549F5}, id 0
    Interface id: 0 (\Device\NPF_{64C4933D-6B3A-4E7F-9021-131A950549F5})
    Encapsulation type: Ethernet (1)
    Arrival Time: Jun 30, 2021 14:16:16.728778000 대한민국 표준시
    [Time shift for this packet: 0.000000000 seconds]
    Epoch Time: 1625030176.728778000 seconds
    [Time delta from previous captured frame: 0.000629000 seconds]
    [Time delta from previous displayed frame: 0.000629000 seconds]
    [Time since reference or first frame: 581.043179000 seconds]
    Frame Number: 152786
    Frame Length: 364 bytes (2912 bits)
    Capture Length: 364 bytes (2912 bits)
    [Frame is marked: False]
    [Frame is ignored: False]
    [Protocols in frame: eth:ethertype:ip:tcp:http:data:data-text-lines]
    [Coloring Rule Name: HTTP]
    [Coloring Rule String: http || tcp.port == 80 || http2]

```

- Interface id: 패킷을 캡처한 인터페이스의 번호
- Encapsulation type: 패킷의 캡슐화 방식입이다. whireshark에서 사용하는 방식이다.
- Epoch Time: 1970년 1월 1일부터의 시간.
- Epoch Time은 무엇일까?

[What is a Unix timestamp and why use it?](https://stackoverflow.com/questions/20822821/what-is-a-unix-timestamp-and-why-use-it)

Time since reference or first frame: SSR = 581 CSR = 81

SSR의 경우 첫 프래임을 받는 시간이 길지만 CSR의 경우 상당히 짧다. 하지만 프레임을 받는 시간은 사용자 경험적인 측면에서 크게 상관이 없다.