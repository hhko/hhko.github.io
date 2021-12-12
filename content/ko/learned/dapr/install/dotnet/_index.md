---
title: .NET 6.0 설치
#linkTitle: OpenSearch 링크 제목
description: Ubuntu 20.04 LTS 환경에서 .NET 6.0 설치
type: docs
weight: 3
#no_list: true
#main_menu: true
#content_type: concept
---

## .NET 6.0 패키지 수동 설치
```shell
# GPG 패키지 설치
sudo apt install -y gpg

# Microsoft 패키지 GPG 서명키 추가
wget -O - https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor -o microsoft.asc.gpg
sudo mv microsoft.asc.gpg /etc/apt/trusted.gpg.d/
sudo chown root:root /etc/apt/trusted.gpg.d/microsoft.asc.gpg

# Microsoft 패키지 저장소 목록 추가
# deb [arch=amd64,armhf,arm64] https://packages.microsoft.com/ubuntu/20.04/prod focal main
wget https://packages.microsoft.com/config/ubuntu/20.04/prod.list
sudo mv prod.list /etc/apt/sources.list.d/microsoft-prod.list
sudo chown root:root /etc/apt/sources.list.d/microsoft-prod.list

# .NET 6.0 패키지 설치
sudo apt update; \
  sudo apt install -y apt-transport-https && \
  sudo apt update && \
  sudo apt install -y dotnet-sdk-6.0
```
- `dotnet-sdk-6.0` : `aspnetcore-runtime-6.0`와 `dotnet-runtime-6.0`을 포함한다.
- `aspnetcore-runtime-6.0`
- `dotnet-runtime-6.0`
- {product}-{type}-{version}
  - {product}
    - dotnet
    - aspnetcore
  - {type}
    - sdk
    - runtime
  - {version}
    - 6.0
    - 5.0
    - 3.1
    - 3.0
    - 2.1

## .NET 6.0 패키지 설치 확인
```shell
# .NET 6.0 패키지 설치 확인
root@HHKO-LABTOP:~# apt list --installed | grep dotnet

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

dotnet-apphost-pack-6.0/focal,now 6.0.0-1 amd64 [installed,automatic]
dotnet-host/focal,now 6.0.0-1 amd64 [installed,automatic]
dotnet-hostfxr-6.0/focal,now 6.0.0-1 amd64 [installed,automatic]
dotnet-runtime-6.0/focal,now 6.0.0-1 amd64 [installed,automatic]
dotnet-runtime-deps-6.0/focal,now 6.0.0-1 amd64 [installed,automatic]
dotnet-sdk-6.0/focal,now 6.0.100-1 amd64 [installed]
dotnet-targeting-pack-6.0/focal,now 6.0.0-1 amd64 [installed,automatic]
```
- dotnet-sdk-6.0
  - dotnet-sdk-6.0/focal
  - dotnet-apphost-pack-6.0/focal
  - dotnet-targeting-pack-6.0/focal
  - dotnet-runtime
    - dotnet-runtime-6.0/focal
    - dotnet-runtime-deps-6.0/focal
    - dotnet-host/focal
    - dotnet-hostfxr-6.0/focal
- aspnetcore-runtime-6.0
  - aspnetcore-runtime-6.0/focal,
  - dotnet-runtime
    - dotnet-runtime-6.0/focal
    - dotnet-runtime-deps-6.0/focal
    - dotnet-host/focal
    - dotnet-hostfxr-6.0/focal
- dotnet-runtime-6.0
  - dotnet-runtime-6.0/focal
  - dotnet-runtime-deps-6.0/focal
  - dotnet-host/focal
  - dotnet-hostfxr-6.0/focal


## .NET 6.0 패키지 제거
```shell
apt purge -y dotnet-* aspnetcore-*
```

## .NET 패키지 업그레이드
```shell
sudo apt update
sudo apt list --upgradeable
sudo apt upgrade
```

## 참고 사이트
- [Debian에 .NET SDK 또는 .NET 런타임 설치](https://docs.microsoft.com/ko-kr/dotnet/core/install/linux-debian#unable-to-locate--some-packages-could-not-be-installed)