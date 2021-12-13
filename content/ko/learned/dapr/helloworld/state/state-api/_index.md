---
title: State API
#linkTitle: OpenSearch 링크 제목
description: Redis 상태 읽기 및 쓰기 예제
#type: docs
weight: 1
#no_list: true
#main_menu: true
#content_type: concept
---

## 명령어 요약
```shell
# Dapr Sideccar 실행
dapr run --app-id myapp --dapr-http-port 3500

# Dapr Port 확인 : 3500
apt install -y net-tools
netstat -nap | grep LISTEN | grep daprd

# 상태 쓰기
curl -X POST -H "Content-Type: application/json" -d '[{ "key": "name", "value": "Bruce Wayne"}]' http://localhost:3500/v1.0/state/statestore

# 상태 읽기
curl -X GET http://localhost:3500/v1.0/state/statestore/name

# Redis 컨테이너 접속
docker container exec -it dapr_redis redis-cli

# Redis 키 목록 확인
keys *

# Redis 값 확인
hgetall "myapp||name"

# Redis 컨테이너 나오기
exit
```

## Dapr sidecar 실행
- `dapr run --app-id myapp --dapr-http-port 3500`
  - `--app-id` : 프로그램 고유 ID
  - `--dapr-http-port` : HTTP 포트
  - 그 외 기본 옵션 : `ps -ef | grep daprd`
    ```shell   
    /root/.dapr/bin/daprd 
      --app-id myapp 
      --dapr-http-port 3500 
      --dapr-grpc-port 34531 
      --log-level info 
      --app-max-concurrency -1 
      --app-protocol http 
      --components-path /root/.dapr/components 
      --metrics-port 45175 
      --dapr-http-max-request-size -1 
      --placement-host-address localhost:50005 
      --config /root/.dapr/config.yaml
    ```
- 실행 과정
  ```shell
  root@HHKO-LABTOP:~# dapr run --app-id myapp --dapr-http-port 3500
  WARNING: no application command found.
  ℹ️  Starting Dapr with id myapp. HTTP Port: 3500. gRPC Port: 42367
  ℹ️  Checking if Dapr sidecar is listening on HTTP port 3500
  ...
- 포트 오픈 확인
  ```shell
  # netstat 명령어를 위한 패키지 설치
  apt install -y net-tools

  root@HHKO-LABTOP:~# netstat -nap | grep LISTEN | grep daprd
  tcp6       0      0 :::45175                :::*                    LISTEN      2934/daprd
  tcp6       0      0 :::35043                :::*                    LISTEN      2934/daprd
  tcp6       0      0 :::34531                :::*                    LISTEN      2934/daprd
  tcp6       0      0 :::3500                 :::*                    LISTEN      2934/daprd
  ```
  - 열린 포트
    - `45175` : --metrics-port
    - `35043` : ?
    - `34531` : --dapr-grpc-port
    - `3500` : --dapr-http-port
  - `netstat` 명령어 옵션
    - `-n`, `--numeric` : don't resolve names
    - `-a`, `--all` : display all sockets (default: connected)
    - `-p`, `--programs` : display PID/Program name for sockets

## Dapr sidecar 상태 쓰기
- 데이터
  ```json
  [
    {
      "key": "name",
      "value": "Bruce Wayne"
    }
  ]
  ```
- 상태 쓰기 HTTP API
  ```  
  curl -X POST -H "Content-Type: application/json" -d '[{ "key": "name", "value": "Bruce Wayne"}]' http://localhost:3500/v1.0/state/statestore
  ```
  - HTTP API Mehtod : POST
  - HTTP API URL : http://localhost:3500/v1.0/state/statestore
  - HTTP API Value : '[{ "key": "name", "value": "Bruce Wayne"}]'

## Dapr sidecar 상태 읽기
- 상태 읽기 HTTP API
  ```shell
  curl http://localhost:3500/v1.0/state/statestore/name
  curl -X GET http://localhost:3500/v1.0/state/statestore/name
  ```
- 상태 읽기 결과
  ```shell  
  root@HHKO-LABTOP:~# curl http://localhost:3500/v1.0/state/statestore/name
  "Bruce Wayne"
  ```

## Dapr sidecar 상태 삭제
- 상태 삭제 HTTP API
  ```shell
  curl -X DELETE http://localhost:3500/v1.0/state/statestore/name
  ```  

## Redis 데이터 확인
- Redis 컨테이너 접속
  ```shell
  docker container exec -it dapr_redis redis-cli
  ```
- Redis 키 확인
  ```shell
  keys *
  ```
  - 실행 결과
    ```shell
    127.0.0.1:6379> keys *
    1) "myapp||name"
    ```
    - `myapp` : --app-id 값  
    - `name` : Json Key 값
- Redis 값 확인
  ```shell
  hgetall "myapp||name"
  ```
  - 실행 결과
    ```shell
    127.0.0.1:6379> hgetall "myapp||name"
    1) "data"
    2) "\"Bruce Wayne\""
    3) "version"
    4) "1"
    
    127.0.0.1:6379> exit
    root@HHKO-LABTOP:~#
    ```

## TODO
- Background 실행
- 컨테이너와 daprd 관계 이해?