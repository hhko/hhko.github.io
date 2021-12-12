---
title: Dapr
#linkTitle: OpenSearch 링크 제목
description: Ubuntu 20.04 LTS 환경에서 Dapr 설치
type: docs
weight: 2
#no_list: true
#main_menu: true
#content_type: concept
---

## 명령어 요약
```shell
# Dapr CLI 설치(Root 권한일 때)
wget -q https://raw.githubusercontent.com/dapr/cli/master/install/install.sh -O - | /bin/bash

# Dapr Runtimer 설치
dapr init

# Dapr CLI & Runtime 버전 확인
dapr --version

# Dapr Runtime 파일 확인
ls -al $HOME/.dapr
```

## Dapr CLI 설치(Root 권한일 때)
- 참고 자료
  - [Install the Dapr CLI](https://docs.dapr.io/getting-started/install-dapr-cli/)
- Dapr CLI 설치 명령어
  ```shell
  wget -q https://raw.githubusercontent.com/dapr/cli/master/install/install.sh -O - | /bin/bash
  ```
  - `-q`, `--quiet` : quiet (no output)
  - `-O`, `--output-document=FILE` : write documents to FILE
  - `-O -` : 텍스트 출력
  - `https://raw.githubusercontent.com/dapr/cli/master/install/install.sh` 파일을 다운로드 받아 bash 쉘에게 실행 시킨다.
- 설치 과정
  ```shell
  root@HHKO-LABTOP:~# wget -q https://raw.githubusercontent.com/dapr/cli/master/install/install.sh -O - | /bin/bash
  Getting the latest Dapr CLI...
  Your system is linux_amd64
  Installing Dapr CLI...
  
  Installing v1.5.1 Dapr CLI...
  Downloading https://github.com/dapr/cli/releases/download/v1.5.1/dapr_linux_amd64.tar.gz ...
  dapr installed into /usr/local/bin successfully.
  CLI version: 1.5.1
  Runtime version: n/a
  
  To get started with Dapr, please visit https://docs.dapr.io/getting-started/
  ```
- Dapr CLI 버전 화인
  ```shell
  root@HHKO-LABTOP:~# dapr --version
  CLI version: 1.5.1
  Runtime version: n/a                # Dapr Runtime은 아직 설치가 안된 상태이다.
  ```

## Dapr Runtime 설치
- 참고 사이트
  [Initialize Dapr in your local environment](https://docs.dapr.io/getting-started/install-dapr-selfhost/)
- Dapr Runtime 설치 명령어
  ```shell
  dapr init
  ```
- 설치 과정
  ```shell
  root@HHKO-LABTOP:~# dapr init
  ⌛  Making the jump to hyperspace...
  ℹ️  Installing runtime version 1.5.1
  ↗  Downloading binaries and setting up components...
  Dapr runtime installed to /root/.dapr/bin, you may run the following to add it to your path if you want to run daprd directly:
      export PATH=$PATH:/root/.dapr/bin
  ✅  Downloading binaries and setting up components...
  ✅  Downloaded binaries and completed components set up.
  ℹ️  daprd binary has been installed to /root/.dapr/bin.
  ℹ️  dapr_placement container is running.
  ℹ️  dapr_redis container is running.
  ℹ️  dapr_zipkin container is running.
  ℹ️  Use `docker ps` to check running containers.
  ✅  Success! Dapr is up and running. To get started, go here: https://aka.ms/dapr-getting-started
  ```

## Dapr Runtime 설치 확인
- Dapr Runtime 버전 확인
  ```shell
  root@HHKO-LABTOP:~# dapr --version
  CLI version: 1.5.1
  Runtime version: 1.5.1             # Dapr Runtime 버전
  ```
- 도커 컨테이너 확인
  ```shell
  root@HHKO-LABTOP:~# docker container ls
  CONTAINER ID   IMAGE               COMMAND                  CREATED         STATUS                   PORTS                              NAMES
  5025e828428d   daprio/dapr:1.5.1   "./placement"            7 minutes ago   Up 7 minutes             0.0.0.0:50005->50005/tcp           dapr_placement
  db441b54b714   redis               "docker-entrypoint.s…"   7 minutes ago   Up 7 minutes             0.0.0.0:6379->6379/tcp             dapr_redis
  9f50ede2ba24   openzipkin/zipkin   "start-zipkin"           7 minutes ago   Up 7 minutes (healthy)   9410/tcp, 0.0.0.0:9411->9411/tcp   dapr_zipkin
  ```
  - `dapr_placement` : daprio/dapr:1.5.1
  - `dapr_redis` : redis
  - `dapr_zipkin` : openzipkin/zipkin
- 도커 이미지 확인
  ```shell
  root@HHKO-LABTOP:~# docker image ls
  REPOSITORY          TAG       IMAGE ID       CREATED       SIZE
  daprio/dapr         1.5.1     226aa2a3ceeb   4 days ago    214MB
  redis               latest    aea9b698d7d1   8 days ago    113MB
  openzipkin/zipkin   latest    353c2229bf24   11 days ago   155MB
  ```
- 설치 파일 확인 : `$HOME/.dapr`
  ```shell
  root@HHKO-LABTOP:~# ls -al $HOME/.dapr
  total 20
  drwxr-xr-x 4 root root 4096 Dec 11 18:39 .
  drwx------ 4 root root 4096 Dec 11 18:39 ..
  drwxrwxrwx 3 root root 4096 Dec 11 18:39 bin
  drwxrwxrwx 2 root root 4096 Dec 11 18:39 components
  -rw-r--r-- 1 root root  187 Dec 11 18:39 config.yaml
  ```
  - `$HOME/.dapr` 폴더 구성
    ```shell
    ├── bin              # 실행 파일 폴더
    │   ├── daprd        # 서비스
    │   ├── dashboard    # 대시보드
    │   └── web
    │       └── ...
    ├── components       # 컴포넌트
    │   ├── pubsub.yaml
    │   └── statestore.yaml
    └── config.yaml      # 기본 설정
    ```
    
## TODO
- 특정 버전 설치
- 업그레이드
