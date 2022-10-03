# HTTP

> HyperText Transfer Protocol
> 

웹 상에서 클라이언트와 서버 간에 `요청(request)` 와 `응답(response)` 으로 정보를 주고받을 수 있는 프로토콜.

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