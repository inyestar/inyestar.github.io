---
layout: post
title: Java Sequence of Characters
categories: ["java"]
---

자바에서는 연속된 문자열을 표현하는 클래스를 아래와 같이 제공하고 있다.

### String
- immutable
- 한 번 생성되면 변경할 수 없기 때문에 멀티 쓰레드 환경에서 동기화를 고려하지 않아도 된다.
- 값을 변경할 경우 변경된 값으로 새로운 객체가 생성된다.
- 클래스 선언 방식에 따라 메모리 저장 방식이 다르다.
  - Heap
    - 같은 값을 선언하더라도 서로 다른 인스턴스에 저장한다.
      ```java
      String s1 = new String("abc");
      ```
  - Runtime Constant Pool
    - 같은 값을 선언하면 같은 모메리를 공유한다.
      ```java
      String s2 = "abc";
      ```
      > 리터럴 값들은 Class 단위로 Constant Pool에서 관리가 이루어 진다. 한 Class에 등장하는 동일한 리터럴 값은 여러 번 메모리에 Load 되지 않는다.

- 문자열에 대한 연산이 일어날 때 마다 새로운 인스턴스가 생성되기 때문에 이전 문자열은 계속 GC 대상이 된다. 이 경우 성능 이슈가 발생할 수 있다.

<br>
<br>

### StringBuffer
- mutable
- since java 1.0
- synchronized : thread-safe를 보장한다. 두 쓰레드가 동시에 StringBuffer의 메소드를 호출 할 수 없다.
    ```java
    @Override
    public synchronized StringBuffer append(Object obj) {
        toStringCache = null;
        super.append(String.valueOf(obj));
        return this;
    }
    ```
- 동기화 때문에 StringBuilder 보다 느리다.
<br>
<br>

### StringBuilder
- mutable
- since java 1.5
- non-syncronized : thread-safe를 보장하지 않는다. 두 쓰레드가 동시에 StringBuilder의 메소드를 호출 할 수 있다.
    ```java
    @Override
    public StringBuilder append(Object obj) {
        return append(String.valueOf(obj));
    }
    ```
- 비동기 특징 때문에 멀티 쓰레드 환경에서 위험하다.
<br>
<br>

### Usecase
- 상수로 사용할 땐 String
- 계속 수정 필요 + 단일 쓰레드에서만 접근할 경우 StringBuilder
- 계속 수정 필요 + 다수의 쓰레드가 접근해야할 경우 StringBuffer
<br>
<br>

### Examples
- immutable과 mutable
    ```java
    public class SequenceOfCharactersTest {

  	static void concatString(String s1) {
  		s1 += "World";
  	}

  	static void concatStringBuffer(StringBuffer s2) {
  		s2.append("World");
  	}

  	static void concatSTringBuilder(StringBuilder s3) {
  		s3.append("World");
  	}

  	public static void main(String[] args) {
  		String s1 = "Hello";
  		StringBuffer s2 = new StringBuffer("Hello");
  		StringBuilder s3 = new StringBuilder("Hello");

  		concatString(s1);
  		concatStringBuffer(s2);
  		concatSTringBuilder(s3);

  		System.out.println(s1);
  		System.out.println(s2);
  		System.out.println(s3);
  	 }
    }
    ```
    ```console
    Hello
    HelloWorld
    HelloWorld
    ```
<br>
<br>

### Advanced
- 위에서 Runtime Constant Pool은 클래스 단위로 관리가 이루어진다고 했지만 String은 특이하게도 리터럴이 할당된 객체의 값을 String Pool에서 먼저 조회한 뒤 이미 있으면 객체가 해당 인덱스를 참조하도록 하고 아니면 String Pool 테이블에 저장한다(String#intern()). 서로 다른 클래스에서 같은 리터럴이 선언되면 같은 값을 참조하도록 해주는 것이다. 누가? JVM이. 그러면 메모리 절약이 엄청 나겠죠~~~
<br>
<br>

### References
- [https://www.javatpoint.com/difference-between-stringbuffer-and-stringbuilder](https://www.javatpoint.com/difference-between-stringbuffer-and-stringbuilder)
- [https://www.geeksforgeeks.org/string-vs-stringbuilder-vs-stringbuffer-in-java/](https://www.geeksforgeeks.org/string-vs-stringbuilder-vs-stringbuffer-in-java/)
- [https://scshim.tistory.com/64](https://scshim.tistory.com/64)
- [http://blog.breakingthat.com/2018/12/21/java-constant-pool%EA%B3%BC-string-pool/](http://blog.breakingthat.com/2018/12/21/java-constant-pool%EA%B3%BC-string-pool/)
- [https://www.latera.kr/blog/2019-02-09-java-string-intern/](https://www.latera.kr/blog/2019-02-09-java-string-intern/)
