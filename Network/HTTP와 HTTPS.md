## HTTP vs HTTPS, SSL/TLS 동작방식, HSTS

## 목차

1. [HTTP vs HTTPS](#http-vs-https)
    - [HTTP](#http)
    - [HTTPS](#https)
    - [HTTPS의 동작 과정](#https의-동작-과정)
    - [SSL 인증서 발급 과정](#ssl-인증서-발급-과정)
1. [TLS(Transport Layer Security)](#tlstransport-layer-security)
    - [제공하는 보안서비스](#제공하는-보안서비스)
    - [TLS는 어떻게 동작?](#tls는-어떻게-동작)
1. [TLS 핸드셰이크](#tls-핸드셰이크)
    - [TLS 핸드셰이크 중에는 어떤 일이 발생?](#tls-핸드셰이크-중에는-어떤-일이-발생)
    - [TLS 핸드셰이크가 이루어지는 단계]()
        - [Client Hello](#client-hello)
        - [Server Hello](#server-hello)
        - [서버 인증 단계(Server certificate)](#서버-인증-단계server-certificate)
        - [Client key exchange(pre-master-secret 전달)](#client-key-exchangepre-master-secret-전달)
        - [서버가 개인 키 사용하여 복호화](#서버가-개인-키-사용하여-복호화)
        - [세션 키 생성](#세션-키-생성)
        - [클라이언트 준비 완료](#클라이언트-준비-완료)
        - [서버 준비 완료](#서버-준비-완료)
        - [HTTPS 통신 시작](#https-통신-시작)
    - [Abbreviated TLS handshake](#abbreviated-tls-handshake) 
1. [HTTPS를 적용하면 100% 안전?](#https를-적용하면-100-안전)
1. [HSTS(HTTP Strict Transport Security)](#hstshttp-strict-transport-security)

## HTTP vs HTTPS

### HTTP

- HTTP는 애플리케이션 레벨의 프로토콜로 TCP/IP 위에서 작동
- 80번 포트 사용
- HTTP는 상태를 가지고 있지 않는 Stateless 프로토콜이며 Method, Path, Version, Headers, Body 등으로 구성된다.
- HTTP는 `암호화가 되지 않은 평문 데이터를 전송하는 프로토콜`이기에, HTTP로 비밀번호나 주민등록번호 등을 주고 받으면 제3자가 정보를 조회할 수 있다.
- 그리고 이러한 문제를 해결하기 위해 HTTPS가 등장하게 되었다.

<img width="600" alt="스크린샷 2022-10-02 오후 9 45 09" src="https://user-images.githubusercontent.com/75410527/193580612-7a731166-5130-46c4-b956-f2800203c140.png">

### HTTPS 

- HTTP에 데이터 암호화가 추가된 프로토콜
  - SSL 프로토콜 위에서 동작하는 HTTP
  - HTTP를 기반으로 웹서버와 통신을 하되 암호화 통신을 위한 별도의 협의 과정을 거친다는 것을 의미
    - 그 협의 과정에 SSL 프로토콜을 사용하여 데이터를 암호화
  - 443번 포트 사용 
- HTTPS는 소켓 통신에서 `일반 텍스트를 이용하는 대신`에, SSL/TLS 프로토콜을 통해 `데이터를 암호화`한다. 따라서 데이터의 보호를 보장한다.
- 기존 HTTP는 TCP와 직접 통신하지만 HTTPS는 HTTP는 SSL/TLS와 통신함.

> HTTP: 내용을 전송할때 HTTP프로토콜을 사용   
> SSL: 내용물을 다른사람이 못보게 하는 과정   
> TCP: 내용물을 어떻게 전송할지?   

### HTTPS의 동작 과정

- HTTPS는 `대칭키 암호화 방식`과 `공개키 암호화 방식`을 모두 사용
- HTTPS 연결 과정(TLS Handshaking)에서는 먼저 공개키 암호화 방식을 사용해 클라이언트-서버간 세션키 교환
- 세션키 교환이후 데이터 송/수신 과정에서는 세션키를 사용한 대칭키 암호화 방식으로 데이터를 주고 받음

> 세션키: 주고받는 데이터를 암호화하기 위해 사용되는 대칭키

<img width="400" alt="스크린샷 2022-10-02 오후 9 45 09" src="https://user-images.githubusercontent.com/75410527/193591799-13826e10-c41f-465d-81da-8170d1723a06.png">

### SSL 인증서 발급 과정

서버는 클라이언트와 세션키를 공유를 위한 공개키를 생성해야 하는데, 일반적으로 인증된 기관(Certificate Authority)에 공개키를 전송하여 인증서를 발급받음 

1. A기업은 HTTP 기반의 애플리케이션에 HTTPS를 적용하기 위해 공개키/개인키를 발급함
2. CA 기업에게 돈을 지불하고, 공개키를 저장하는 인증서의 발급을 요청함
3. CA 기업은 CA기업의 이름, 서버의 공개키, 서버의 정보 등을 기반으로 인증서를 생성하고, CA 기업의 개인키로 암호화하여 A기업에게 이를 제공함
4. A기업은 클라이언트에게 암호화된 인증서를 제공함
5. 브라우저는 CA기업의 공개키를 미리 다운받아 갖고 있어, 암호화된 인증서를 복호화함
6. 암호화된 인증서를 복호화하여 얻은 A기업의 공개키로 세션키를 공유함

<img width="600" alt="스크린샷 2022-10-02 오후 9 45 09" src="https://user-images.githubusercontent.com/75410527/193593334-94c8a753-d4d8-4a28-948f-8ca8b4f77f2d.png">

- 인증서는 CA의 개인키로 암호화되었기 때문에, 신뢰성을 확보할 수 있고, 클라이언트는 A 기업의 공개키로 데이터를 암호화하였기 때문에 A기업만 복호화하여 원본의 데이터를 얻을 수 있다. 
- 여기 인증서에는 A 기업의 공개키가 포함되어 있으므로, 인증서는 A 기업의 공개키라고 봐도 무방하다. 
- 브라우저에는 인증된 CA 기관의 정보들이 사전에 등록되어 있어 인증된 CA 기관의 인증서가 아닐 경우에는 다음과 같은 형태로 브라우저에서 보여지게 된다.

![다운로드 (1)](https://user-images.githubusercontent.com/75410527/193594927-d36f875d-f885-4e7d-96ed-81c23270fab7.png)

## TLS(Transport Layer Security)

- TLS는 안전한 인터넷 통신을 위한 `암호화 프로토콜`
- TLS의 주요 사용 사례는 웹 사이트를 로드하는 웹 브라우저와 같이 `웹 응용 프로그램과 서버 간의 커뮤니케이션을 암호화`하는 것

> ☝️여기서 잠깐!    
> SSL은 TLS 프로토콜의 바로 이전 버전    
> TLS는 Netscape가 개발한 SSL(Secure Sockets Layer)이라고 불리는 이전의 암호화 프로토콜에서 발전한 것    
> TLS 버전 1.0은 SSL 버전 3.1로서 개발을 시작했지만 Netscape와 더 이상 연관이 없음을 명시하기 위해 발표 전에 프로토콜의 이름이 변경되었음.     
> 다만 이러한 역사 때문에 용어 TLS와 SSL은 가끔 서로 바꿔서 사용함      

- TLS는 전송 계층(Transport Layer) 위에서 TLS 계층을 따로 두어 동작하게 됨. TLS를 사용하는 애플리케이션 프로토콜은 끝에 S가 붙는다.
- TLS 기반의 HTTP는 HTTPS라고 지칭. TLS 기반의 FTP는 FTPS라고 부른다.

<img width="700" alt="스크린샷 2022-10-03 오후 8 59 27" src="https://user-images.githubusercontent.com/75410527/193571726-80fcd357-f320-46ae-a798-e2877cbafa5b.png">

- TLS의 세부 프로토콜은 다음과 같습니다.

| 프로토콜           | 상세 설명                                                                                         |
|--------------------|---------------------------------------------------------------------------------------------------|
| Handshake          | 양쪽 간에 연결을 설정할때 보안 협상을 위한 프로토콜                                               |
| Change Cipher Spec | 보안 파라미터를 변경하거나 적용할때 사용. 예를 들어 대칭키 알고리즘을 변경할때 이 프로토콜을 사용 |
| Alert              | 오류를 전송할때 사용되는 프로토콜                                                                 |
| Application Data   | 실제 데이터가 전송될때 사용되는 프로토콜                                                          |
| Record             | 협상된 보안 파라미터를 이용하여 암,복호화, 무결성 검증 등을 수행하는 프로토콜                     |

### 제공하는 보안서비스

1. 기밀성
    - 대칭키 암호화 방식을 사용하게 되면 기밀성을 제공할 수 있습니다.
2. 무결성
    - 메시지 인증 코드(`MAC: Message Authentication Code`)를 통해 메시지 인증을 제공합니다. `위/변조 여부 확인` 가능
3. 인증
    - 연결 초기 설정에서 주고 받는 인증서를 통해 신뢰할 수 있는 서버인지 인증가능

#### MAC: Message Authentication Code

- 상대방이 보낸 암호화데이터가 변조된 것인지, 아닌지를 판단하는 역할
- `메시지 + 대칭키`를 MAC 알고리즘을 적용하여 `MD(Message Digest)` 생성 후 메시지와 함께 보냄
- 수신자는 전달받은 메시지를 대칭키와 함께 MAC 알고리즘을 적용시켜 MD 생성 후 자신이 받은 MD와 일치여부 검증
- 이 과정을 통해 위/변조 여부까지 확인가능

> MD(Message Digest): 단방향 암호화 해싱 함수가 적용된 숫자  

 <img width="700" alt="스크린샷 2022-10-03 오후 9 15 19" src="https://user-images.githubusercontent.com/75410527/193574308-ab64a60a-1610-4285-9678-0c8141bf0ca6.png">


### TLS는 어떻게 동작?

- 웹 사이트나 응용 프로그램이 TLS를 사용하기 위해서는 원본 서버에 `TLS 인증서`가 설치되어 있어야 함
  > 위에서 설명한 이름 혼동 때문에 이 인증서는 "SSL 인증서"라고도 알려져 있음 
- 인증 기관(CA)이 도메인을 소유한 사람 혹은 비즈니스에게 TLS 인증서를 발행함 
  > 인증서는 `서버의 공개 키`와 더불어 `누가 도메인 소유자인지에 대한 중요한 정보`를 포함하며, 이 두 가지는 모두 서버의 신원을 확인하는 데 중요
- TLS 연결은 `TLS Handshake`를 사용하여 초기화됨
- 사용자가 TLS를 사용하는 웹 사이트를 돌아다니면 `사용자 장치(클라이언트 장치)`와 `웹 서버` 간에 TLS Handshake가 시작됨.

## TLS 핸드셰이크

- TLS 핸드셰이크는 TLS 암호화를 사용하는 통신 세션을 실행하는 프로세스
- 통신 양측은 TLS 핸드셰이크 중에 메시지를 교환하여 `서로를 인식`하고, `서로를 검증`하며, `사용할 암호화 알고리즘을 구성`하고, `세션 키를 합의`함

> TLS 핸드셰이크 발생시점    
> TLS 핸드셰이크는 사용자가 HTTPS를 통해 웹 사이트를 탐색하고 브라우저가 처음 해당 웹 사이트의 원본 서버를 쿼리하기 시작할 때마다 발생.   
> 다른 통신이 API 호출 및 HTTPS를 통한 DNS 쿼리를 포함하는 HTTPS를 사용할 때에도 항상 TLS 핸드셰이크가 발생    
> TLS 핸드셰이크는 TCP 핸드셰이크를 통해 TCP 연결이 열린 후에 발생

<img width="700" alt="스크린샷 2022-10-02 오후 10 20 22" src="https://user-images.githubusercontent.com/75410527/193456261-cdf5b29b-e3b4-4b1d-9d3f-e717aa7384b4.png">

### TLS 핸드셰이크 중에는 어떤 일이 발생?

TLS 핸드셰이크 과정중에는 `클라이언트와 서버`가 함께 다음 `네 가지를 수행`한다.

1. 사용할 `TLS 버전`(TLS 1.0, 1.2, 1.3 등)을 지정
1. 사용할 `암호화 알고리즘` 결정
1. 서버의 공개 키와 SSL 인증서 기관의 디지털 서명을 통해 `서버의 ID를 인증`한다.
1. 핸드셰이크가 완료된 후에 대칭 암호화를 사용하기 위하여 `세션 키를 생성`

### TLS 핸드셰이크가 이루어지는 단계


<img width="900" alt="스크린샷 2022-10-03 오후 8 15 16" src="https://user-images.githubusercontent.com/75410527/193564193-bf20b57a-b1e9-487c-bb63-094b28fd2a45.png">


#### Client Hello

> 브라우저 주소창에서 `blog.naver.com`을 치면 내 브라우저는 네이버 블로그 웹 서버에 접속을 시도한다.    
> HTTP는 TCP의 일종이니 3-Way Handshake를 수행한 브라우저는 네이버 블로그가 HTTPS를 사용하는 것을 알게되고     
> 브라우저는 아래 정보를 Client Hello 단계에서 보낸다.

- 클라이언트가 서버로 "헬로" 메시지를 전송하면서 핸드셰이크를 개시
- 이 메시지에는 아래 내용들이 포함되어 있다.
  1. 클라이언트(브라우저)가 지원하는 `TLS 버전`
  1. 클라이언트(브라우저)에서 사용가능한 `암호화 방식 모음(cipher suite)`
  1. 클라이언트가 생성한 임의의 난수(숫자)
  1. 만약에 이전에 TLS 핸드 셰이크가 완료된 상태라면, 그땐 생성된 세션 아이디(Session ID)

> ☝️여기서 잠깐!     
> cipher suite?        
>         
> 보안의 궁극적 목표를 달성하기 위해 사용하는 방식을 패키지 형태로 묶어놓은 것이명 보안의 목표란 다음과 같다.    

- 안전한 키교환
- 전달 대상 인증
- 암호화 알고리즘
- 메시지 무결성 확인 알고리즘

> 예를 들어, 이름이 TLS_RSA_WITH_AES_128_GCM_SHA256이면,    
> 키 교환 알고리즘은 RSA, 대칭키 알고리즘은 AES_128_GCM, Hash 알고리즘은 SHA256을 사용한다는 것    


![스크린샷 2022-10-03 오후 8 50 38](https://user-images.githubusercontent.com/75410527/193570271-df681673-08e1-4c63-ad82-4282c3e36c56.png)

#### Server Hello

- 클라이언트 헬로 메시지에 대한 서버의 응답
- 이 메시지에는 아래 내용들이 포함되어 있습니다.
  1. `서버의 공개키가 담긴 SSL 인증서`. 이 인증서는 CA의 비밀키로 암호화되어 발급된 상태 
  1. 클라이언트의 암호화 방식 모음 중에서, 서버가 지원하고 선택한 `암호화 방식(cipher suite)`
  1. 서버가 생성한 임의의 난수(숫자)

#### 서버 인증 단계(Server certificate)

> 대부분의 브라우저에는 공신력 있는 CA들의 정보와 CA가 만든 공개키가 이미 설치되어 있다.    
> 그래서 서버가 보낸 SSL 인증서가 정말 CA가 만든 것인지 확인하기 위해 내장된 CA 공개키로 암호화된 인증서를 복호화해본다.    

- 클라이언트가 서버로부터 받은 `SSL 인증서`를 `인증서 발행 기관을 통해 검증`한다. 
- 이를 통해 서버가 인증서에 명시된 서버인지, 그리고 클라이언트가 상호작용 중인 서버가 실제 해당 도메인의 소유자인지를 확인한다.
  - 정상적으로 복호화가 되었다면 CA가 발급한 것이 증명
  - 만약 등록된 CA가 아니거나, 등록된 CA가 만든 인증서처럼 꾸몄다면 이 과정에서 발각되며 브라우저 경고가 날아감 

> 브라우저에서 인증서 찾는 방법

<img width="400" alt="스크린샷 2022-10-02 오후 11 08 35" src="https://user-images.githubusercontent.com/75410527/193458388-7999ad38-5fdf-4a6e-9670-d22377d8c26a.png">

> 인증서 정보 조회

<img width="400" alt="스크린샷 2022-10-02 오후 11 08 47" src="https://user-images.githubusercontent.com/75410527/193458393-c0714ab8-d9b8-4892-b9dc-6a4e397fea27.png">

> 어떤 ROOT CA들이 브라우저에서 유효하다고 인정받고 있는지는 브라우저 - 설정 - 보안 메뉴에서 확인가능

<img width="400" alt="스크린샷 2022-10-02 오후 11 08 58" src="https://user-images.githubusercontent.com/75410527/193458396-e6aef202-c14a-4619-8c4c-38007f9666d3.png">

#### Client key exchange(pre-master-secret 전달)

- 브라우저가 `자신이 생성한 난수`와 `서버가 생성한 난수`를 사용하여 `pre-master-secret`이라고하는 무작위 바이트 문자열을 만든다.
- 그리고 pre-master-secret을 서버의 공개키로 암호화하여 서버로 전송한다.
  - 클라이언트는 서버의 `SSL 인증서를 통해 공개 키를 받습니다.`(인증서 안에는 서버의 공개키가 있다.)

> 세션키 교환에 필요한 정보를 서버에 제공

#### 서버가 개인 키 사용하여 복호화

- 서버는 자신의 개인키를 사용하여 브라우저가 보낸 `pre-master-secret`을 복호화한다.

#### 세션 키 생성

- 클라이언트와 서버가 모두 `클라이언트 random`, `서버의 random`, `pre-master-secret`를 이용해 세션 키를 생성한다. 
  - 세션 키는 클라이언트(브라우저)와 서버 간의 연결에 고유한 값을 부여하기 위한 값 

> 세션 키?    
> 
> 클라이언트(브라우저)와 서버간에 만들어진 연결에 고유한 값을 부여하기 위한 게 session key    
> 세션 키는 대칭키 암호화에 사용될 키    
> 이것으로 브라우저와 서버 사이에 주고 받는 데이터를 암호화하고 복호화    

#### 클라이언트 준비 완료

- 클라이언트가 세션 키로 암호화된 "완료" 메시지를 전송한다.

#### 서버 준비 완료

- 서버가 세션 키로 암호화된 "완료" 메시지를 전송한다.

#### HTTPS 통신 시작

- 핸드셰이크가 완료되고, `세션 키`를 이용해 `대칭키 암호화`를 사용하여 통신이 계속 진행된다.
  - 웹상에서 데이터를 세션 키를 사용하여 암/복호화하며 HTTPS 프로토콜을 통해 주고받을 수 있다. 
- HTTPS 통신이 완료되는 시점에서 서로에게 공유된 세션 키를 폐기한다.
- 만약 세션이 여전히 유지되고 있다면 브라우저는 SSL handshake 요청이 아닌 세션 ID만 서버에게 알려주면 된다.

### Abbreviated TLS handshake

- 축약형 TLS handshake
- 이전에 TLS 핸드 셰이크가 완료된 상태라면, 그땐 생성된 `세션 아이디(Session ID)`를 함께 Client Hello 메시지로 보내면 아래와 같이 축약형 TLS handshake으로 연결이 완료된다.

<img width="700" alt="스크린샷 2022-10-02 오후 11 08 58" src="https://user-images.githubusercontent.com/75410527/193619128-f23df3b0-104a-412b-961f-513a2faeacb4.png">

```
Client (with SessionID)                              Server

 ClientHello                  -------->
                                ServerHello (with SessionID)
                                          [ChangeCipherSpec]
                              <--------      ServerHelloDone
 [ChangeCipherSpec]
 Finished                     -------->
 Application Data             <------->     Application Data
```

## HTTPS를 적용하면 100% 안전?

- 웹 서버가 해킹의 다양한 공격에 의해 루트 권한을 탈취당했다면 모든 기밀 데이터를 열람할 수 있는 권한이 넘어갈 수 있음
- `중간자 공격(Man in the middle attack)`을 수행할 수 있는 취약점이 있다면 HTTPS는 유지되지만 전달하는 내용은 고스란히 노출됨
  - HTTPS는 전달 구간에 대한 보안 기술이기 때문

### Man in the middle attack

네트워크 통신을 조작하여 통신 내용을 도청하거나 조작하는 공격 기법


#### 예시 

- 갑이 을과 통신하려고 하는 상황에서, 병은 그 사이에 끼어들어 을에게 잘못된 메시지를 전달한다. 
- 먼저, 갑이 을에게 공개키(Public Key)를 요청한다. 
- 을이 갑에게 을의 암호 키를 보낼 때, 병이 이 사이에 끼어들 수 있다면 중간자 공격이 시작된다. 
- 병은 갑에게 자신의 암호 키를 보내며 갑이 받은 것은 을의 암호키라고 믿게한다.

> 갑 "안녕 을! 너의 암호 키 좀 줘!" → 병 을   
> 갑 병 "안녕 을! 너의 암호 키 좀 줘!" → 을   
> 갑 병 ← [을의 암호 키] 을   
> 갑 ←[병의 암호 키] 병 을   
> 갑 "버스 정류장에서 봐!"(병의 암호 키로 암호화한 암호문) → 병 을   
> 갑 병 "지하철 6번출구에서봐!"(을의 암호 키로 암호화한 암호문)→을   

<img width="300" alt="스크린샷 2022-10-02 오후 11 08 58" src="https://user-images.githubusercontent.com/75410527/193614956-88658acf-6f86-429d-befb-4944e96b11a9.png">

## HSTS(HTTP Strict Transport Security)

- Web Site에 접속할 때, 강제적으로 `HTTPS Protocol로만 접속하게 하는 기능`이다. 
  - 보안을 강화시킬 목적으로, Web Browser에게 HTTPS Protocol만 사용하도록 강제
- 즉, HTTPS Protocol을 지원하는 Web Site에서, 자신은 HTTPS만 사용해서 통신할 수 있음을, 접속하고자 하는 Web Browser에게 알려 주는 기능 
- HSTS를 사용하기 위해서는 `웹 서버와 브라우저 모두 기능을 지원해야 한다.`

### HSTS 기능을 사용하는 목적

> 리다이렉트 응답을 통한 HTTPS 강제 방법의 한계

- 웹 브라우저가 HTTP Protocol 로 특정 도메인에 접속을 시도하는 경우, 해당 도메인이 HTTPS 프로토콜만을 지원하는 웹사이트라면 ”301 Redirect” 또는 ”302 Redirect” 응답을 보내 웹 브라우저가 HTTPS로 다시 접속하라고 redirect 하여 `HTTPS를 통해 웹사이트와 통신`한다.
- 그러나 이 경우 해커와 같은 공격자가 중간자공격(MITM attack)을 하여 중간에 `Proxy Server`를 두고 사용자와는 HTTP 통신을 하고 실제 사이트와는 HTTPS 통신을 하는 문제가 발생할 수 있다. 
- 즉, 사용자가 실제 사이트와 주고 받는 모든 정보는 공격자에게 노출될 수 있다. 이러한 공격 방지하기 위하여 HSTS를 사용해야 한다.

> 즉 사용자가 실수로 HTTPS Protocol을 지원하는 Site를, HTTP Protocol로 접속 했을 때,    
> 중간자 공격에 의해, HTTP Protocol을 사용한 통신을 하게 되고, 이로 인해 통신 정보가 공격자에게 노출이 되는 것을 방지하고자 하는 목적          

### HSTS 기능의 동작

HSTS를 지원하는 Web Browser는 내부에 HSTS List를 보유하고 있다. 즉 HTTPS Protocol을 사용해야만 하는 Web Site에 대한 정보를 가지고 있다.

1. 클라이언트가 HTTP로 접속을 시도하는 경우, 먼저 브라우저에서 HSTS 설정된 도메인인지 확인(HTTP 요청 보내지 않음)
2. HSTS된 사이트에 대해 브라우저는 HTTPS로 서버에 요청한다. (중간자는 SSL로 암호화된 패킷을 도청할 수 없다.)
3. 서버가 HTTPS 요청에 대한 응답을 보낸다.
    - 웹서버는 HTTPS Reply Message에 HSTS Policy를 넣어서 보낸다.
4. 서버로 부터 받은 응답을 통해 HSTS List를 구성한다.

> HSTS를 사용하면 사용자가 입력한 도메인이 HSTS List에 있는 지 먼저 확인하여 HTTPS를 사용하여 사이트에 접속할 수 있다.     
> 만약 웹 브라우저가 HTTP Protocol로 웹 서버에 접속하고 HTTP 응답 메세지에 HSTS Policy 정보가 들어 있는 경우는 무시된다.    

#### HSTS List

- HSTS는 HTTPS로 웹 사이트에 접속하기 위해 적어도 한번 웹 서버와 통신을 해야하는데, 통신 전에 미리 HTTPS로 접속하도록 목록을 미리 만들어 놓은 것.
- `브라우저에서 지원하는 기능`이며, 구글 크롬이 주도하는 [HSTS Preload List](https://hstspreload.org/)가 주로 사용된다.
  - 크롬 뿐만 아니라 엣지, 사파리, 파이어폭스 등 다양한 브라우저도 이를 지원
- 해당 리스트에 사이트가 등록되면 이후 업데이트되는 브라우저는 등록된 도메인을 `무조건적으로 HTTPS로 연결`하며, HTTPS가 지원되지 않는 경우 연결 자체가 불가하게 된다.

### HSTS 적용방법

- HSTS는 웹서버에서 응답해주는 헤더에 최초 포함되어 있는 만큼 `웹서버에서 설정`해 주어야 한다. 
- 설정 방법은 HTTP 응답 헤더에 `Strict-Transport-Security`라는 필드를 내려주면 된다. 
- 해당 응답을 받은 브라우저는 그 사이트에 접속할 때 무조건 HTTPS로만 연결한다.
- HSTS response header 요소
  - max-age=31536000 : HSTS가 브라우저에 설정될 시간. 초단위로, 63072000은 2년을 의미
  - includeSubdomains : HSTS가 적용될 도메인의 서브 도메인에도 HSTS를 적용한다.
  - preload : 브라우저가 해당 사이트를 HSTS 적용 preload list에 추가하도록 한다.

```bash
# 예시
Strict-Transport-Security: max-age=63072000; includeSubDomains; preload
```


## References

- [HTTPS 통신과정 쉽게 이해하기 #1(HTTPS의 개요)](https://aws-hyoh.tistory.com/34)
- [TLS(Transport Layer Security)는 무엇입니까?](https://www.cloudflare.com/ko-kr/learning/ssl/transport-layer-security-tls/)
- [SSL이란 무엇입니까? | SSL 정의](https://www.cloudflare.com/ko-kr/learning/ssl/what-is-ssl/)
- [TLS 핸드셰이크란 무엇일까요?](https://www.cloudflare.com/ko-kr/learning/ssl/what-happens-in-a-tls-handshake/)
- [TLS 핸드셰이크의 원리는 무엇일까요?  | SSL 핸드셰이크](https://www.cloudflare.com/ko-kr/learning/ssl/what-happens-in-a-tls-handshake/)
- [ROOT CA 인증서는 무엇인가?](https://brunch.co.kr/@sangjinkang/47)
- [HTTPS를 위한 SSL/TLS 핸드 셰잌으 작동원리](https://brunch.co.kr/@sangjinkang/38)
- [SSL/TLS #3, MAC(Message Authentification Code)](https://aws-hyoh.tistory.com/entry/SSLTLS-3-MACMessage-Authentification-Code)
- [[네트워크/보안] TLS(SSL) 개념과 기본 동작 원리](https://reakwon.tistory.com/106)
- [[Web] HTTP와 HTTPS의 개념 및 차이점](https://mangkyu.tistory.com/98)
- [HTTP와 HTTPS(HTTP Secure)](https://github.com/workhardslave/cs-study/blob/main/Network/HTTP_and_HTTPS.md)
- [중간자 공격](https://ko.wikipedia.org/wiki/%EC%A4%91%EA%B0%84%EC%9E%90_%EA%B3%B5%EA%B2%A9)
- [TLS Abbreviated Handshake](https://ldapwiki.com/wiki/TLS%20Abbreviated%20Handshake)
- [Change the (S)Channel! Deconstructing the Microsoft TLS Session Resumption bug](https://blog.cloudflare.com/microsoft-tls-downgrade-schannel-bug/)
- [HSTS Preload List](https://hstspreload.org/)
