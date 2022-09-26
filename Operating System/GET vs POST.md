## RESTful API (Representational State Transfer API)

### 1. 개념

두 컴퓨터 시스템이 인터넷을 통해 정보를 안전하게 교환하기 위해 사용하는 인터페이스

→ REST 기반의 아키텍처를 사용함으로써 대규모의 고성능 특징을 안정적으로 지원할 수 있다.

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

