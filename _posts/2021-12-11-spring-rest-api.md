---
layout: post
title: Spring Rest API
tags: [Spring]
---

### Rest API


### URL Convention
- Do!
  - 복수 명사
  - 하이픈(-) 문자
  - 소문자
- Don't!
  - 동사
  - 끝에 / 포함
  - 언더바(_) 문자
  - 대문자
  - 확장자
- 예) /users, /users/123
<br>
<br>

### Response Convention
- 200, 201
- 400, 401, 404, 405
- 301 : Location Header
<br>
<br>

### REST in Spring
#### Requests

#### Response
- @RestController

#### Exceptions
- ExceptionHandler
  - 익셉션을 처리할 메소드 생성 후 상단에 @ExceptionHandler 추가
  - 특정 컨트롤러에만 적용된다는 단점이 있음
- HandlerExceptionResolver
  - ExceptionHandlerExceptionResolver
    - DispatcherServlet이 기본적으로 활성화 시킴
    > ServletController -> DispatcherServlet

  - DefaultHandlerExceptionResolver
    - DispatcherServlet이 기본적으로 활성화 시킴
    - 에러 코드를 세팅
    - 응답 메시지의 바디에 상세 에러 메시지 추가할 수 없음
  - ResponseStatusExceptionResolver
    - DispatcherServlet이 기본적으로 활성화 시킴
    - Custom 익셉션을 Http 에러 코드에 맵핑
    - 응답 메시지의 바디에 상세 에러 메시지 추가할 수 없음
  - @ControllerAdvice
    -
  - ResponseStatusException

### References
- [https://meetup.toast.com/posts/92](https://meetup.toast.com/posts/92)
- [https://sanghaklee.tistory.com/57](https://sanghaklee.tistory.com/57)
- [https://www.baeldung.com/exception-handling-for-rest-with-spring](https://www.baeldung.com/exception-handling-for-rest-with-spring)
