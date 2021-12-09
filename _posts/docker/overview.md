---
layout: default
title: Overview
parent: Docker
---

# Overview

| 날짜 | 2021년 02월 02일 |
|:----------|:-------------------------------------|
| 출처 | https://docs.docker.com/get-started/overview/ |

## Architecture
- Client-Server 아키텍쳐
  Docker client와 deamon은 한 서버에 함께 있거나 서로 다른 서버에서 운영될 수도 있다. 두 서비스는 REST API로 통신한다.
- Docker deamon (dockerd)
  API 요청을 수신하며, 이미지, 컨테이너, 네트워크, 볼륨과 같은 docker의 object를 관리한다. 데몬은 다른 데몬과 통신이 가능하다.
- Docker client (docker)
  API를 이용하여 데몬에게 명령어를 전달한다. 하나 이상의 데몬과 통신이 가능하다.
- Docker registries
  Docker 이미지를 저장한다. Docker Hub는 공개된 레지스트리로 Docker는 기본적으로 이 곳에서 이미지를 찾도록 설정되어 있다. 개인 레지스트리 또한 운영할 수 있다. docker pull 이나 docker run 명령어를 사용할 때, 필요한 이미지는 설정된 레지스트리 공간에서 가져올 수 있다. docker push 명령어로 해당 레지스트리에 이미지를 저장할 수 있다.

## Object
- 이미지
  이미지는 컨테이너를 생성하기 위한 읽기 전용 템플릿이다. 이미지는 추가적인 커스터마이징을 할 수 있는 여러 설정에 대한 내용도 담고 있다. 이미지를 생성하기 위해서는 몇 가지 세부적인 내용이 정의된 Dockerfile을 생성해야 한다. 파일 내에 세부적인 지시 사항들은 이미지 안에서 각각의 레이어를 생성한다. 그래서 파일을 수정하게되면 이 각 레이어들만이 변경되며, 이 방식으로 이미지 파일을 가볍게 유지할 수 있다.
- 컨테이너
  실행가능한 이미지 인스턴스로 API나 CLI를 통해 생성, 삭제, 시작, 멈춤 등이 가능하다. 호스트 머신과 다른 컨테이너로부터 네트워크, 저장소, 시스템적 요소들을 독립적으로 운영할 수 있다. 컨테이너는 이미지와 설정 값들로 만들어진다. 컨테이너가 삭제되면 영구적인 저장소에 저장되지 않은 값들은 사라지게 된다.

## Example
```bash
# docker run -i -t ubunt /bin/bash
```
1. ubunt 이미지가 로컬 저장소에 없으면, 설정된 레지스트리로부터 이미지를 가져온다.
(docker pull ubunt 명령어와 동일)
2. Docker는 새로운 컨테이너를 생성한다.
(docker container create 명령어와 동일)
3. Docker는 마지막 레이어로 read-write 파일 시스템을 컨테이너에게 할당한다. 이렇게 함으로써 컨테이너는 파일이나 디렉토리를 생성하거나 편집할 수 있다.
4. 네트워크 옵션에 대한 정의를 따로 하지 않으면 Docker는 컨테이너를 기본 네트워크에 연결하기 위해 네트워크 인터페이스를 생성한다. 이 과정에는 IP 주소 할당이 포함되어 있다. 기본적으로 컨테이너는 호스트 머신의 네트워크를 사용해 외부 네트워크에 연결할 수 있다.
5. Docker가 컨테이너를 시작하고 /bin/bash 를 실행한다. -i와 -t 옵션 때문에 해당 컨테이너는 사용자의 터미널에서 상호작용이 가능하므로 키보드를 이용하여 입력값을 주고 터미널상에서 출력값을 확인할 수 있다.
6. /bin/bash를 종료하기위해 exit을 입력하면 컨테이너는 멈추게 되지만 삭제되지는 않는다. 따라서 나중에 다시 시작하거나 삭제할 수 있다.

## Technology
- Docker는 Go 언어로 작성되었으며 Linux 커널의 몇 가지 특징을 사용하고 있다. Docker는 각각 고립된 레이어를 제공하는 namespace  기술로 컨테이너라고 불리는 독립된 작업 공간을 제공하고 있다. 컨테이너를 시작할 때 Docker는 해당 컨테이너에 대한 namespace 집합을 생성한다. 컨테이너의 각 측면은 분리된 namespace 안에서 운영되며 서로의 구역에대한 접근이 제한되어 있다.