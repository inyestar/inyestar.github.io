---
layout: default
title: 2nd
parent: ALONE
---

# 2021년 11월 17일

## NOTE
#### Messenger
- Archictecture
  - Kafka : 메시지 브로커
  - Redis : 캐싱
  - WebSocket : 클라이언트-서버 통신 프로토콜
- Kafka
  - Apache Zookeeper : 클러스터간에 데이터 동기화 담당
    - 단순히 데이터 동기화만 담당하지 않고 많은 역할을 하는 듯
    - 마스터 노드 선택, 업무 분배, 살아있는 노드 파악 등
    - 서비스의 기능 개발 이외에 신경쓰지 말라고 그 밖에 업무 다 처리해주는 거 같음

## PRACTICE
- Kafka
  ```bash
  # docker-compose.yml로 zookeeper, kafka 생성
  $ sudo docker-compose up -d --build
  # create topic
  $ sudo docker exec kafka \
        kafka-topics \
        --create --topic testTopic \
        --bootstrap-server localhost:29092 \
        --replication-factor 1 \
        --partitions 1
  # list topics
  $ sudo docker exec kafka \
        kafka-topics \
        --list \
        --bootstrap-server localhost:29092
  # test producer
  $ sudo docker exec kafka  \
        bash -c "seq 50 | kafka-console-producer --request-required-acks 1 --broker-list localhost:29092 --topic testTopic && echo 'Produced 50 messages.'"
  # test consumer
  $ sudo docker exec kafka  \
        kafka-console-consumer \
        --bootstrap-server localhost:29092 \
        --topic testTopic \
        --from-beginning
  ```
  - 💡 클라이언트들이 브로커에 연결할 때 사용되기 때문에 Listener에 관련된 환경변수 설정이 아주 중요함
  - KAFKA_ADVERTISED_LISTENERS (리스너이름:주소) : 리스너들에 대한 실제 주소를 정의하는데 각 리스너마다 포트가 달라야함 이 주소를 정확하게 써주지 않으면 외부 클라이언트가 localhost로 접속 시도하려고 함

## REFERENCES
- https://www.endpointdev.com/blog/2020/04/messaging-app-spring-kafka-pt-one/
- https://www.baeldung.com/ops/kafka-docker-setup
- https://docs.confluent.io/3.2.2/installation/docker/docs/quickstart.html
