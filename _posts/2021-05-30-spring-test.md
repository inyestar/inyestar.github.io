---
layout: post
title: Spring Test
tags: [Spring]
---

## How works
- 다음 조건을 갖고 있는 메소드가 있는 클래스를 읽는다.
  - @Test 애너테이션
  - public 접근자  ---> 확인 필요
  - void 리턴
  - 파라미터 없음
- 테스트 클래스 Object를 만든다.
  - 각 테스트 메소드를 실행할 때 마다 테스크 클래스 Object가 생성 된다.
  - Object는 테스트가 끝나면 버려진다.
  - 테스트 독립성 보장을 위해 매번 새로운 오브젝트가 생성된다.
- @Before가 붙은 메소드가 있으면 실행한다.
- @Test가 붙은 메소드를 호출하고 테스트 결과를 저장한다.
- @After가 붙은 메소드가 있으면 실행한다.
- 나머지 테스트 메소드에 대해 위 세 가지 과정을 반복한다.


## References
- [https://goodgid.github.io/How-JUnit-Works/](https://goodgid.github.io/How-JUnit-Works/)
