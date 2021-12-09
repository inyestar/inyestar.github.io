---
layout: post
title: Docker Installation On CentOS
tags: [Docker]
---

### Prerequisites
- Root 계정 혹은 sudo를 사용할 수 있는 계정으로 접속
- centos-extras 레포지토리 활성화
    ```bash
    $ sudo yum repolist | grep extras
    ```
- overay2 스토리지 드라이버 추천 (not followed)
- 이전 버전 (docker, docker-engine) 삭제
<br>
<br>

### Installation
- yum-config-manager 사용을 위해 yum-utils 설치
    ```bash
    $ sudo yum install -y yum-utils
    $ sudo yum-config-manager --version
    ```
- docker 레포지토리 추가
    ```bash
    $ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    $ sudo ls /etc/yum.repos.d/docker-ce.repo
    ```
- docker 엔진 설치 (docker 그룹도 자동 추가됨)
    ```bash
    $ sudo yum install -y docker-ce docker-ce-cli containerd.io
    $ sudo docker version
    $ sudo cat /etc/group | grep docker
    ```
- docker 시작 후 image 실행
    ```bash
    $ sudo systemctl start docker
    $ sudo ps -ef | grep docker
    $ sudo docker run hello-world
    ```
<br>
<br>

### Usage
- Docker 엔진, CLI, Containerd 패키지 삭제
    ```bash
    $ sudo yum remove docker-ce docker-ce-cli containerd.io
    ```
- 이미지, 컨테이너, 볼륨 파일은 수동으로 삭제 필요
    ```bash
    $ sudo rm -rf /var/lib/docker
    ```
<br>
<br>

### Troubleshooting
- Unable to find image 'hello-world:latest' locally
    ```bash
    $ sudo docker login --username ${dockerID}
    ```
- Unauthorized: incorrect username or password
docker hub에서 패스워드 확인 필요
<br>
<br>

### References
- [https://docs.docker.com/engine/install/centos/](https://docs.docker.com/engine/install/centos/)
