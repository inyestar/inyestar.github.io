---
layout: post
title: Jenkins Installation
tags: [Jenkins]
---

#### CI/CD
- Github Action을 쓸 경우 Docker Local Registry 사용 불가
- Jenkins 서버는 로컬에서 사용가능하니까 Local Registry 사용 가능
- Steps
  - push to Github
  - mvn install in Jenkins
  - build docker image in Jenkins
  - push docker image to local Repository in Jenkins
  - start docker container in Jenkins (maybe)
<br>
<br>

### Configuration
- 언어 설정
  - 💡 한국어 번역이 어설퍼서 영어로 변경하는 편이 좋음
  - jenkins 관리 > 플러그인 관리 > locale 플러그인 설치
  - jenkins 관리 > 환경설정 > locale의 Default Language > en_US 입력
  - Ignore browser preference and force this language to all users 체크
  - apply and save
- maven 추가
  - 💡 생성하지 않으면 mvn 찾지 못하여 build 실패
  - Manage Jenkins > Global Tool Configuration > Maven의 Maven installation 클릭
  - add maven 클릭
  - Name 입력
  - Version 선택
  - apply and save
- docker 연동
  - 💡 docker registry를 등록할 때 host:port 형식으로 등록하지 않으면 이미지 push할 때 default로 접속함 (80이나 443도 마찬가지)
  - Manage Jenkins > Plugin manager > Available 탭 클릭
  - 검색 창에 docker 입력
  - Docker와 CloudBees Docker Build and Publish 플러그인 선택
  - 하단의 Install without restart 클릭
<br>
<br>

### Installation
- Network
    ```bash
    $ sudo docker network create jenkins
    ```
- Docker in Docker
    ```bash
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
    ```
- Jenkins
    ```bash
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
- Docker Local Registry
    ```bash
    $ sudo docker run -d \
          -p 5000:5000 \
          --restart=always \
          --name inminhub \
          -v inminhub:/var/lib/registry \
          --network jenkins \
          registry:2
    $ sudo docker pull hello-world
    $ sudo docker tag hello-world localhost:5000/hello
    $ sudo docker push localhost:5000/hello
    $ sudo docker image remove hello-world
    $ sudo docker image rm localhost:5000/hello
    $ sudo docker run localhost:5000/hello
    $ sudo docker volume inspect inminhub
      -----
      "Mountpoint": "/var/lib/docker/volumes/inminhub/_data",
      -----
    # 등록된 repository
    $ ll /var/lib/docker/volumes/inminhub/_data/docker/registry/v2/repositories/
    ```
- docker-compose로 전체 구성 : [alone-ci-cd](https://github.com/inminhouse/alone-ci-cd)
<br>
<br>

### Troubleshooting
#### temporarily enable Common Name matching with GODEBUG=x509ignoreCN=0
- 원인 : golang 버전 올라가면서 wildcard 인증서가 아닌 SAN 인증서를 만들어야 하는 것 같다.
- 해결 : 임시 방편으로 Docker in Docker 환경변수로 GODEBUG=x509ignoreCN=0 사용 했다
- ref : https://ikcoo.tistory.com/143

#### certificate signed by unknown authority
- 원인 : 모르겠다..Docker in Docker가 inminhub 인증서를 reject하는거 같은데...
- 해결 : docker in docker에 inminhub 인증서 추가해주니까 작동한다..아직 TLS는 어려워~~~ client에 대한 인증은 도메인별로 디렉토리를 가지고 있는건가?
- ref : https://forums.docker.com/t/self-signed-private-registry-error-certificate-signed-by-unknown-authority/45129
<br>
<br>

## References
- [https://www.jenkins.io/doc/book/installing/docker/](https://www.jenkins.io/doc/book/installing/docker/)
- [https://docs.docker.com/registry/deploying/](https://docs.docker.com/registry/deploying/)
