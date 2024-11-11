---
layout: post
title: Kafka Concepts
categories: ["kafka", "message", "queue"]
---

### Prerequsites
- Zookeeper 서비스 up
- Kafka Broker 서비스 3개 up
- docker-compose로 구성 : [alone-message](https://github.com/inminhouse/alone-message)

<br>
<br>

### Event
- "Something happend"
- Record 혹은 Message 라고도 불림
- 이 "이벤트" 형식으로 Kafka에서 데이터를 읽거나 쓸 수 있음
- key, value, timestamp, metadata (optional)로 구성됨
- 즉, Kafka가 처리하는 가장 작은 크기의 논리적 데이터 단위
- 💡 Event 사이즈 별 Kafka 성능 테스트가 필요하지 않을까? 문서에서는 문제 없다고 말하지만.
<br>
<br>

### Producer
- Kafka에 이벤트를 publish (write) 하는 클라이언트
- Test
  - 존재하는 Topic에 이벤트 생성
      ```bash
      $
      ```
  - 존재하지 않은 Topic에 이벤트 생성
      ```bash
      $
      ```
<br>
<br>

### Consumer
- Producer가 생성한 이벤트를 subscribe (read/process) 하는 클라이언트
- Consumer와 Producer는 완전이 독립적이기 때문에 서로의 존재에 영향을 받지 않음
- Consumer에 의해 이벤트가 소비된 후에도 Kafka는 이벤트를 삭제 않기 때문에 필요한 만큼 같은 이벤트를 계속 Subscribe 할 수 있음
- Test
  - 존재하는 Topic에 이벤트 조회
      ```bash
      $
      ```
  - 존재하지 않은 Topic에 이벤트 조회
      ```bash
      $
      ```
  - 특정 Topic의 이벤트 조회
      ```bash
      $
      ```
<br>
<br>

### Topic
- Events를 조직하고 저장하는 단위
- Events는 파일들, Topic은 폴더
- Topic의 Producer와 Consumer는 존재하지 않을 수도 있고 하나 혹은 다수로 존재할 수 있음
- Topic 당 이벤트가 Kafka 내에서 삭제되지 않고 유지될 수 있도록 기간을 정의 할 수 있으며 기간이 지난 이벤트는 삭제됨
- Topic은 하나의 Broker에 저장되는 것이 아니고 여러 Broker에 분산되어 저장됨
- 모든 Topic은 복제가 가능함 (보통 복제본 개수는 3의 배수로 설정)
- Test
  - Topic 생성
      ```bash
      $ docker exec kafka_1 \
              kafka-topics \
              --bootstrap-server localhost:9092,kafka_2:9092,kafka_3:9092 \
              --create --topic chat01 \
              --partitions 3 \
              --replication-factor 3
      # Created topic chat01.
      ```
  - Topic 중복 생성
      ```bash
      $ docker exec kafka_1 \
              kafka-topics \
              --bootstrap-server localhost:9092,kafka_2:9092,kafka_3:9092 \
              --create --topic chat01 \
              --partitions 3 \
              --replication-factor 3
      # Error while executing topic command : Topic 'chat01' already exists.
      ```
  - Topic 조회
      ```bash
      $ docker exec kafka_1 \
              kafka-topics \
              --bootstrap-server localhost:9092,kafka_2:9092,kafka_3:9092 \
            	--list
      # chat01
      ```
  - Topic 삭제
      ```bash
      $ docker exec kafka_1 \
              kafka-topics \
              --bootstrap-server localhost:9092,kafka_2:9092,kafka_3:9092 \
              --delete \
              --topic chat01
      $ docker exec kafka_1 \
              kafka-topics \
              --bootstrap-server localhost:9092,kafka_2:9092,kafka_3:9092 \
            	--list
      # <empty>
      ```
<br>
<br>

### Partition
- 특정 Topic 용 이벤트가 생성되면 해당 Topic의 Partition 중 하나에 추가 되는 것
- 같은 Event key를 가진 이벤트는 같은 Partition에 기록 됨
- Event는 Partition에 기록된 순선데로 소비할 수 있음
- 하나의 Topic이 여러개의 Partition으로 들어갈 경우 각 파티션끼리의 순서는 보장되지 않음
- Test
  - Partition 1개 생성
      ```bash
      $
      ```
  - Partition 8개 생성
      ```bash
      $
      ```
<br>
<br>

### Offset
- Consumer의 현재 위치
<br>
<br>

### References
- [https://kafka.apache.org/documentation/#design](https://kafka.apache.org/documentation/#design)
- [https://akageun.github.io/2020/05/01/docker-compose-kafka-cluster-manager.html](https://akageun.github.io/2020/05/01/docker-compose-kafka-cluster-manager.html)
