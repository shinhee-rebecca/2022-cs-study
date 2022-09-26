# TCP UDP



## Internet Protocol Suite
`인터넷 프로토콜 스위트(Internet Protocol suit)`는 인터넷에서 컴퓨터들이 정보를 주고받는데 쓰이는 프로토콜의 집합이다. 대표적으로 `TCP/IP`의 4계층 모델로 설명하거나 `OSI` 7계층 모델이 있다.

<br>

- **TCP/IP** <Br>
> ☝️여기서 잠깐! <br>
>
> `TCP/IP`는 단순히 `TCP`와 `IP`만 일컫는 말이 아니라, 여러 가지 프로토콜의 조합을 의미한다. 다만 `TCP`와 `IP`가 가장 많이 사용되며 중요하기 때문에 그렇게 불리운다.

<br>

| 4    | **애플리케이션(응용)** **계층**                    | FTP, HTTP, SSH, SMTP, DNS … |
| ---- | ------------------------------------ | ------------------------------------------------------------ |
| 3    | **전송 계층**                   | TCP, UDP, DCCP, SCTP, IL, RUDP, …                            |
| 2    | **네트워크** **계층**                  | IP (IPv4, IPv6), ARP, RARP(주소 결정 프로토콜)|
| 1  | **데이터링크 계층**                              |  IEEE 802, SDLC, HDLC …                              |


<br>

- **OSI 7 계층**

> ☝️여기서 잠깐! <br>
>
> `OSI`계층은 `애플리케이션 계층`을 세 개로 쪼개고(애플리케이션 계층, 프레젠테이션 계층, 세션 계층), `링크 계층`을 두 개로 쪼개는(데이터 링크 계층, 물리 계층)것이 차이점이다.

<br>

| 7    | **애플리케이션(응용)** **계층**            | HTTP, SMTP, SNMP, FTP, 텔넷, SSH & Scp, NFS, RTSP            |
| ---- | ---------------------------- | ------------------------------------------------------------ |
| 6    | **프레젠테이션(표현)** **계층**            | XDR, ASN.1, SMB, AFP                                         |
| 5    | **세션** **계층**            | TLS, SSL, ISO 8327 / CCITT X.225, RPC, 넷바이오스, 애플토크  |
| 4    | **전송** **계층**            | TCP, UDP, RTP, SCTP, SPX, 애플토크                           |
| 3    | **네트워크** **계층**        | IP, ICMP, IGMP, X.25, CLNP, ARP, RARP, BGP, OSPF, RIP, IPX, DDP |
| 2    | **데이터** **링크** **계층** | 이더넷, 토큰`링, PPP, HDLC, 프레임 릴레이, ISDN, ATM, 무선랜, FDDI |
| 1    | **물리** **계층**            | 전선, 전파, 광섬유, 동축케이블, 도파관, PSTN, 리피터, DSU, CSU, 모뎀 |

<br>

---


## 전송(Transport layer) 계층

<br>

- OSI 7계층 중 4계층
- 호스트들 간에 통신을 제공.
- 역할
    1) 데이터가 제대로 도착했는지 확인
    2) 전송된 데이터의 목적지가 어떤 애플리케이션인지 식별
- 목적지에 신뢰할 수 있는 데이터를 전달하기 위해 필요
- 오류를 점검하는 기능이 있어 오류 발생 시 데이터를 재전송하도록 요청함

<br>
☝️전송 계층의 프로토콜인 TCP와 UDP를 비교해보자.

<br>

----


<br>