---
layout: post
title: Spring REST API
tags: [Spring]
---

### REST API
- REST(Representational State Transfe) 아키텍처의 제약 조건을 준수하는 API
- 특징
  - HTTP 프로토콜
  - Client-Server 아키텍처
  - Stateless
  - 캐싱
<br>
<br>

### Convention
#### Method
- GET : 기존 리소스 읽기
- POST : 새로운 리소스 생성
- PUT : 기존 리소스 전체 업데이트
- PATCH : 기존 리소스 일부 업데이트
- DELETE : 기존 리소스 삭제

#### URL
- Do!
  - 복수 명사
  - 하이픈(-) 문자
  - 소문자
  - 슬래시(/)를 통해 계층 관계 명시
- Don't!
  - 동사(HTTP method로 대체)
  - 끝에 / 포함
  - 언더바(_) 문자
  - 대문자
  - 확장자
- 예시
  - GET /users : 모든 유저 정보 조회
  - GET /users/123 : 유저 123 조회
  - POST /users : 새로운 유저 생성
  - PUT /users/123 : 유저 123 정보 수정
  - GET /users/123/orders : 유저 123의 전체 주문 정보 조회
  - GET /users/123/orders/1 : 유저 123의 주문번호 1 조회

#### Header
- Content-Type
  - HTTP Request 안에 담긴 Body의 데이터 종류를 명시하는 헤더
  - 서버는 헤더를 보고 메시지를 적절하게 처리할 준비를 할 수 있음
  - 예: Content-Type: application/json
- Accept 헤더
  - HTTP Response 안에 담긴 Body의 데이터 종류(MIME)를 명시하는 헤더
  - 클라이언트는 헤더를 보고 메시지를 적절하게 처리할 준비를 할 수 있음
  - 예: Accept: application/json

#### Message Format
- json
- xml

#### Response Code
- 200, 201 : 요청 성공 시 2xx로 응답
- 400, 401, 404, 405 : 요청 실패 시 4xx로 응답
- 301 : 새로운 리소스 생성 시 Location 헤더를 추가하여 새로 생성된 리소스 위치 명시
<br>
<br>

### REST in Spring
#### Response and Request
- @Get/Post/Put/Patch/DeleteMapping :
  - HTTP method에 상응하여 처리할 메소드를 명시하는 애너테이션
- @RestController
  - 컨트롤러 안의 각 메소드가 반환하는 데이터는 (템플릿 렌더링 대신) 바로 Reponse의 Body로 작성하겠다고 명시하는 애너테이션

#### Exceptions
- @ExceptionHandler
  - 익셉션을 처리할 메소드 생성 후 상단에 @ExceptionHandler 추가
  - 특정 컨트롤러에만 적용된다는 단점이 있음
- HandlerExceptionResolver
  - ExceptionHandlerExceptionResolver
    - since 3.1
    - DispatcherServlet이 기본적으로 활성화 시킴
    > ServletController -> DispatcherServlet

    - @ExceptionHandler가 동작하기 위한 핵심 콤포넌트
  - DefaultHandlerExceptionResolver
    - since 3.0
    - DispatcherServlet이 기본적으로 활성화 시킴
    - HTTP의 4xx와 5xx 에러를 처리하기 위해 사용됨
    - 응답 메시지의 바디에 상세 에러 메시지 추가할 수 없음
  - ResponseStatusExceptionResolver
    - since 3.0
    - DispatcherServlet이 기본적으로 활성화 시킴
    - Custom 익셉션에 @ReponseStatus를 통해 Http 에러 코드에 맵핑
    - 응답 메시지의 바디에 상세 에러 메시지 추가할 수 없음
  - SimpleMappingExceptionResolver
    - Spring MVC부터 사용
    - Exception 클래스의 잉름을 view의 이름과 맵핑하는데 사용
  - AnnotationMethodHandlerExceptionResolver
    - since 3.0
    - @ExceptionHandler로 익센셥 처리를 위해 등장
    - 3.2부터 deprecated
- @ControllerAdvice
  - since 3.2
  - 흩어져 있는  @ExceptionHandler를 single, global하게 관리 가능하게 함
  - 응답 코드, 응답 메시지 모두 제어 가능
  - 여러 개의 익셉션을 단일 메소드에 맵핑 가능
- ResponseStatusException
  - since 5
  - 컨트롤러에서 응답 코드와 메시지로 ResponseStatusException 초기화하여 던짐
  - 프로토타이핑에 좋음
  - 글로벌하게 한번에 처리할 방법이 없으며 컨트롤러마다 에러 메시지를 작성해야 해서 중복 코드 발생
<br>
<br>

### References
- [https://meetup.toast.com/posts/92](https://meetup.toast.com/posts/92)
- [https://sanghaklee.tistory.com/57](https://sanghaklee.tistory.com/57)
- [https://www.baeldung.com/exception-handling-for-rest-with-spring](https://www.baeldung.com/exception-handling-for-rest-with-spring)
- [https://spring.io/guides/tutorials/rest/](https://spring.io/guides/tutorials/rest/)
- [https://www.redhat.com/ko/topics/api/what-is-a-rest-api](https://www.redhat.com/ko/topics/api/what-is-a-rest-api)
