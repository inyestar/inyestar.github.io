---
layout: post
title: Java IO
categories: ["java"]
---

### Introduction
네트워크를 사용하거나 파일을 읽고 쓸 때 빠지지 않는 개념인 IO. 개념을 이해하는데 있어서 가장 먼저 맞닥뜨리는 난관은 버퍼의 개념을 이해하는게 아니었나싶다. 전혀 모르던 세상이었기에 버퍼라는 단어를 들어도 머릿속에서 잘 그려지지 않았다. 이제는 버퍼가 메모리에 할당된 일정한 공간이라는 사실을 어렴풋이 이해하고 있지만, 여기서 한 가지 더 궁금한 점이 생겼다. 메모리에 생긴 공간이 도대체 어떻게 디스크에 쓰여져 있는 파일을 읽어오는 것이지? 이 궁금증을 해소하고자 포스트를 작성한다.
<br>
<br>

### Buffer
파일을 읽고 쓸 때 1 byte를 읽고 1 byte를 바로 쓰면 너무 비효율적이라서 (버퍼라고 불리는) 메모리에 일정 공간을 할당하여 먼저 읽기를 통해 그 공간만큼 채우고 채운 공간만큼 다시 쓰도록 해주는 일종의 임시 저장소이다. 그렇다면 왜 1 byte를 읽고 1 byte를 쓰는 것이 비효율적일까? 그 질문에 대해 알아보기전에 버퍼에 대해 간략하게 짚고 넘어가자. 자바에서 사용하는 버퍼는 크게 두 가지가 있다.
#### Non Direct Buffer
- JVM의 Heap 영역 내에서 공간을 만드는 버퍼
- 실질적으로 파일을 읽을 때 JNI를 통해 OS 레벨로 접근이 가능한 C 코드를 호출하기 때문에 오버헤드가 발생한다. 즉 추가적인 자원 할당이 필요하다는 말씀.
#### Direct Buffer
- 시스템 메모리에 직접 공간을 만드는 버퍼
- 시스템 메모리에 공간을 만들어야 하기 때문에 Non Direct Buffer 보다는 초기화가 오래 걸린다. 그래서 사용이 필요한 경우 한 번 생성하면 계속 사용할 수 있는 구조로 만드는 것이 중요.
<br>
<br>

### How it works?
일반적으로 프로세스는 OS에게 버퍼에 있는 데이터를 빼가거나 버퍼에 데이터를 채워달라고 요청하는 방식으로 IO를 수행한다. 다음은 (실제로는 훨씬 더 복잡하지만) 간략하게 요약된 디스크를 읽는 절차이다.
#### Read
- 프로세스는 시스템 콜인 read()를 호출하여 프로세스의 버퍼가 채워지도록 요청한다.
- read() 콜이 발생하면 커널은 disk controller에게 하드웨어의 데이터를 가져오라고 명령을 내린다.
- 명령을 받은 disk controller는 DMA를 통해 커널의 메모리 영역(커널의 버퍼)에 데이터를 쓰기 시작한다.
    > DMA란 Direct Memory Access의 약자로 하드웨어가 메모리에 직접 접근하여 read/write를 가능하게 하는 기능으로 CPU의 개입이 없이 데이터를 전송하며 물리적인 메모리 주소에만 접근이 가능

- disk controller가 커널 버퍼에 데이터를 다 채우면 커널은 해당 데이터를 프로세스 영역의 버퍼에 복사한다.
- 커널은 데이터를 캐싱하거나 미리 가져오려고 하기 때문에 프로세스가 요청한 데이터는 이미 커널 영역에서 사용이 가능할 수도 있다.

#### Virtual Memory
위에서 disk controller는 커널에 데이터를 보낸다고 했다. 또 커널은 해당 데이터를 다시 프로세스의 버퍼에 복사해준다. 두 번이나 copy/paste 작업이 일어나는 것이다. 그럼 왜 disk controller는 프로세스의 버퍼에 직접 데이터를 보내지 않는 것일까? 아마도 (자세히는 모르지만) 불가능해서 였겠지만 이 불가능을 해소하기 위해 가상 메모리가 등장한다.
    > 가상 메모리란 물리적인 메모리 주소를 대신하여 사용할 수 있는 인공적인, 가상의 주소를 의미한다.

