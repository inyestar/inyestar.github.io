---
layout: post
title: Java IO
tags: [Java]
---

### Introduction
네트워크를 사용하거나 파일을 읽고 쓸 때 빠지지 않는 개념인 IO. 개념을 이해하는데 있어서 가장 먼저 맞닥뜨리는 난관은 Buffer의 개념을 이해하는게 아니었나싶다. 전혀 모르던 세상이었기에 buffer라는 단어를 들어도 머릿속에서 잘 그려지지 않았다. 이제는 buffer가 메모리에 할당된 일정한 공간이라는 사실을 어렴풋이 이해하고 있지만, 여기서 한 가지 더 궁금한 점이 생겼다. 메모리에 생긴 공간이 도대체 어떻게 디스크에 쓰여져 있는 파일을 읽어오는 것이지? 이 궁금증을 해소하고자 포스트를 작성한다.
<br>
<br>

### Buffer
파일을 읽고 쓸 때 1 byte를 읽고 1 byte를 바로 쓰면 너무 비효율적이라서 buffer라고 불리는 메모리에 일정 공간을 할당하여 먼저 읽기를 통해 그 공간만큼 채우고 채운 공간만큼 다시 쓰도록 해주는 일종의 임시 저장소이다. 그렇다면 왜 1 byte를 읽고 1 byte를 쓰는 것이 비효율적일까? 그 질문에 대해 알아보기전에 Buffer에 대해 간략하게 짚고 넘어가자. 자바에서 사용하는 buffer는 크게 두 가지가 있다.
#### Non Direct Buffer
- JVM의 Heap 영역 내에서 공간을 만드는 버퍼
- 실질적으로 파일을 읽을 때 JNI를 통해 OS 레벨로 접근이 가능한 C 코드를 호출하기 때문에 오버헤드가 발생한다. 즉 추가적인 자원 할당이 필요하다는 말씀.
#### Direct Buffer
- 시스템 메모리에 직접 공간을 만드는 버퍼
- 시스템 메모리에 공간을 만들어야 하기 때문에 Non Direct Buffer 보다는 초기화가 오래 걸린다. 그래서 사용이 필요한 경우 한 번 생성하면 계속 사용할 수 있는 구조로 만드는 것이 중요하다.
<br>
<br>

### How it works?
일반적으로 프로세스는 OS에게 buffer에 있는 데이터를 빼가거나 buffer에 데이터를 채워달라고 요청하는 방식으로 IO를 수행한다. 다음은 (실제로는 훨씬 더 복잡하지만) 간략하게 요약된 디스크를 읽는 절차이다.
#### Read
- 프로세스는 시스템 콜인 read()를 호출하여 프로세스의 Buffer가 채워지도록 요청한다.
- read() 콜이 발생하면 kernel은 disk controller에게 하드웨어의 데이터를 fetch하라고 명령을 내린다.
- 명령을 받은 disk controller는 DMA를 통해 kernel의 메모리 영역(kernel의 buffer)에 데이터를 쓰기 시작한다.
    > DMA란? 물리적인 메모리 주소에만 접근이 가능한

- disk controller가 kernel buffer에 데이터를 다 채우면 kernel은 해당 데이터를 프로세스 영역의 buffer에 복사한다.
- kernel은 데이터를 cache하거나 미리 가져오려고 하기 때문에 프로세스가 요청한 데이터는 이미 kernel 영역에서 사용이 가능할 수도 있다.
#### Virtual Memory
위에서 disk controller는 kernel에 데이터를 보낸다고 했다. 그럼 왜 disk controller는 프로세스의 buffer에 직접 데이터를 보내지 않는 것일까? Virtual Memory를 사용하면 가능하다.
> Virtual memory란 물리적인 메모리 주소를 대신하여 사용할 수 있는 인공적인, 가상의 주소를 의미한다.

- 커널 영역의 주소를 사용자 공간의 가상 공간의 주소와 동일한 물리적 주소에 맵핑 시키는 것으로 가능해진다.
- DMA가 커널 영역의 버퍼에 데이터를 전송하면 똑같은 물리적 주소에 맵핑되어 있는 사용자 영역의 버츄얼 메모리에서 해당 데이터를 동시에 조회할 수가 있다.

#### File System
- 파일 시스템은 높은 추상화 개념으로 디스크에 저장되어 있는 데이터에 대한 해석과 어렌징에 대한 방식이다.
- 우리가 쓴 코드는 거의 대체로 파일 시스템과 상호작용을 하지 디스크와 하는 것이 아니다.
- 사용자 프로세스로부터 파일 읽기에 대한 요청이 들어오면 파일 시스템 imple은 디스크의 어느 부분에 그 데이터가 존재하는지 판단한다. 그리고나서 해당 디스크 섹터를 메모리로 가져온다.

### Terminologies
- overhead
- kernel
- process
- cache
<br>
<br>

### References
- [https://howtodoinjava.com/java/io/how-java-io-works-internally/](https://howtodoinjava.com/java/io/how-java-io-works-internally/)
- [https://palpit.tistory.com/641](https://palpit.tistory.com/641)
