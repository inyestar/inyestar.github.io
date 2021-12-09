---
layout: post
title: Spring Core Concepts
tags: [Java, Spring]
---

### Spring IoC (Inversion of Control) Container
- Spring의 핵심
- Object를 생성, 연결, 구성 등 object 전체 라이프 사이클 관리
- 애플리케이션을 구성하는 콤포넌츠를 관리하기위해 DI를 사용
- Packages
  - org.springframework.beans
    - BeanFractory 인터페이스로 모든 타입의 object를 관리할 수 있는 매커니즘 제공
    - 프레임워크 구성, 기본 기능 제공
  - org.springframework.context
    - ApplicationContext는 BeanFractory의 서브 인터페이스로 Spring Ioc Conatiner 개념 구현체라고 봐도 무방
    - AOP, 다국어를 위한 메시지 리소스, 이벤트 발행, 웹 애플리케이션 사용을 위한 WebApplicationContext 등과의 더 쉬운 통합을 가능하게 함
    - BeanFactory의 완전한 상위 집합
    - Enterprise 용 기능 제공
    > BeanFactory나 ApplicationContext나 Bean을 생성하고 연결할 수 있지만 MessageSource 접근이나 BeanPostProcessor 기능은 ApplicationContext만 갖고 있음

<br>
<br>

### Bean
- Ioc container가 관리하는 Java Object
- Scope
  - singleton
    - Ioc Container마다 생성되는 단일 인스턴스로 thread-unsafe
    - bean의 default scope
  - prototype
    - bean이 필요할 때 마다 새롭게 생성되는 인스턴스
    - 생성 갯수에 제한 없음
  - request
    - 단일 HTTP 요쳥의 라이프 사이클에 대한 정의
    - 모든 HTTP 요청은 (하나의 bean 정의에서 생성된) 개별 인스턴스를 갖게 됨
    - Web을 사용하는 ApplicationContext에서만 유효함
  - session
    - 하나의 HTTP 세션의 라이프 사이클에 대한 정의
    - Portlet을 사용하는 context에서 유효
        > Portlet이란? Servlet과 유사하게 포틀릿은 포틀릿컨테이너에 배치되어 동적 컨텐츠를 생성하는 웹 콤포넌트

    - Web을 사용하는 ApplicationContext에서만 유효함
  - global-session, custom, web socket, refresh, thread 등이 있음

### Dependency Injection
- IoC 컨테이너가 서비스 impl을 Target object의 프로퍼티에 Injection함
- Injection은 생성자나 setter 메소드를 통해 이루어짐
- Target object가 직접 서비스 impl의 인스턴스를 생성하지 않도록 하는 이유는 애플리케이션의 객체가 POJO 그 자체로 존재할 수 있도록 하기 위함
    > POJO란? Plain Old Java Object의 약자로 Java EE에서 프레임워크에 종속된 '무거운' 객체에 반하는 개념으로 특정 '기술'에 종속된 것이 아닌 순수한 자바 객체를 말함

- 객체가 POJO이면 다른 환경의 다른 구현체에서 쉽게 사용이 가능함
- 독립적인 콤포넌츠나 객체를 wiring 하여 느슨한 결합을 구현할 수 있음
<br>
<br>

### Aspect Oriented Programming
- 프로그래밍 로직을 관심사를 중심으로 재구성
- 관심사 교차로 모듈성 향상
- 관심사 교차란 한 공간에 존재하는 코드로 전체 애플리케이션에 영향을 끼치는 것을 의미함
- 트랜잭션 관리, 인증, 로깅, 보안 등
<br>
<br>

### References
- [https://www.dariawan.com/tutorials/spring/core-concepts-of-the-spring-framework/](https://www.dariawan.com/tutorials/spring/core-concepts-of-the-spring-framework/)
- [https://siyoon210.tistory.com/120]{https://siyoon210.tistory.com/120}
- [https://blog.daum.net/tomayoon/7095404](https://blog.daum.net/tomayoon/7095404)
