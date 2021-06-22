---
layout: default
title: Install On CentOS
parent: Docker
---

# Install on CentOS

| 날짜 | 2021년 1월 31일 |
|:----------|:-------------------------------------|
| 출처 | https://docs.docker.com/engine/install/centos/ |

- **전제조건**
    - Root 계정 혹은 sudo를 사용할 수 있는 계정으로 접속
    - centos-extras 레포지토리 활성화

    ```bash
    # yum repolist | grep extras
    ```

    - overay2 스토리지 드라이버 추천 (not followed)
    - 이전 버전 (docker, docker-engine) 삭제
- **레포지토리를 통한 설치**
    - yum-config-manager 사용을 위해 yum-utils 설치

    ```bash
    # yum install -y yum-utils
    # yum-config-manager --version
    ```

    - docker 레포지토리 추가

    ```bash
    # yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    # ls /etc/yum.repos.d/docker-ce.repo
    ```

    - docker 엔진 설치 (docker 그룹도 자동 추가됨)

    ```bash
    # yum install -y docker-ce docker-ce-cli containerd.io
    # docker version
    # cat /etc/group | grep docker
    ```

    - docker 시작 후 image 실행

    ```bash
    # systemctl start docker
    # ps -ef | grep docker
    # docker run hello-world
    ```

- **Docker 엔진 삭제**
    - Docker 엔진, CLI, Containerd 패키지 삭제

    ```bash
    # yum remove docker-ce docker-ce-cli containerd.io
    ```

    - 이미지, 컨테이너, 볼륨 파일은 수동으로 삭제 필요

    ```bash
    # rm -rf /var/lib/docker
    ```

- **트러블슈팅**
    - Unable to find image 'hello-world:latest' locally

    ```bash
    # docker login --username ${dockerID}
    ```

    - Unauthorized: incorrect username or password
    docker hub에서 패스워드 확인 필요
