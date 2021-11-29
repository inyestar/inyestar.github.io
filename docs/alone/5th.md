---
layout: default
title: 5th
parent: ALONE
---

# 2021년 11월 29일

## NOTE
#### Message Backend
- Spring Boot
  - Spring Framework를 기반으로 REST API를 개발하는데 자주 사용
  - 의존성 주입이 Spring의 핵심이었다면 Boot는 Autoconfiguration
  - Jetty나 Tomcat 같은 WAS가 내장되어 있음
  - H2와 같은 in-memory 데이터베이스 지원
- JPA
  - 자바 객체를 관계형 데이터베이스의 레코드와 바인딩 시키는 기술 명세
  - https://github.com/javaee/jpa-spec 에서 확인 가능
  - Spring Data JPA :
  - Hibernate 애너테이션보다 Javax.persistence의 애너테이션을 써야 나중에 다른 provider로 변경할 때 이슈가 없음
  - JDBC
- ORM
  - Java Object mapped to Databases
  - JAP도 실행 방법은 다르나 ORM 비슷한 레이어가 존재함
  - Hibernate : 자바 ORM 라이브러리

#### Concerns
- 메시지를 새로 보내면 채팅 리스트에 있는 날짜로 변하는 걸 확인했다. 그러면 메시지를 DB에 저장할 때마다 채팅의 recent 시간도 업데이트 되어야 할까? 아니면 사용자에게 보여지는 것만 변경하고 나중에 동기화를 시키는 것이 좋을까? 아니면 다른 좋은 방법이 있을까!
- Java 종류
  - Java SE or J2SE : Standard Edition의 약자로 일반 자바 프로그램 개발을 위한 용도로 사용되며 (Swing, AWT 지원) 썬 마이크로시스템에서 JCP로 관리 주체가 변경됨
  - jAVA EE : Enterprise Edition의 약자로 (WAS를 이용하는) 서버 개발을 위한 플랫폼이며 EJB, JSP, Servlet, JNDI등을 지원

## PRACTICE
- MariaDB
  ```bash
  $ sudo docker run -d \
      --name db \
      -p 3306:3306 \
      -e MARIADB_USER=#### \
      -e MARIADB_PASSWORD=#### \
      -e MARIADB_ROOT_PASSWORD=#### \
      mariadb:latest
  $ mysql -h 127.0.0.1 -u root -p
  ```
  ```sql
  create database message default character set utf8mb4;
  use message;
  show create database message;
  CREATE TABLE `conversation` (
    `cid` bigint(20) NOT NULL PRIMARY KEY,
    `uid` varchar(50) NOT NULL,
    `msgType` int(11) NOT NULL,
    `status` int(11) NOT NULL,
    `content` varchar(128) NOT NULL,
    `time` bigint(20) NOT NULL
  ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4
  select * from mysql.user where User = '####'\G
  grant all on message.* to '####'@'%' identified by '####';
  flush privileges;
  select version();

  ```

## REFERENCES
- https://www.geeksforgeeks.org/difference-between-spring-and-spring-boot/
- https://www.youtube.com/watch?v=8SGI_XS5OPw
- https://www.youtube.com/watch?v=uzeJb7ZjoQ4
- https://www.baeldung.com/jpa-vs-jdbc
- https://kys4871.tistory.com/74
- https://www.infoworld.com/article/3379043/what-is-jpa-introduction-to-the-java-persistence-api.html
