---
layout: default
post: 1st
parent: ALONE
---

# 2021년 11월 15일

## NOTE
#### CI/CD
  - Github Action을 쓸 경우 Docker Local Registry 사용 불가
  - Jenkins 서버는 로컬에서 사용가능하니까 Local Registry 사용 가능
  - Steps
    - push to Github
    - mvn install in Jenkins
    - build docker image in Jenkins
    - push docker image to local Repository in Jenkins
    - start docker container in Jenkins (maybe)

## PRACTICE
- Jenkins
  ```bash
  $ sudo docker network create jenkins
  $ sudo docker run \
        --name jenkins-docker \
        --rm \
        --detach \
        --privileged \
        --network jenkins \
        --network-alias docker \
        --env DOCKER_TLS_CERTDIR=/certs \
        --volume jenkins-docker-certs:/certs/client \
        --volume jenkins-data:/var/jenkins_home \
        --publish 2376:2376 \
        docker:dind \
        --storage-driver overlay2
  $ sudo vi Dockerfile
    -----
    FROM jenkins/jenkins:2.303.3-jdk11
    USER root
    RUN apt-get update && apt-get install -y lsb-release
    RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
    https://download.docker.com/linux/debian/gpg
    RUN echo "deb [arch=$(dpkg --print-architecture) \
    signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
    https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list
    RUN apt-get update && apt-get install -y docker-ce-cli
    USER jenkins
    RUN jenkins-plugin-cli --plugins "blueocean:1.25.1 docker-workflow:1.26"
    -----
  $ sudo docker build -t myjenkins-blueocean:1.1 .
  $ sudo docker run \
        --name jenkins-blueocean \
        --rm \
        --detach \
        --network jenkins \
        --env DOCKER_HOST=tcp://docker:2376 \
        --env DOCKER_CERT_PATH=/certs/client \
        --env DOCKER_TLS_VERIFY=1 \
        --publish 8080:8080 \
        --publish 50000:50000 \
        --volume jenkins-data:/var/jenkins_home \
        --volume jenkins-docker-certs:/certs/client:ro \
        myjenkins-blueocean:1.1
  # ${IP}:8080으로 접속
  # admin password 입력
  $ sudo docker exec jenkins-blueocean cat /var/jenkins_home/secrets/initialAdminPassword
  # installation 진행
  ```
  💡 Docker안에 Docker가 왜 떠있어야 하는지 의문이다. 공식 문서에 의하면 docker-cli가 공식 jenkins 이미지에 포함되어 있지 않고 블루오션 플러그인을 사용하기 위해서라고 하는데 블루 오션이 뭔지 모르고 docker-cli가 왜 필요한지 모르겠다.

## REFERENCES
- Jenkins
  - https://www.jenkins.io/doc/book/installing/docker/