---
title: State C#
#linkTitle: OpenSearch 링크 제목
description: Redis 상태 읽기 및 쓰기 예제
type: docs
weight: 2
#no_list: true
#main_menu: true
#content_type: concept
---

- 소스 : [링크](./DaprCounter)

## 명령어 요약
```shell
# 콘솔 프로젝트 생성
dotnet new console -o DaprCounter
cd DaprCounter
dotnet run

# 패키지 추가
dotnet add package Dapr.Client

# State 상태 코드

# Dapr 실행
dapr run --app-id DaprCounter dotnet run

# Reids 값 확인
docker container exec -it dapr_redis redis-cli
keys *
hgetall "DaprCounter||counter"
```

## .NET 6.0 콘솔 프로젝트 생성
- 콘솔 프로젝트 생성
  ```shell
  dotnet new console -o DaprCounter
  cd DaprCounter
  dotnet run
  ```
- Dapr 패키지 추가
  ```shell
  dotnet add package Dapr.Client
  ```
- State 상태 코드 구현
  ```cs
  using Dapr.Client;
  
  const string storeName = "statestore";
  const string key = "counter";
  
  var daprClient = new DaprClientBuilder().Build();
  var counter = await daprClient.GetStateAsync<int>(storeName, key);
  
  while (true)
  {
      Console.WriteLine($"Counter = {counter++}");
  
      await daprClient.SaveStateAsync(storeName, key, counter);
      await Task.Delay(1000);
  }
  ```
  - `statestore` : `$HOME/.dapr/components/statestore.yaml`에 정의된 상태 이름
  - `counter` : Redis에서 사용될 키 이름 

## .NET 6.0 콘솔 프로젝트 실행
- Dapr 실행
  ```shell
  dapr run --app-id DaprCounter dotnet run
  ℹ️  Starting Dapr with id DaprCounter. HTTP Port: 45877. gRPC Port: 34973
  ℹ️  Checking if Dapr sidecar is listening on HTTP port 45877
  ...
  
  == APP == Counter = 0
  == APP == Counter = 1
  == APP == Counter = 2
  == APP == Counter = 3
  == APP == Counter = 4
  == APP == Counter = 5
  == APP == Counter = 6
  
  terminated signal received: shutting down
  ✅  Exited Dapr successfully
  ✅  Exited App successfully

  # 재실행 : 이전 값 이후로 시작된다
  dapr run --app-id DaprCounter dotnet run
  ℹ️  Starting Dapr with id DaprCounter. HTTP Port: 45877. gRPC Port: 34973
  ℹ️  Checking if Dapr sidecar is listening on HTTP port 45877
  ...
  
  == APP == Counter = 7
  == APP == Counter = 8
  ...
  ```

## Redis 키/값 확인
- Redis 접속
  ```shell
  docker container exec -it dapr_redis redis-cli
  ```
- 키 확인
  ```shell
  127.0.0.1:6379> keys *
  1) "DaprCounter||counter"
  ```
  - `DaprCounter` : --app-id 식별 값
  - `counter` : `daprClient.GetStateAsync<int>`와 `daprClient.SaveStateAsync`
- 값 확인
  ```shell  
  127.0.0.1:6379> hgetall "DaprCounter||counter"
  1) "data"
  2) "92"
  3) "version"
  4) "92"
  ```

## Redis 설정
- 기본 설정 파일 : `$HOME/.dapr/components/statestore.yaml`
- 기본 설정
  ```yaml
  apiVersion: dapr.io/v1alpha1
  kind: Component
  metadata:
    name: statestore
  spec:
    type: state.redis
    version: v1
    metadata:
    - name: redisHost
      value: localhost:6379
    - name: redisPassword
      value: ""
    - name: actorStateStore
      value: "true"
  ```
- 추가 설정
  ```yaml
  apiVersion: dapr.io/v1alpha1
  kind: Component
  metadata:
    name: statestore
    namespace: production
  spec:
    type: state.redis
    version: v1
    metadata:
    - name: redisHost
      value: localhost:6379
    - name: redisPassword
      value: ""
    - name: actorStateStore
      value: "true"
    scopes:
    - DaprCounter
    ```
    - `namespace: production`
    - `scopes: DaprCounter`

## TODO
- [ ] `namespace: production` 의미는?
- [ ] `scopes: DaprCounter` 의미는?

## 참고 사이트
- [Get started with Dapr](https://docs.microsoft.com/en-us/dotnet/architecture/dapr-for-net-developers/getting-started)
