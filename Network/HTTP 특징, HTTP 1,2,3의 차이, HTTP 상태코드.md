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

