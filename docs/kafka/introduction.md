---
layout: default
title: Introduction
parent: kafka
---

# Introduction
| 날짜 | 2021년 09월 08일 |
|:----------|:-------------------------------------|
| 출처 | https://howtodoinjava.com/java/io/how-java-io-works-internally/ |

### Event Streaming
- 사람의 중추 신경계와 같은 역할
- Stream 형태의 데이터를 실시간으로 캡쳐하는 방식
- 데이터의 지속적인 흐름을 보장하여 'always-on'을 가능하게 함

### Apache Kafka
- 이벤트 스트리밍 플랫폼
- 지속적으로 import/export할 수 있는 이벤트 스트림을 publish 하거나 subscribe할 수 있음
- 원하는 기간만큼 안정적으로 이벤트 스트림을 저장할 수 있음
- 이벤트 발생 시 바로 혹은 소급하여 이벤트 스트림을 처리할 수 있음
- 서버와 클라이언트로 구성되어 있으며 TCP 네트워크 프로토콜로 통신함
- 하나 이상의 서버가 클러스터 형태로 운영됨

### Terminology
- Event : kafka에서 데이터 스트림 처리 시 사용하는 논리적 개념으로 key, value, timestamp, 기타 헤더 정보를 가지고 있음
- Producer : kafka에 이벤트를 publish(write)하는 모든 클라이언트 애플리케이션
- Consumer : Producer가 제공한 이벤트를 subscribe(read/process)하는 클라이언트 애플리케이션
- Broker : kafka 서버
- Topic : 이벤트가 분류되는 형태로 파일시스템의 폴더와 유사한 개념이며 이벤트들은 이 폴더에 존재하게 됨
- Partition : 토픽이 분산되어 저장될 때 해당 위치(location)를 구분짓는 개념으로 같은 key를 가진 이벤트는 같은 파티션에 추가(append)됨
![Topic](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/e76f5cd9-0a49-4142-a1f1-cab414ebe1b8/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210908%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210908T095154Z&X-Amz-Expires=86400&X-Amz-Signature=3859041224e1a04c5b33886db22a0bc47f57965a8aa1edda7df2de0514b56ff7&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

### Quick Start
- Pre-Requisites
  * docker
    ```bash
      # yum-utils 설치
      $ sudo yum install -y yum-utils
      # yum repolist에 docker용 추가
      $ sudo yum-config-manager \
          --add-repo \
          https://download.docker.com/linux/centos/docker-ce.repo
      # docker-ce, docker-ce-cli, containerd.io 설치
      $ sudo yum -y install docker-ce docker-ce-cli containerd.io
      # systemctl로 docker 서비스 시작
      $ sudo systemctl start docker
      # hello-world 이미지 실행
      $ sudo docker run hello-world
    ```
  * docker-compose
    ```bash
      # github에서 os와 arch에 맞는 docker-compose 파일을 받아서 /usr/local/bin/docker-compose에 저장
      $ sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
      # 내려받은 파일에 실행권한 추가
      $ sudo chmod +x /usr/local/bin/docker-compose
      # sudo 명령어 사용을 위해 /usr/bin 디렉토리에 docker-compose 링크 생성
      $ sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
      # version 확인 명령어로 잘 설치되었는지 확인
      $ docker-compose --version
    ```
  * 방화벽 추가
    ```bash
      # local 테스트만 진행할 경우 방화벽 설정 안해도 상관 없음
      # systemctl stop firewalld로 서비스 중지 해도 됨
      # kafka 브로커 포트 추가
      $ sudo firewall-cmd --permanent --add-port=9092/tcp
      # zookeeper 포트 추가
      $ sudo firewall-cmd --permanent --add-port=2181/tcp
      # 방화벽 정책 리로딩
      $ sudo firewall-cmd --reload
      # 현재 zone의 정책 확인
      $ sudo firewall-cmd --list-all
    ```
- Kafka
  ```bash
    # /home 디렉토리로 이동
    $ cd /home
    # --- 사이의 내용 작성
    $ vi docker-compose.yml
      ---
      version: '3'
      services:
      zookeeper:
        image: confluentinc/cp-zookeeper:6.2.0
        hostname: zookeeper
        container_name: zookeeper
        environment:
          ZOOKEEPER_CLIENT_PORT: 2181
          ZOOKEEPER_TICK_TIME: 2000

      broker:
        image: confluentinc/cp-kafka:6.2.0
        container_name: broker
        ports:
          - "9092:9092"
        depends_on:
          - zookeeper
        environment:
          KAFKA_BROKER_ID: 1
          KAFKA_ZOOKEEPER_CONNECT: 'zookeeper:2181'
          KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_INTERNAL:PLAINTEXT
          KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092,PLAINTEXT_INTERNAL://broker:29092
          KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
          KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
          KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
      ---
    # docker-compose.yml을 통해 kafka 도커 컨테이너 올림
    $ sudo docker-compose up -d
  ```

### Advanced
- zookeeper (TODO)

### References
[https://kafka.apache.org/documentation/#gettingStarted](https://kafka.apache.org/documentation/#gettingStarted)
[https://docs.docker.com/engine/install/centos/](https://docs.docker.com/engine/install/centos/)
[https://docs.docker.com/compose/install/](https://docs.docker.com/compose/install/)
[https://tecadmin.net/install-python-3-8-centos/](https://tecadmin.net/install-python-3-8-centos/)
[https://developer.confluent.io/get-started/python/#introduction](https://developer.confluent.io/get-started/python/#introduction)
[https://logdeveloper.github.io/python/python-mariadb-example/](https://logdeveloper.github.io/python/python-mariadb-example/)
