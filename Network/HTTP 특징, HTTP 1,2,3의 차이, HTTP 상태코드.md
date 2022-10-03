# HTTP

> HyperText Transfer Protocol
> 

웹 상에서 클라이언트와 서버 간에 `요청(request)` 와 `응답(response)` 으로 정보를 주고받을 수 있는 프로토콜.

<Br>

- Request의 예시

```
  GET <https://www.naver.com> HTTP/1.1								
  User-Agent: Chrome/5.0 (Windows NT 10.0; Win64; x64) ...			

```

- Response의 예시

```
  HTTP/1.1 200 OK														// 시작줄
  Connection: keep-alive												 // 헤더
  Content-Length: 21211
  Content-Type: text/html;

  <!DOCTYPE html><html><head><title...

```

<Br>
<Br>

# HTTP 특징

1. **서버-클라이언트 구조**

클라이언트가 서버에 요청을 보내면 서버는 그에 대한 응답을 보내는 클라이언트 서버 구조로 이루어져 있다.

<Br>

2. **비연결성(Connectionless)**

클라이언트가 어떠한 데이터를 요청하면 서버는 응답을 하고 한 번 맺었던 연결을 끊어버린다.

- 장점
    - 리소스를 아낄 수 있기 때문에 더 많은 연결을 할 수 있다.
- 단점
    - 매번 연결을 새로 맺기 때문에 시간이 발생한다.(3 way handshake)
    - 매번 새로운 연결을 해야하므로 `오버헤드`가 발생한다.
    
    > `오버헤드` 란 어떠한 처리를 하기 위해 들어가는 간접적인 처리 시간, 메모리를 말함.
    > 

<Br>

3. **무상태(Stateless)**

연결을 끊는 순간 서버는 클라이언트의 상태 정보를 유지하지 않는다.

- 장점
    - 서버의 확장에 용이하다(scale out). stateful은 특정 클라이언트를 특정 서버가 전담하는 식으로 대응한다. 따라서 동적으로 서버를 확장하기가 어렵다. 반면에 statelesss하다면 서버 호출 시 정보를 다 넘겨주기 때문에 문제가 발생해도 다른 서버를 호출해 처리가 가능하다. 따라스 스케일 아웃(같은 기능하는 서버군들의 수평 확장)에 유리하다.
- 단점
    - 클라이언트가 추가 데이터를 전송해야 한다.

<Br>
<Br>

# HTTP 버전

## HTTP 0.9 (1991년)

- 특징
    - 요청 method는 GET만 존재
    
    ```
    /* 요청 */
    GET /mypage.html
    ```
    
    - 응답도 단순. 파일 자체로만 구성됨.
    
    ```
    /* 응답 */
    <HTML>
    A very simple HTML page
    </HTML>
    ```
    
    - HTTP 헤더도 없고, HTML파일만 전송 가능했던 것이 특징

<br>


## HTTP 1.0(1996년)

- 특징
    - HTTP 헤더(`header`) 개념이 도입되어 요청과 응답에 추가
    - 버전 정보, 요청 method가 함께 전송되기 시작.
    - `Content-type` 도입으로 HTML이외의 문서 전송이 가능해짐
- 한계
    - `커넥션 하나` 당 `요청 하나` 와 `응답 하나` 만 처리가 가능. → HTTP 1.1에서 개선

```html
/* 요청 */
GET /mypage.html HTTP/1.0
User-Agent: NCSA_Mosaic/2.0 (Windows 3.1)
```

```html
/* 응답 */
200 OK
Date: Tue, 15 Nov 1994 08:12:31 GMT
Server: CERN/3.0 libwww/2.17
Content-Type: text/html
<HTML>
A page with an image
  <IMG SRC="/myimage.gif">
</HTML>
```

<br>

## HTTP 1.1(1997년)

- 특징
    - Persistent Connection : 지정한 timeout 동안 커넥션을 닫지 않아 속도가 빨라짐
    
    ![Untitled](./img/http1.0_http1.1.png)
    
    - 파이프라이닝(Pipelining) : 송신자가 다수의 패킷을 한 번에 보내는 것.
    
    ![Untitled](./img/pipelining.png)
    
    - 호스트헤더(Host Header)
- 단점
  - HOL (Head of Line) blocking: 특정 응답의 지연. 앞 응답이 오래걸리면 뒤 요청은 `Blocking`되어버림.
    ![Untitled](./img/hol.png)
  - 무거운 header 구조(ex. 쿠키)

<br><br>

## HTTP2.0(2015년)

- **Multiplexed Streams :** HTTP 1.1의 `HTTP Pipelining`의 개선안으로 하나의 Connection으로 동시에 여러 개의 메세지를 주고 받을 수 있음. 응답은 요청 순서에 상관없이 Stream으로 받기 때문에 `HOL Blocking`
 도 발생하지 않음

![img](./img/mutiplexed_streams.png)

- **Stream Prioritization : 응답에 대한 우선순위를 정해 우선순위가 높을수록 응답을 빨리함.**
- **Header Compression :** Header Table과 Huffman Encoding을 사용하는 HPACK 압축방식으로 이를 개선. 클라이언트와 서버는 각각 Header Table을 관리하고 이전 요청과 동일한 필드는 table의 index만 보내고, 변경되는 값은 Huffman Encoding 후 보냄으로서 Header의 크기를 경령화.

<br>
<br>


# HTTP 상태코드

클라이언트가 서버에 보낸 요청이 어떻게 처리되었는지 보여주는 코드.

## 1xx(Informational) : 요청이 수신되어 처리중

