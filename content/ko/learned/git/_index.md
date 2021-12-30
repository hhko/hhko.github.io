---
title: Git
description: .
#type: docs
#no_list: true
#main_menu: true
#content_type: concept
---

```shell
#
# 버전
#
git --version

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
# 편집 도구
git config --global core.editor "code --wait"
     system > core.editor = \"C:\\\\Program Files\\\\Notepad++\\\\notepad++.exe\" -multiInst -notabbar -nosession -noPlugin

# diff 편집 도구
git config --global diff.tool "vscode"
git config --global difftool.vscode.cmd "code --wait --diff $LOCAL $REMOTE"

# 계정
git config --global user.name "xxx"
git config --global user.email "xxx@yyy.com"
     local > git config user.name "xxx"
     local > git config user.email "xxx@yyy.com"

# \r 처리
git config --global core.autocrlf true
     true : \r\n  -> \n
     input : \n   -> \n

# 한글 깨짐
git config --global core.quotepath false

# 브랜치명?

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
   <-> git restore --staged .
git status
   git status -s
git diff 
   git diff --staged
   git diff --cached
git difftool -y 
   git difftool --staged -y
git log
   # 'sysdm.cpl ,3' 환경 변수 : System Device Manager
   LC_ALL -> ko_KR.UTF-8
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
