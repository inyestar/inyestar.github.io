---
layout: default
title: 2nd
parent: ALONE
---

# 2021년 11월 16일

## NOTE
#### Jenkins
- 언어 설정
  💡 한국어 번역이 어설퍼서 영어로 변경하는 편이 좋음
  1) jenkins 관리 > 플러그인 관리 > locale 플러그인 설치
  2) jenkins 관리 > 환경설정 > locale의 Default Language > en_US 입력
  3) Ignore browser preference and force this language to all users 체크
  4) apply and save
- maven 추가
  💡 생성하지 않으면 mvn 찾지 못하여 build 실패
  1) Manage Jenkins > Global Tool Configuration > Maven의 Maven installation 클릭
  2) add maven 클릭
  3) Name 입력
  4) Version 선택
  5) apply and save
- docker 연동
  💡 docker registry를 등록할 때 host:port 형식으로 등록하지 않으면 이미지 push할 때 default로 접속함 (80이나 443도 마찬가지)
  1) Manage Jenkins > Plugin manager > Available 탭 클릭
  2) 검색 창에 docker 입력
  3) Docker와 CloudBees Docker Build and Publish 플러그인 선택
  4) 하단의 Install without restart 클릭

## PRACTICE
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
- docker-compose로 전체 구성
  https://github.com/inminhouse/alone-ci-cd

## TROUBLESHOOTING
```console
temporarily enable Common Name matching with GODEBUG=x509ignoreCN=0
```
- 원인 : golang 버전 올라가면서 wirdcard 인증서가 아닌 SAN 인증서를 만들어야 하는 것 같다. 허허 그게 모람
- 해결 : 임시 방편으로 Docker in Docker 환경변수로 GODEBUG=x509ignoreCN=0 사용 했다
- ref : https://ikcoo.tistory.com/143
```console
certificate signed by unknown authority
```
- 원인 : 모르겠다..Docker in Docker가 inminhub 인증서를 reject하는거 같은데...
- 해결 : docker in docker에 inminhub 인증서 추가해주니까 작동한다..아직 TLS는 어려워~~~ client에 대한 인증은 도메인별로 디렉토리를 가지고 있는건가?
- ref : https://forums.docker.com/t/self-signed-private-registry-error-certificate-signed-by-unknown-authority/45129

## REFERENCES
- Docker
  - https://docs.docker.com/registry/deploying/
