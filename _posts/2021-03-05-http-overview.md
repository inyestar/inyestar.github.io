---
layout: post
title: HTTP Overview
tags: [HTTP]
---

### Http
- TCP 혹은 TLS로 암호화된 TCP를 통해 전송되는 애플리케이션 레이어 프로토콜
- client-server 프로토콜로 웹 브라우저와 같은 수신자가 요청을 시작한다.
- 문서, 이미지, 비디오, 스크립트와 같은 하위 문서들을 통해 페이지가 구성된다.
- 클라이언트와 개별적인 (스트림과 다른)메시지를 교환하여 통신한다.
- 클라이언트가 보내는 메시지가 request 서버가 응답으로 보내는 메시지가 response이다.
<br>
<br>

#### 특징
- 사람이 읽을 수 있을 정도로 간단하다.
- Http 헤더를 통해 신기능 도입이 쉬워 확장성이 좋다.
- 클라이언트의 이전 상태를 알 수가 없기 때문에 stateless라고 불린다.
- 쿠키를 통해 클라이언트의 상태를 유지 할 수 있기때문에 sessionless는 아니다.
<br>
<br>

#### HTTP를 통해 제어 가능한 영역
- 캐싱 :  서버는 어떤 데이터를 얼마만큼의 기간동안 캐싱할 것인지를 프록시와 클라이언트에게 지시할 수 있다. 클라이언트는 저장된 문서를 무시하도록 중간 캐시 프록시에게 지시할 수 있다.
- 제약 조건 완화 : 개인 정보 침해를 완화하기 위해 same origin 에서 온 페이지만 웹 페이지에 접속할 수 있도록 하는 제약 사항을 서버에서 HTTP 헤더를 통해서 완화 시킬수가 있다. 그래서 사이트의 일부 정보들을 다른 도메인으로부터 가져올 수가 있다.
- 인증 :  쿠키나 헤더를 통해 특정 유저에게만 접근이 허용되도록 할 수 있다.
- 프록시와 터널링 : 인트라넷과 같은 네트워크 장벽을 프록시를 통해 지나갈 수 있다.
- 세션 : 쿠키를 통해 요청을 서버 상태와 연결할 수 있다.
<br>
<br>

#### Flow
1. TCP 연결 열기 : TCP 연결은 요청을 전달하거나 응답을 받기위해 사용된다. 클라이언트는 매번 새로운 요청을 열 수도 있고 기존에 있는 연결을 사용할 수도 있으며 서버로 연결된 여러개의 TCP 연결을 열 수도 있다.
2. HTTP 메시지 전송 :  HTTP/2 이전의 HTTP 메시지는 사람이 읽을 수 있었다. HTTP/2 이후에는 메시지들이 프레임에 캡슐화 되어 있어 직접적으로 읽을 수 없도록 만들어졌다. 하지만 아래 사항들은 동일하게 읽을 수 있도록 남아있다.
    ```java
    GET / HTTP/1.1
    Host: developer.mozilla.org
    Accept-Language: fr
    ```
3. 아래와 같은 서버발 메시지 해석
    ```java
    HTTP/1.1 200 OK
    Date: Sat, 09 Oct 2010 14:28:02 GMT
    Server: Apache
    Last-Modified: Tue, 01 Dec 2009 20:18:22 GMT
    ETag: "51142bc1-7449-479b075b2891b"
    Accept-Ranges: bytes
    Content-Length: 29769
    Content-Type: text/html

    <!DOCTYPE html... (here comes the 29769 bytes of the requested web page)
    ```
4. 이후 요청들을 위해 연결을 닫거나 재사용
- HTTP 파이프라이닝이 활성화 되어 있을 경우 일부 요청들은 첫 번째 응답이 다 수신하기 전에 서버로 전달된다. 파이프라이닝은 기존 네트워크에서 구현이 너무 어려워 HTTP/2에서는 멀티플렉싱으로 대체되었다.
<br>
<br>

