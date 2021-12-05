## choco 패키지 설치 관리자
### choco 설치
- https://chocolatey.org/install#individual
  - 관리자 권한 Powershell 실행
- Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

### choco 주요 명령어
#### search
- choco search -l : 설치된 패키지 검색
  - `-l`, `--lo`, `--local`, `--localonly`, `--local-only` : LocalOnly - Only search against local machine items.
- choco search -e 패키지명 : 일치하는 패키지 검색
  - `-e`, `--exact` : Exact - Only return packages with this exact name. Available in 0.9.10+.
- choco search -a 패키지명 : 패키지명과 일치하는 모든 버전 표시
  - `-a`, `--all`, `--allversions`, `--all-versions` : AllVersions - include results from all versions.

#### install
- choco install -y 패키지명
  - `-y`, `--yes`, `--confirm` : Confirm all prompts - Chooses affirmative answer instead of prompting.
- choco install -y --version=버전 패키지명
  - choco install -y --version 0.84.1 hugo-extended
  - `--version=VALUE` : A specific version to install. Defaults to unspecified.

#### upgrade
- choco upgrade hugo-extended
- choco upgrade all

#### uninstall
- choco uninstall?

## hugo 로컬 설치
- choco install -y --version=0.89.4 hugo-extended
- choco install -y hugo-extended 
	- choco install -y hugo-extended --version=0.88.1
	- C:\ProgramData\chocolatey\lib
- choco uninstall hugo-extended
- choco upgrade hugo-extended
	- choco upgrade all