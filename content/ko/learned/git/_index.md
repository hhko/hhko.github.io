---
title: Git
description: .
#type: docs
#no_list: true
#main_menu: true
#content_type: concept
---

### Powershell
- 공백 경로
  - 변경 전 : `"C:\Program Files\Microsoft Visual Studio\Bin\hello.exe"`
  - 변경 후 : C:\\`"Program Files"`\\`"Microsoft Visual Studio"`\\Bin\\hello.exe
- 한글 깨짐
  - [OS : intl.cpl -> "시스템 로캘 변경" 탭 -> 한국어(대한민국), [x] Beta 세계 언어 지원을 위해 Unicode UTF-8 사용](https://torbjorn.tistory.com/550)

### Git 설치
```shell
choco search git
choco install git -y –version 2.34.1

choco upgrade chocolatey

choco search git
choco install git -y –version 2.34.1

# 원격 검색
# 모든 버전 원격 검색
# 로컬 검색
# 정확히 검색

# 설치
# 특정 버전 설치
# 삭제

# 현재 설치된 패키지 목록 조회
PS > choco list -lo

# 최신 버전이 아닌 패키지 목록 조회
PS > choco outdated

# 모든 패키지를 최신 버전으로 업그레이드
PS > choco upgrade all

# 특정 패지지 설치 후 PATH 환경 변수 변경점 발생시 PowerShell 재시작 없이 즉시 반영
PS > refreshenv
```
- [choco, Windows 패키지 관리자 설치하기](https://jsonobject.tistory.com/526)

### Git
```shell
#
# 버전
#
git --version
```

### Git Config
```
#
# 환경설정 옵션
#
git config -h
    -l, --list            list all
    -e, --edit            open an editor

#
# 환경설정 확인
#
git config -l

#
# 환경설정 범주
# - C:\Users\{계정명}\.gitconfig
# - C:\Program Files\Git\etc\gitconfig
# - .\.git\config
git config --global -l
git config --system -l
git config --local -l
git config --worktree -l

#
# 환경설정 편집
#
git config --global -e
git config --system -e
git config --local -e
git config --worktree -e

#
# 필수 환경설정
#
# config 편집 도구
git config --global core.editor "code --wait"
     system > core.editor = \"C:\\\\Program Files\\\\Notepad++\\\\notepad++.exe\" -multiInst -notabbar -nosession -noPlugin

# diff 편집 도구
git config --global diff.tool "vscode"
git config --global difftool.vscode.cmd "code --wait --diff $LOCAL $REMOTE"

# merge 편집 도구
git config --global merge.tool "vscode"
git config --global mergetool.vscode.cmd "code --wait $MERGED"

# 계정
git config --global user.name "xxx"
git config --global user.email "xxx@yyy.com"
     local > git config user.name "xxx"
     local > git config user.email "xxx@yyy.com"

# 기본 브랜치명
git config --global init.defaultBranch main

# \r 처리
git config --global core.autocrlf true
     true : \r\n  -> \n
     input : \n   -> \n

# 한글 깨짐
git config --global core.quotepath false
  # LC_ALL -> ko_KR.UTF-8
```

| 운영체제 | Gtt 명령어 | 설정 적용 범위 | 설정 파일 경로 |
|---|---|---|---| 
| Linux	| git config --system |	시스템의 모든 사용자와 모든 저장소	| /etc/gitconfig | 
| Linux	| git config --global | 시스템의 특정 사용자의 모든 저장소	| ~/.gitconfig,~/.config/git/config | 
| Linux	| git config --local  | 현재 작업중인 특정 저장소	       | ./ | 

```shell
#
# 별칭
# - git config --global alias.{별칭} {git 명령}
git config --global alias.st status
git st
```

```shell
git init
   .git
git add .  
   <- git restore --staged .
git status
   git status -s
git diff 
   git diff --staged
   git diff --cached
git difftool -y 
   git difftool --staged -y
git log
   # 'sysdm.cpl ,3' 환경 변수 : System Device Manager
   
```
- 버전 : commit 단위
  - 물리적 구분 키 : SHA-1 Hash 
  - 논리적 구분 키 : 메시지
- 파일 상태
  - Working Directory
    - Untracked
    - Tracked
      - Unmodified
      - Modified
  - Staging Area : **`staged` ＝ `cached`**
  - Local Repo.
