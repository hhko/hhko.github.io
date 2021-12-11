---
title: Secrets Component
#linkTitle: OpenSearch 링크 제목
description: 사용자 정의 Secrets Component 생성 예제
type: docs
weight: 2
#no_list: true
#main_menu: true
#content_type: concept
---

## 명령어 요약
```shell
# Secrets 파일 생성 : mysecrets.json
{
   "my-secret" : "I'm Batman"
}

# Component 폴더 생성 : my-components 
mkdir my-components

# Component 설정 파일 : localSecretStore.yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: my-secret-store
  namespace: default
spec:
  type: secretstores.local.file    # secrets 로컬 파일 설정
  version: v1
  metadata:
  - name: secretsFile
    value: ./mysecrets.json        # secrets 파일 지정 
  - name: nestedSeparator
    value: ":"

# Dapr 실행
dapr run --app-id myapp --dapr-http-port 3500 --components-path ./my-components

# Secrets 값 읽기
curl http://localhost:3500/v1.0/secrets/my-secret-store/my-secret
```

## Secret 파일 생성
- `mysecrets.json` 파일
  ```json
  {
     "my-secret" : "I'm Batman"
  }
  ```

# Component 생성
- Component 폴더 
  - `mkdir my-components`
- Component 설정 파일
  ```yaml
  apiVersion: dapr.io/v1alpha1
  kind: Component
  metadata:
    name: my-secret-store
    namespace: default
  spec:
    type: secretstores.local.file
    version: v1
    metadata:
    - name: secretsFile
      value: ./mysecrets.json
    - name: nestedSeparator
      value: ":"
  ```
  - `secretstores.local.file`
    - `secrets` 지정
    - `./mysecrets.json` : `.` 경로는 dapr run 위치이다.

## Dapr sidecar 실행
```shell
dapr run --app-id myapp --dapr-http-port 3500 --components-path ./my-components
```
- `--components-path` : ./my-components
- `.` : `dapr run`을 실행한 현재 경로이다.

## Secret 값 읽기
```shell
curl http://localhost:3500/v1.0/secrets/my-secret-store/my-secret
```
- my-secret-store
- my-secret : json 파일 데이터 키