### Connection
- 네트워크의 transport 레이어에서 제어된다.
- Http가 transport 레이어에서 특정 프로토콜을 선호하지는 않지만 프로토콜의 신뢰성은 보장되어야 한다.
- 일반적인 tranport 프로토콜은 TCP와 UDP 있지만 TCP가 신뢰성이 보장되어 인터넷상에서 자주 사용되어 결과적으로 Http는 TCP에 의지하고 있다.
- HTTP 요청과 응답을 주고받기 전에 TCP가 먼저 연결되어야 한다.
- HTTP/1.0에서는 각 Http 요청/응답마다 따로 TCP 연결을 했으나 비효율적인 측면이 커서 해당 부분을 보완하기 위해 HTTP/1.1에서는 파이프라이닝을 도입하여 Connection 헤더를 통해 TCP 연결을 제어하여 일관된 연결을 유지하고자 하였으나 구현하기가 어려웠다. HTTP/2에서는 한 연결당 메시지를 다중화(멀티플렉싱)하여 연결을 유지하고 효율성을 높였다.
- HTTP를 위한 더 효과적인 transport 레이어 프로토콜을 개발중에 있으며 구글은 QUIC라는 UDP 기반의 신뢰 가능한 프로토콜을 개발하고 있다.
<br>
<br>

### Client (User-agent)
- 유저의 역할을 대신 해주는 툴로 웹 브라우저가 대표적이다.
- 요청을 시작하는 주체
- 서버에 HTML 문서 요청 > 파싱 > 문서안의 하위 리소스 요청이나 스크립트로 발생하는 추가 요청 생성 > 조합 > 완성된 페이지를 유저에게 보여준다.
- 사용자가 웹 페이지 안의 특정 텍스트를 통해 User-agent에게 한 지시를 Http 요청으로 변환하고 서버로 부터 전달 받은 응답 메시지를 해석하여 유저에게 보여준다.
<br>
<br>

### Web Server
- 클라이언트의 요청에 따라 다큐먼트를 제공한다.
- 서버는 (가상적으로 혹은 개념적으로) 단일 머신으로 표현된다.
- HTTP/1.1과 Host 헤더를 통해 같은 IP 주소를 공유할 수 있다.
<br>
<br>

### Proxies
- 서버와 클라이언트 중간에서 Http 메시지를 전달(릴레이)하는 머신들
- 웹 스택의 계층적 구조 때문에 릴레이 행위는 대체로 transport, network, physical 수준에서 동작하지만 성능에 영향을 줄 수 있으므로 이러한 것을 애플리케이션 레이어에서 처리하는 것이 프록시
- transparent : 받은 요청을 변경 없이 전달
- non-transparent : 받은 요청을 일부분 변경하여 전달
- 기능
  - 캐싱
  - 필터 (안티바이러스 스캔, 자녀보호)
  - 로드 밸런싱
  - 인증
  - 로깅
<br>
<br>

### Messages
- HTTP/1.1에서는 사람이 읽을 수 있었다.
- HTTP/2부터 프레임(바이너리 구조)로 내장되어 헤더의 압축이나 멀티플렉싱이 가능하게 되었다.
- 일부 메시지만 HTTP/2로 보내져도 각 메시지의 시멘틱은 변하지 않아서 클라이언트는 HTTP/1.1 요청을 재구성한다. 그래서 HTTP/1.1 형식으로 HTTP/2 메시지를 포함시킬 수 있다.
<br>
<br>

#### Request
- Method : GET, POST, OPTIONS, HEAD 등이 있고 클라이언트가 원하는 동작들을 정의한다. 리소스를 가져오고 싶을 경우 GET, HTML form을 전달하고 싶을 경우 POST를 사용한다.
- Path : URL에서 프로토콜이나 도메인과 TCP 포트를 제거한 가져올 리소스의 위치
- HTTP Protocol : 현재 사용중인 프로토콜
- Headers : 서버를 위한 추가적인 정보 전달
- Body : POST 메소드를 위한 것으로 보내질 리소스가 담겨 있다.
<br>
<br>

#### Response
- Http Protocol : 현재 사용중인 프로토콜
- Status Code : 요청이 성공적인지 아닌지와 이유를 나타내는 지표
- Status Message : 상태 코드에 대한 권한이 없는 간단한 설명
- Headers : request와 같은 헤더들
- Body : 전달할 리소스 (Optional)
<br>
<br>

### APIs
- XMLHttpRequest : user-agent와 서버간에 데이터를 교환할 목적으로 사용되는 API이다. 비슷한 기능을 하는 더 현대적인 Fetch API라는 것도 있다.
- EventSource 인터페이스 : 클라이언트가 커넥션을 열고 이벤트 핸들러를 구성하여 서버로부터 전달된 HTTP 스트림을 Event 객체로 변환한다. 변환된 타입의 객체를 이벤트 핸들러에 전달한다. 특정한 이벤트 핸들러를 구성하지 않을경우 onmessage 이벤트 핸들러가 사용된다.
<br>
<br>

### References
- [https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview)
