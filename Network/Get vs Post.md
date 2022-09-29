## Index

- [REST API](#REST-API-(Representational-State-Transfer-API))

  → 개념 / 구성 / 작동방식 / 특징

- [Get vs Post](#Get-vs-Post)

  → Get / Post

- [그외 메소드](#그외-메소드)

  → PUT / PATCH / DELETE / HEAD & OPTIONS & CONNECT & TRACE

- [스터디 질의 응답 정리](#스터디-질의-응답-정리)

  → 본문에서 확인할 수 있는 질문 / 추가 질문

- [References](#References)

---

## REST API (Representational State Transfer API)

### 1. 개념

두 컴퓨터 시스템이 인터넷을 통해 정보를 안전하게 교환하기 위해 사용하는 인터페이스

→ REST 기반의 아키텍처를 사용함으로써 대규모의 고성능 특징을 안정적으로 지원할 수 있다.

REST는 HTTP URI를 통해 자원을 명시하고 HTTP Method를 통해 해당 자원에 대한 CRUD Operation을 지원하는 것을 의미한다.

→ 웹 사이트의 이미지, 텍스트, DB 내용 등의 모든 자원에 고유한 ID인 HTTP URI를 부여한다.

### 2. 구성

자원에 대한 CRUD 요청을 특정한 형태로 구성하여 전달한다.

> **CRUD 요청**이란?
>
> <img width="774" alt="Screen Shot 2022-09-26 at 11 34 14 PM" src="https://user-images.githubusercontent.com/59877415/192303976-fa63bbd6-7da5-469f-8855-7c04435f5813.png">
>
> 데이터를 다룰 때 사용하는 4가지 요청으로 **Create, Read, Update, Delete**를 뜻한다.

- 메서드 (Method)

- 자원과 자원에 대한 표현 (Resource and Representation of Resource)

Method, Resource, Representation of Resource에 대한 자세한 설명은 RESTful API에 대한 설명이 끝나고 이어집니다.

### 3. 작동방식

1. 클라이언트가 서버에 요청을 전송한다. (이 때, 클라이언트는 API 문서에 따라 서버가 이해할 수 있는 형식에 맞춰 요청을 작성한다.)
2. 서버가 클라이언트를 인증하고 해당 요청을 수행할 수 있는 권한이 클라이언트에 있는지 확인한다.
3. 클라이언트의 권한이 확인되면 서버는 요청을 수신하고 내부적으로 처리한다.
4. 서버가 클라이언트에게 응답을 반환한다. (응답에는 요청을 성공했는지의 여부와 요청한 내용이 포함된다.)

### 4. 특징

1. Server - Client 구조

   → Server는 API를 제공하고 비즈니스 로직을 처리하고 Client는 사용자 인증이나 세션 정보 등을 직접 관리하고 책임짐으로써 서로 간의 의존도가 줄어든다.

2. Stateless (무상태)

   → Client의 상태를 Server에 저장하지 않으므로 구현이 단순하다.

   → Server는 각각의 요청을 별개의 것으로 인식하고 처리하므로 처리방식이 일관된다.

3. Cacheable (캐시 처리 가능성)

   → 웹 표준 HTTP를 그대로 사용하므로 웹에서 사용하는 캐싱 인프라를 그대로 사용할 수 있다. 캐시 사용을 통해 전체 응답 시간과 서버의 자원 이용률을 향상시킬 수 있다.

4. Layered System (계층화)

   → API 서버는 순수 비즈니스 로직 앞에 보안, 로드밸런싱, 암호화, 사용자 인증 등을 추가하여 확장성과 보안성을 확장시킬 수 있다.

5. Uniform Interface (일관성있는 인터페이스)

   → 인터페이스가 일관성이 있어서 HTTP 표준 프로토콜을 따르는 모든 플랫폼에서 사용이 가능하다.

---

## GET vs POST

### 1. GET

데이터를 읽거나 검색할 때 사용하는 메소드이다. GET 요청이 성공적으로 이루어진다면 서버는 XML이나 JSON 코드와 함께 200 HTTP 응답코드를 리턴한다.

<img width="834" alt="Screen Shot 2022-09-27 at 12 19 56 AM" src="https://user-images.githubusercontent.com/59877415/192315878-385eab86-6e4d-4bc4-801c-0f337eae23cc.png">

- 계속해서 호출하더라도 리소스를 변경시키지 않으므로 안전하다. 또한, 계속해서 호출하더라도 결과가 똑같으므로 멱등하다. 안전하기 때문에 캐싱을 사용할 수 있다.

  > HTTP 메소드의 속성
  >
  > 1. 안전 (Safe) : 메소드를 계속 호출하더라도 리소스를 변경하지 않는다는 뜻이다. 대표적으로 GET 메소드가 안전하다.
  > 2. 멱등 (Idempotent) : 메소드를 계속 호출하더라도 결과가 똑같다는 뜻이다. GET, PUT, DELETE 메소드는 멱등하지만 POST나 PATCH는 멱등하지 않다.
  > 3. 캐시 가능 (Cacheable) : 캐싱을 통해 데이터를 효율적으로 가져올 수 있다는 뜻이다. GET, HEAD, POST, PATCH가 캐싱이 가능하지만 실제로는 GET과 HEAD에서만 주로 캐싱이 쓰인다.

- 필요한 모든 데이터를 URL로 인코딩하여 요청한다.

  → URL에 넣을 수 있는 파라미터 데이터의 길이가 제한된다.

  → ASCII character만 허용된다.

  → 데이터가 URL의 일부로 전송되고 브라우저 히스토리에 저장되며 서버가 플레인 텍스트로 로그를 남기므로 POST에 비해 보안에 취약하다. (비밀번호와 같은 민감 정보를 전송하는데 사용해서는 안된다.)

- 캐싱을 하기 때문에 여러번 요청시 저장된 데이터를 사용하므로 다른 요청에 비해 속도가 빠를 수 있다.

### 2. POST

새로운 리소스를 생성할 때 사용하는 메소드이다. POST 요청이 성공적으로 이루어진다면 서버는 생성 결과와 자원의 경로, 201 HTTP 응답코드를 리턴한다.

<img width="847" alt="Screen Shot 2022-09-27 at 12 38 52 AM" src="https://user-images.githubusercontent.com/59877415/192320271-0a462bac-b1c4-4a7d-9374-5704507b9da8.png">

- 새로운 리소스를 생성하므로 안전하지 않다. 같은 요청을 여러번 했을 때 다른 결과를 받을 수 있으므로 멱등하지 않다. 따라서 캐싱을 사용할 수 없다.

- 새로운 리소스를 생성할 때 뿐 아니라 프로세스를 처리하거나 사용할 메서드를 특정하기 어려울 때에도 사용된다.

- 요청 파라미터들을 메세지 바디를 통해 서버로 전송한다.

  → GET에 비해 해킹하기 어렵다. (비밀번호와 같은 민감한 정보를 전송하는데 사용된다.)

  → 사용할 수 있는 타입에 제한이 없다. (binary data도 사용할 수 있다.)

  → URL에 보여지지 않는다.

- 캐싱이 불가능하다.

---

## 그외 메소드

### 1. PUT

기존 리소스가 있다면 대체하고 없다면 새로 생성할 때 사용하는 메서드이다.

> POST vs PUT
>
> POST의 경우에는 URI의 결정권이 서버에 있고 PUT의 경우에는 클라이언트에 있다. 이때문에 같은 요청을 반복하면 POST의 경우에는 데이터가 계속 생성되지만 PUT의 경우에는 그렇지 않다.
>
> > URI vs URL
> >
> > URI : 특정 리소스를 식별하는 통합 자원 식별자로 웹 기술에서 사용하는 논리적 또는 물리적 리소스를 식별하는 고유한 문자열 시퀀스이다. (URL을 포함하는 개념이다.)
> >
> > URL : 웹주소라고 불리며 컴퓨터 네트워크 상에서 리소스가 어디에 있는지 알려주기 위한 규약이다.
> >
> > <img width="750" alt="Screen Shot 2022-09-27 at 12 52 12 AM" src="https://user-images.githubusercontent.com/59877415/192323166-99515927-0812-4325-9b11-5ee78178e36c.png">

<img width="847" alt="Screen Shot 2022-09-27 at 12 52 48 AM" src="https://user-images.githubusercontent.com/59877415/192323297-098b8503-50c8-451b-b309-d259e5c927fc.png">

<img width="846" alt="Screen Shot 2022-09-27 at 12 53 38 AM" src="https://user-images.githubusercontent.com/59877415/192323491-1525b8f5-6fc2-4433-837f-896552720681.png">

- 위와 같은 상황이 발생할 수 있으므로 부분적인 변경이 필요할 때에는 PUT의 사용이 권장되지 않는다.

### 2. PATCH

기존에 존재하는 리소스를 부분적으로 변경할 때 사용하는 메서드이다.

> PUT vs PATCH
>
> PUT은 지정된 데이터를 전부 수정하지만 PATCH는 정보의 일부분을 변경한다. 따라서 PUT은 멱등하지만 PATCH는 멱등하지 않다. (PATCH 메서드가 이름을 happy로 바꾸는 경우 멱등하지만, 호출시마다 나이를 10씩 더하는 경우는 멱등하지 않다.)

<img width="847" alt="Screen Shot 2022-09-27 at 12 59 03 AM" src="https://user-images.githubusercontent.com/59877415/192324632-eeed061a-6618-45b6-a59d-f6d5bd12740a.png">

### 3. DELETE

기존에 존재하는 리소스를 삭제할 때 사용하는 메서드이다.

<img width="787" alt="Screen Shot 2022-09-27 at 1 10 55 AM" src="https://user-images.githubusercontent.com/59877415/192327136-c15171fa-0cce-49ef-b064-877f82ef8ac9.png">

### 4. HEAD & OPTIONS & CONNECT & TRACE

- HEAD: GET과 동일하지만 메시지 부분을 제외하고, 상태 줄과 헤더만 반환한다.

- OPTIONS: 대상 리소스에 대한 통신 가능 옵션을 전송한다.

- CONNECT: 대상 자원으로 식별되는 서버에 대한 터널을 설정한다.

- TRACE: 대상 리소스에 대한 경로를 따라 메시지 루프백 테스트를 수행한다.

  > 루프백 테스트란?
  >
  > 장치가 제대로 작동하는지 여부와 네트워크에 장애가 있는 노드가 존재하는지 확인하기 위해 수행한다.

---

## 스터디 질의 응답 정리

</br>

### 본문에서 확인할 수 있는 질문

1. REST api의 특징은 무엇인가요?
2. GET과 POST의 차이는 무엇인가요?
3. 비밀번호와 같은 민감한 정보가 포함된 데이터를 전송할 때 GET과 POST 중 어떤 메소드를 사용하는게 좋을까요?
4. PUT 메서드가 자원을 새로 생성하거나, 수정한다고 하셨는데 생성한다고 하면 멱등하지 않은 것 아닌가요?

</br>

### 추가 질문

1. 자원과 자원의 표현, 그리고 메소드에 대한 예시를 들어주세요.

> GET 메소드의 경우 읽고 싶은 자원의 위치를 URI를 통해서 나타냅니다. 또한 서버와 클라이언트는 JSON과 XML 등으로 자원을 주고 받습니다.

</br>

2. REST 는 무엇이고 RESTful하게 API를 디자인한다는 것은 무엇인가요?

>REST는 HTTP URI를 통해 자원을 명시하고 HTTP Method를 통해 해당 자원에 대한 CRUD Operation을 지원하는 것을 의미합니다. RESTful하게 API를 디자인한다는 것은 REST의 설계 규칙을 지켜서 API를 설계한다는 것을 의미합니다.

</br>

3. POST에서 바디에 비밀번호를 넣어서 보낸다고 하면, 해당 정보가 서버의 로그에 텍스트로 저장이 될텐데, 보안이 안되는 것 아닌가요?

> POST에서 메세지 바디에 비밀번호 값을 그대로 넣는 것이 아니라 암호화한 값을 넣어서 전송하면 보안이 강화될 것 같습니다. 

</br>

4. REST API의 단점은?

> 첫째, 표준이 존재하지 않는다는 단점이 있습니다. 둘째, 사용할 수 있는 메소드가 제한적입니다. 셋째, 인터넷 익스플로러와 같은 구형 브라우저에서 호환이 되지 않아서 지원해주지 못하는 동작이 많습니다.

</br>

5. REST 외 다른 인터페이스는?

> HTTP를 사용해서 프로그램끼리 소통하는 HTTP API가 있습니다. 누구나 제한 없이 사용 가능한 public API도 있습니다. 

</br>

6. RESTful API의 제약 조건은?

>첫째, 클라이언트-서버 구조를 준수해야 합니다. 클라이언트는 서버의 URI만 알뿐 이 둘은 비의존적이어야 합니다. 둘째, 서버는 클라이언트의 상태를 저장해서는 안됩니다. 로그인 등의 세션 정보는 클라이언트만이 알고 있어야 합니다. 셋째, 요청에 대한 응답에 해당 요청이 캐싱이 가능한지 가능하지 않은지의 정보를 포함해야 합니다. 넷째, 약속된 Interface를 준수해야 합니다. 다섯째, 계층화된 아키텍처여야 하며 각각의 계층은 마음대로 상호작용할 수 없습니다.

</br>

7. URL 과 URI 는 어떤 차이가 있나요? 구분해서 사용하는 이유는?

>URI는 특정 리소스를 식별하는 통합 자원 식별자입니다. URL은 컴퓨터 네트워크 상에서 리소스가 어디에 있는지를 알려주는 규약입니다. URL은 URI에 포함됩니다. 원하는 정보에 도달하기 위해서 URI이 유리한 상황도, URL이 유리한 상황도 있기 때문에 둘을 구분하여 사용합니다. 예를 들어서 www.google.com과 같이 서버의 위치를 나타낼 때에는 URL이면서 URI인 값을 사용하지만 서버의 자원을 나타낼 때에는 URI이지만 URL이 아닌 값을 사용하게 됩니다.

</br>

8. HTTP 프로토콜에서 서버가 내려주는 응답코드 아는대로 말해주세요.

>100번대는 정보 응답코드로 요청을 수신했으며 해당 요청을 처리하고 있음을 뜻합니다. 200번때는 성공 응답코드로 요청을 수신하고 처리했음을 뜻합니다. 300번대는 리다이렉션 응답코드로 요청을 위해 추가적인 조치가 필요함을 의미합니다. 400번대는 클라이언트 오류 응답코드로 요청의 문법이 잘못되었거나 요청을 처리할 수 없음을 의미합니다. 500번대는 서버 오류 응답코드로 요청은 유효하나 서버가 요청을 충족할 수 없음을 의미합니다.

</br>

9. REST API 설계 시 규칙 아는대로 말씀해주세요.

>첫째, URI를 통해 필요 자원의 위치를 명시해야 합니다. 둘째, 자원에 대한 행위는 HTTP 메소드를 사용하여 표현합니다.

</br>

10. REST API의 URI를 설계하는 규칙을 말해보세요.

> 첫째, 소문자를 사용해야 합니다. 둘째, 언더바 대신 하이픈을 사용해야 합니다. 셋째, URI의 마지막에는 슬래시를 사용하지 않아야 합니다. 넷째, 계층관계는 슬래시 구분자를 사용하여 나타냅니다. 다섯째, 파일의 확장자는 URI에 포함시키지 말아야 합니다. 여섯째, 컨트롤 자원의 경우에는 동사형으로 그 외의 자원의 경우에는 명사형으로 작성합니다. 일곱째, 인스턴스가 하나더라도 복수형으로 작성합니다.

</br>

11. 무상태성(stateless)의 예시로 실제로 사용되는 거 알고 계신거 있으신가요?

> 무상태성은 서버가 클라이언트의 상태를 보존하지 않는 것을 의미합니다. REST api의 모든 통신은 무상태성을 충족한 채로 이루어집니다. 예를 들어 웹사이트의 로그인 정보는 서버에 저장되지 않습니다.

</br>

12. 실제로 프로젝트 진행하셨을 때 REST API 적용하신 예시 하나 들어주세요. 어떤 API이고, 어떤 메소드를 사용했고, 해당 URL은 무엇인지 말씀해주시면 됩니다.

---

## References

[REST API 제대로 알고 사용하기](https://meetup.toast.com/posts/92)

[RESTful API란 무엇인가요?](https://aws.amazon.com/ko/what-is/restful-api/)

[[Network] REST란? REST API란? RESTful이란?](https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html)

[GET vs POST](https://github.com/workhardslave/cs-study/blob/main/Network/GET%20vs%20POST.md)

[[HTTP] HTTP Method 정리 / GET vs POST 차이점](https://im-developer.tistory.com/166)

[http 메소드와 상태코드](https://kyun2da.dev/CS/http-메소드와-상태코드/)

[Http Method 란? (GET, POST, PUT, DELETE)](https://velog.io/@yh20studio/CS-Http-Method-란-GET-POST-PUT-DELETE)

[HTTP Request 정리](https://velog.io/@josworks27/HTTP-Request-정리)

[URI랑 URL 차이점이 뭔데?](https://www.charlezz.com/?p=44767)

[PATCH 메서드가 멱등이 아닌 이유](https://www.inflearn.com/questions/110644)

[루프백 테스트 란 무엇입니까? -techopedia에서 정의](https://ko.theastrologypage.com/loopback-test)

[HTTP 상태 코드 정리](https://www.whatap.io/ko/blog/40/)

[7 Rules for REST API URI Design](https://dzone.com/articles/7-rules-for-rest-api-uri-design-1)

[이미지 출처1](https://www.edureka.co/blog/what-is-rest-api/)

[이미지 출처2](
