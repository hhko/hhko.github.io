# 배움은 설렘이다.
- Hugo docsy 테마를 이용한 GitHub Pages 만들기

## 목차
- 개발 환경
- GitHub 저장소
- Hugo 사이트
- Hugo Docsy 테마
- GitHub Actions
- GitHub Pages

<br/>

## 1. 개발 환경
1. 관리자 권한 PowerShell 열기
1. choco 설치 : [INSTALLING CHOCOLATEY](https://chocolatey.org/install)
   ```powershell
   Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
   ```
1. hugo-extended 0.89.4 설치
   ```shell
   choco install -y --version=0.89.4 hugo-extended
   ```

## 2. GitHub 저장소
1. GitHub 저장소 기본 설정
   - [id].github.io
   - public
   - README.md
   - license
1. git clone 받기
   ```shell
   git clone ...URL...
   ```

## 3. Hugo 사이트
1. 브랜치 생성
   ```shell
   cd [id].github.io
   git checkout -b setup-hugo-site
   git branch
     main
   * setup-hugo-site
   ```
1. Hugo 사이트 생성
   ```shell
   hugo new site --force .
   ```
1. Hugo 사이트 빌드
   ```shell
   hugo server
   http://localhost:1313/
   ```
   - hugo server -p 1314 : 기본 포트 변경
1. .gitignore 파일 수정 : [kubernetes .gitignore 파일](https://github.com/kubernetes/website/blob/main/.gitignore#L29)
   ```
   public/
   resources/
   node_modules/
   package-lock.json
   .hugo_build.lock
   ```
   - `public` : hugo 빌드 결과물
   - `resources` : hugo 빌드 중간 결과물
1. 저장소 추가
   ```shell
   git add .
   git commit -m "hugo 사이트을 생성한다"
   git push origin setup-hugo-site
   
   #
   # GitHub 사이트에서 PR 완료시킴
   #
   
   git checkout main
   git pull
   ```

## 4. Hugo Docsy 테마 추가
1. 브랜치 만들기
   ```shell
   git checkout -b setup-docsy-theme
   git branch
   ```
1. Hugo Docsy 테마 받기
   ```shell
   git submodule add https://github.com/google/docsy.git themes/docsy
   git submodule update --init --recursive
   ```
1. Docsy 테마 지정 : config.toml 파일 수정
   ```toml
   theme = ["docsy"]
   ```
1. Hugo 사이트 빌드
   ```shell
   hugo server
   http://localhost:1313/
   ```
1. 저장소 추가
   ```shell
   git add .
   git commit -m "docsy 테마를 추가한다"
   git push origin setup-docsy-theme
   
   #
   # GitHub 사이트에서 PR 완료시킴
   #

   git checkout main
   git pull
   ```

## 5. GitHub Actions
1. 브랜치 만들기
   ```shell
   git checkout -b setup-hugo-workflow
   git branch
   ```
1. GitHub Action 폴더 생성
   ```shell
   mkdir -p .github/workflows
   cd .\.github\workflows\
   ```
1. `github-pages.yml` 파일 생성 : [Workflow for autoprefixer and postcss-cli](https://github.com/peaceiris/actions-hugo#%EF%B8%8F-workflow-for-autoprefixer-and-postcss-cli)
   ```yml
   name: GitHub Pages
   
   on:
     push:
       branches:
         - main  # Set a branch to deploy
     pull_request:
   
   jobs:
     deploy:
       runs-on: ubuntu-20.04
       concurrency:
         group: ${{ github.workflow }}-${{ github.ref }}
       steps:
         - name: Checkout Repository
           uses: actions/checkout@v2
           with:
             submodules: recursive  # Fetch the Docsy theme
             fetch-depth: 0         # Fetch all history for .GitInfo and .Lastmod
   
         - name: Setup Hugo
           uses: peaceiris/actions-hugo@v2
           with:
             hugo-version: '0.89.4'
             extended: true
   
         - name: Setup Packages
           run: npm install autoprefixer postcss-cli
         
         - name: Build
           run: hugo --minify
   
         - name: Deploy
           uses: peaceiris/actions-gh-pages@v3
           if: ${{ github.ref == 'refs/heads/main' }}
           with:
             github_token: ${{ secrets.GITHUB_TOKEN }}
   ```
1. 저장소 추가
   ```shell
   git add .
   git commit -m "Hugo workflow을 추가한다"
   git push origin setup-hugo-workflow
   
   #
   # GitHub 사이트에서 PR 완료시킴
   #
   
   git checkout main
   git pull
   ```

## 6. GitHub Pages
1. 브랜치 확인
   - GitHub branch 목록 : gh-pages
1. 설정
   - GitHub Settings > Pages
     - **`gh-pages`** : / (root) 
1. 사이트 확인
   - https://{id}.github.io