## Index

1. [웹 브라우저에 URL을 입력했을 때의 수행 과정](#웹-브라우저에-URL을-입력했을-때의-수행-과정)
2. [HTTP 응답의 렌더링 과정](#HTTP-응답의-렌더링-과정)
3. [네트워크 프로토콜과 네트워크 주소들](#네트워크-프로토콜과-네트워크-주소들)
4. [URI vs URL vs URN](#URI-vs-URL-vs-URN)
5. [웹 캐시](#웹-캐시)

---

## 웹 브라우저에 URL을 입력했을 때의 수행 과정

<img width="735" alt="Screen Shot 2022-10-04 at 12 35 08 AM" src="https://user-images.githubusercontent.com/59877415/193618084-eb88fe78-3cad-441d-841b-7e5e9208565c.png">

1. 사용자가 `브라우저의 주소창`에 `URL을 입력`한다.
2. 브라우저는 해당 URL에 상응하는 IP 주소를 찾기 위해 DNS 기록 캐시를 확인한다.

> 👆 Yeogiseo jamkkan
>
> **DNS 기록 캐시는 무엇인가요?**
>
> DNS는 Domain Name System으로 웹사이트의 URL과 연결된 IP주소를 가지고 있는 데이터베이스입니다. 네트워크 트래픽을 통제하고 데이터 전송 시간을 줄이기 위해 DNS에 접근하지 않고도 빠르게 IP주소를 찾을 수 있도록 돕는 캐시입니다. browser, OS, router, ISP 등 여러 단계의 캐시로 구성되어 있습니다.

3. 요청한 URL이 캐시에 없다면 ISP의 DNS 서버는 DNS query를 보내어 해당 URL을 갖는 서버의 IP 주소를 찾는다.

> 👆 Yeogiseo jamkkan
>
> **ISP는 무엇인가요?**
>
> Internet Service Provider의 약자로 인터넷을 통한 데이터 전송을 제공하는 업체를 뜻합니다.
>
> ~~kt SK telecom U+~~

4. 브라우저는 서버와 TCP 통신을 한다.

> ✅ more information
>
> 연결은 TCP/IP three-way handshake를 통해 이루어진다.

5. 브라우저는 웹 서버에 HTTP 요청을 보낸다.

> ✅ more information
>
> 페이지에 대해서 묻는다면 GET 요청을, 증명 정보를 입력하거나 폼을 제출한다면 POST 요청을 보낼 것이다. 해당 요청에는 브라우저 식별 정보, TCP 연결 유지를 요청하는 헤더 등의 정보를 담고 있다.

6. 서버는 요청을 처리하고 응답을 다시 보낸다.

7. 서버는 HTTP 응답을 내보낸다.

> ✅ more information
>
> 서버는 요청한 페이지와 상태코드, 인코딩 방식, 캐싱 방법, 프라이빗 정보등을 담아서 응답을 보낸다.

8. 브라우저는 컨텐츠를 표시한다.

> ✅ more information
>
> 브라우저는 HTML의 뼈대만 렌더링한 뒤, HTML의 태그를 확인하여 이미지, CSS stylesheet, JavaScript 파일과 같은 추가적인 요소에 대한 get 요청을 보내어 받고 표시한다. (static한 파일은 브라우저에 의해 캐시되기 때문에 다음에 동일한 페이지를 방문했을 때 다시 fetch해오지 않아도 된다.)

---

## HTTP 응답의 렌더링 과정

<img width="521" alt="Screen Shot 2022-10-04 at 1 01 18 AM" src="https://user-images.githubusercontent.com/59877415/193623791-48b606d9-3af3-4577-9d27-73c67a713bad.png">

### HTTP 렌더링이란?

화면에 표시할 `웹 페이지`를 만드는 것으로 `HTML, CSS, 자바스크립트` 등 개발자가 작성한 문서가 `브라우저에 출력되는 과정`을 의미한다.

### 렌더링 과정

1. 서버에서 HTML을 받아온다.
2. HTML을 구조별로 분류하여 DOM 트리를 생성한다.
3. CSS 파일과 스타일 요소를 분류하여 CSSOM 트리를 생성한다.
4. 트리들의 요소들의 크기와 위치를 계산한다.
5. 계산된 크기와 위치에 맞게 화면에 출력한다.

> 👆 Yeogiseo jamkkan
>
> **누가(무엇이) 렌더링을 해주나요?**
>
> 모든 웹 브라우저는 렌더링 엔진과 자바스크립트 엔진을 가지고 있습니다. HTML, CSS 문서는 렌더링 엔진이, 자바스크립트 코드는 자바스크립트 엔진이 읽어내 렌더링합니다.

---

## 네트워크 프로토콜과 네트워크 주소들



### 네트워크 프로토콜

**HTTP**

인터넷에서 하이퍼 텍스트 문서를 전송하기 위해 사용되는 프로토콜 (서버/클라이언트 모델을 따라 데이터를 주고 받기 위한 프로토콜)

> 👆 Yeogiseo jamkkan
>
> **하이퍼 텍스트란?**
>
> 참조(하이퍼링크)를 통해 독자가 한 문서에서 다른 문서로 즉시 접근할 수 있는 텍스트를 의미합니다.

**HTTPS**

HTTP에 데이터 암호화가 추가된 프로토콜

> ✅ more information
>
> HTTPS는 대칭키 암호화 방식과, 비대칭키 암호화 방식을 모두 사용한다.

**FTP**

컴퓨터 사이의 원활한 파일 전송을 위해 사용되는 프로토콜

> ✅ more information
>
> 보안을 향상시키기 위해 TLS와 결합한 FTPS와 SSH와 결합한 SFTP가 있다.

**DNS**

DNS 서버에 도메인 이름 - IP 주소를 매핑시켜서 데이터베이스를 유지하고 있다가 요청이 들어오면 변환 정보를 내보내는 프로토콜

**ARP**

IP 주소를 물리적 네트워크 주소로 대응시키기 위해 사용되는 프로토콜



### 네트워크 주소

**MAC 주소**

하드웨어 주소로 컴퓨터의 물리적 주소를 의미한다.

**IP 주소**

소프트웨어 주소로 네트워크 주소를 의미한다.

---

## URI vs URL vs URN

| <img width="598" alt="Screen Shot 2022-10-03 at 11 39 09 PM" src="https://user-images.githubusercontent.com/59877415/193605177-1a601188-582b-4bbf-af98-d410ad259737.png"> | URI는 인터넷의 자원을 식별할 수 있는 문자열을 의미한다.<br /><br />그 중 어떠한 표준을 지켜서 자원을 식별하는 문자열을 URL, URN이라고 한다. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |



### URI (Uniform Resource Identifier)

- `통합 자원 식별자`로 `인터넷에 있는 자원을 고유하게 식별하고 위치를 지정`한다.



### URL (Uniform Resource Locator)

- `네트워크상에서 웹 페이지, 이미지, 동영상 등 파일이 위치한 정보`를 나타낸다.
- FTP, SMTP 등 다른 프로토콜에서도 사용할 수 있다.

```
http://naver.com - 네이버 사이트의 URL
http://img.naver.net/static/www/dl_qr_naver.png - 네이버 앱 QR 코드의 이미지에 대한 URL
```



### URN (Uniform Resource Name)

- `이름으로 자원을 특정`하여 가리킨다.
- 흔히 사용되지 않는다.

```
urn:ietf:rfc:2141 - 'RFC 2141' 문서
```



> 👆 Yeogiseo jamkkan
>
> **URN은 왜 필요할까요?**
>
> URL은 주소이지 실제 이름이 아니라서 특정 시점에 위치한 곳을 알려줍니다. 그래서 리소스의 위치가 옮겨지면 이전의 URL로는 해당 자원을 찾을 수 없게 됩니다. 이러한 문제를 예방할 수 있는 효과적인 방법은 해당 자원의 실제 이름을 사용하는 것으로 URN의 필요성이 대두됩니다.



> 🤔 Let's think about it together
>
> **URL도 아니고 URN도 아닌 URI가 분명 있을텐데 그 예시를 찾기가 어려웠어요.**
>
> 함께 찾아봐요.



### URL 구조

URL은 `프로토콜` :// `호스트 주소` : `포트 번호` / `경로` ? `쿼리` 로 구조화된다. 예를 통해 살펴보자.

```
https://www.youtube.com/watch?v=RUjG1et23ZM
```

`https` - 프로토콜

`www.youtube.com` - 호스트 주소

`watch` - 경로

`v=RUjG1et23ZM` - 쿼리



> 👆 Yeogiseo jamkkan
>
> **포트 번호는 어디로 갔나요?** 평소에도 URL에서 포트 번호를 잘 보지 못한것 같은데요?
>
> HTTP의 경우 80번, HTTPS의 경우 443번 포트 번호가 기본으로 적용됩니다. 그래서 위의 주소는 `https://www.youtube.com:443/watch?v=RUjG1et23ZM` 로 봐도 무방합니다.

---

## 웹 캐시



### 캐시의 필요성

웹 브라우저가 서버에 계속해서 같은 자원을 요청하는 것은 효율적이지 않다. 같은 데이터를 계속해서 네트워크를 통해 다운받는 것은 인터넷 네트워크가 메모리나 디스크에 비해 상대적으로 느리고 비싼 자원이기 때문이다.



### 캐시

<img width="668" alt="Screen Shot 2022-10-04 at 1 57 14 AM" src="https://user-images.githubusercontent.com/59877415/193635184-a11122e7-9f2b-42b7-965b-deeb9b31259a.png">

- 캐시를 적용하기 위해서는 서버에서 캐시와 관련된 설정을 해주어야 한다. HTTP 응답 메세지의 헤더에 cache-control이라는 속성을 추가하게 되는데 해당 속성을 캐시가 유효한 시간을 의미한다. 위의 예시에서는 60초동안 star.jpg 파일의 캐시가 유효하게 된다.

- 유효 시간 안에 같은 파일을 재요청하게 되면 서버로 통신하지 않고 브라우저 캐시에서 데이터를 가져오게 되어 비싼 네트워크의 사용량을 줄이고 브라우저 로딩 속도를 높여 사용자 경험을 향상시킬 수 있다.



### 캐시의 개선

유효 시간이 만료되더라도 클라이언트와 서버가 가진 데이터가 똑같다면 똑같은 데이터를 서버에서 가져오는 것은 비효율적이지 않을까?  `검증 헤더와 조건부 요청`을 사용해서 캐시를 개선할 수 있다.



**Last-Modified, If-Modified-Since**

<img width="564" alt="Screen Shot 2022-10-04 at 2 00 22 AM" src="https://user-images.githubusercontent.com/59877415/193635776-b8c2bf89-e92e-43cc-9262-818fdee520cc.png">

- 서버에서 응답 메세지를 만들 때 헤더에 데이터가 마지막에 수정된 시간을 의미하는 `Last-Modified` 속성을 추가해야 한다. 캐시에는 `캐시의 유효 시간과 최종 수정 시간`을 함께 기록한다.
- 캐시의 유효 시간 이내에 같은 데이터를 요청할 경우 캐시에 저장된 데이터를 사용한다.
- 캐시의 유효 시간이 지나서 데이터를 요청할 경우 서버에 `if-modified-since` 속성에 최종 수정 시간을 담아서 보낸다. 서버에 보낸 최종 수정 시간과 서버에 저장된 최종 수정 시간이 일치하면 `304 not modified` 응답 메세지가 전송된다. 이경우 캐시의 데이터는 그대로 사용하고 헤더 데이터만 갱신된다.

- 비슷한 방법으로 `ETag, If-None-Match` 가 있다.

---

## References



[[네트워크/기본] URI, URL 및 URN의 차이점](https://hanamon.kr/네트워크-기본-url-uri-urn-차이점/)

[URI vs URL vs URN :: 마이구미](https://mygumi.tistory.com/139)

[[ 웹(WWW) 기본 개념 ] URL, HTTP, IP, 도메인, 포트](https://all-young.tistory.com/20)

[What happens when you type a URL in the browser and press enter?](https://medium.com/@maneesha.wijesinghe1/what-happens-when-you-type-an-url-in-the-browser-and-press-enter-bb0aa2449c1a)

[HTTP 렌더링 이란?](https://velog.io/@kimxdaxsom/HTTP-렌더링-이란)

[[네트워크] 프로토콜 종류](https://helloworld-88.tistory.com/146)

[IP주소와 맥주소](https://jhnyang.tistory.com/404)

[HTTP Header - 캐시와 조건부 요청](https://www.notion.so/HTTP-Header-0a285f87134a453291d585ff9d21fb04)

---