- 하나 이상의 가상 주소는 같은 물리적 메모리 주소를 참조할 수 있기 때문에, 사용자 영역의 가상 주소와 커널 영역의 가상 주소를 같은 물리적 주소에 맵핑 시킨다.
- DMA가 커널 영역의 버퍼에 데이터를 전송하면 똑같은 물리적 주소에 맵핑되어 있는 사용자 영역의 가상 메모리에서 해당 데이터를 동시에 조회할 수가 있다.
- 같은 물리적 메모리 주소를 공유하기 위해서는 아래와 같은 조건이 필요하다.
  - 커널과 사용자 버퍼는 같은 page alignment를 공유해야 함
  - 버퍼는 disk controller가 사용하는 block 크기 단위로 할당되어야 함 (보통 512 byte)
    > OS는 메모리 주소 영역을 pages라는 단위로 나눠서 관리한다. Pages는 고정된 크기를 구분짓는 개념으로 대개 disk block size의 배수이거나 2의 제곱 정도의 크기를 갖는다. 가상 메모리와 물리적 메모리의 페이지 크기는 항상 똑같다.

#### File System
- 파일 시스템은 디스크에 저장되어 있는 데이터에 대한 추상화 개념이다.
- 파일 시스템은 데이터에 대한 해석과 어렌징 방식이다.
- 우리가 쓴 코드는 거의 대부분 파일 파일 시스템과 상호 작용한다. 하드 디스크와 직접 하는 것이 아니다.
- 사용자 프로세스로부터 파일 읽기에 대한 요청이 들어오면, 파일 시스템 구현체가 디스크의 어느 부분에 그 데이터가 존재하는지 판단한다. 그리고나서 해당 디스크 섹터를 메모리로 가져온다.
- 파일 시스템도 메모리의 pages 개념을 갖고 있다.
- Step by Step
  - 요청된 파일이 파일 시스템의 어느 page에 위치하는지 파악한다. (메타 데이터와 실제 파일 데이터는 다른 page 내에 존재할 수 있으며 해당 page는 인접한 곳에 위치하지 않을 수도 있다.)
  - 필요한 pages를 가져오기 위해 커널 영역의 메모리에 충분한 공간을 확보한다.
  - 커널 영역의 메모리 pages와 파일 시스템의 pages를 맵핑 시킨다.
  - page faults를 각 메모리 페이지에 생성한다.
    > page faults란 메모리에 사용하고자 하는 데이터가 없어서 디스크에 해당 데이터를 요쳥하는 것

  - 가상 메모리 시스템이 page faults를 trap하고 pageins를 스케줄링한다.
    > trap이란 어떤 프로세스가 특정 시스템 기능을 사용하려고 할 때 그 기능을 운영체제에게 요쳥하는 방법

  - pageins가 완료되면 파일 시스템은 요청된 파일 데이터와 정보를 추출하기 위해 raw data를 조각낸다.
<br>
<br>

### Practice
- Non Direct 버퍼
- Direct 버퍼
<br>
<br>

### References
- [https://howtodoinjava.com/java/io/how-java-io-works-internally/](https://howtodoinjava.com/java/io/how-java-io-works-internally/)
- [https://palpit.tistory.com/641](https://palpit.tistory.com/641)
- [https://kkhipp.tistory.com/168](https://kkhipp.tistory.com/168)
- [https://ko.wikipedia.org/wiki/%ED%8A%B8%EB%9E%A9_(%EC%BB%B4%ED%93%A8%ED%8C%85)](https://ko.wikipedia.org/wiki/%ED%8A%B8%EB%9E%A9_(%EC%BB%B4%ED%93%A8%ED%8C%85))
- [https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=lyongh00&logNo=90050623703](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=lyongh00&logNo=90050623703)
