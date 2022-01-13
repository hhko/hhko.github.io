---
title: dotnet
description: dotnet 명령어
#type: docs
---

## 템플릿
### 템플릿 목록 확인하기
```shell
dotnet new --list
```

<br/>

## 솔루션
### 솔루션 생성하기
```shell
dotnet new sln -n [솔루션 파일명] -o [솔루션 폴더명] --framework [SDK이름] -lang [언어} --langVersion [버전]
```
- `--framework` : [링크](https://docs.microsoft.com/ko-kr/dotnet/standard/frameworks)
  - `net6.0`
  - `netcoreapp3.1`
- `--langVersion` : [링크](https://docs.microsoft.com/ko-kr/dotnet/csharp/language-reference/configure-language-version#c-language-version-reference)
  - `latest` : 컴파일러가 최신 릴리스 버전(부 버전 포함)
  - `10.0`
  - `9.0`
  - `8.0`
  - `7.3`

### 솔루션 모든 프로젝트 확인하기
```shell
dotnet sln list
```
