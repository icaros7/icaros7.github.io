---
title:  "Windows 기본 설정 및 팁.md"
categories:
    - TIPs-Init
tags:
    - 따라하기
    - bash
    - zsh
    - shell
    - Oh My Zsh
toc: true
published: true
---
필자의 Windows 기본 설정 및 팁!

안녕하세요. 매번 여러 운영체제를 재설치 한 뒤 초기 설정은 가장 귀찮은 작업 중 하나 입니다. 이를 위해 필자의 기본 설정을 공유를 겸한 스스로를 위한 메모 차 남겨 둡니다.


# 1. Chocolatey 설치하기
리눅스계에 apt, rpm과 맥 계에 brew가 있다면 윈도우 계열에서는 [Chocolatey](https://chocolatey.org/)가 있다. 커뮤니티 상의 사용자가 패키징 혹은 작성한 스크립트를 기반으로 한번에 여러 패키지를 설치 및 관리를 해준다.

`PowerShell`을 관리자 권한으로 실행한 후 아래 명령어 입력
```
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

# 2. Chcolatey 패키지 설치
시간이 상당 소요되므로 먼저 설치를 진행한다.
아래는 필자가 자주 사용하는 패키지 이며, [이 스크립트](https://raw.github.com/icaros7/icaros7.github.io/main/assets/2022-02-05-Windows-init/packages.config)를 다운로드 받아 사용 할 경우 한줄로 해결이 된다.

혹시 `JetBrains Rider` 설치가 다 되었음에도 무한 루프에 빠진다면 작업 관리자에서 `JetBrains Rider AfterInstall` 이런 비슷한 프로세스명을 가진 프로세스를 종료 하면 된다.

```
# config 스크립트를 받은 경우
choco install packages.config -y

# 수동
choco install discord --version 1.0.9003 -y
choco install vscode --version 1.64.0 -y
choco install intellijidea-ultimate --version 2021.3.2 -y
choco install jetbrains-rider --version 2021.3.3 -y
choco install bandizip --version 7.22 -y
choco install oraclejdk --version 17.0.2 -y
choco install jdk8 --version 8.0.211 -y
choco install winmerge --version 2.16.18 -y
choco install neovim --version 0.6.1 -y
choco install authy-desktop --version 1.9.0 -y
choco install adguardhome --version 0.107.3 -y
choco install crystaldiskinfo --version 8.14.2 -y
choco install zoom --version 5.9.3.3169 -y
choco install notepadplusplus.install --version 8.3 -y
choco install 7zip.install --version 21.7 -y
choco install python3 --version 3.10.2 -y
choco install vlc --version 3.0.16 -y
choco install curl --version 7.81.0 -y
choco install msys2 --version 20220128.0.0 -y
```

# 3. 수동 설치 패키지
- `Windows 업데이트` 및 `Microsoft Store` 에서 업데이트 진행
- [AMD Radeon RX480 드라이버](https://www.amd.com/ko/support/graphics/radeon-400-series/radeon-rx-400-series/radeon-rx-480)
- [AMD Radeon Ramdisk](http://radeonramdisk.com/software_downloads.php)
- [Between](https://assets-pc.between.us/downloads/setup.exe)
- [카카오톡](https://app-pc.kakaocdn.net/talk/win32/KakaoTalk_Setup.exe)


# 4. WSL 설치
아래 명령어를 통해 WSL 의존성 Windows 기능 및 Debian 이미지를 설치 할 수 있다.

```
# PowerShell
wsl --install -d Debian
```
# 5. 사용 레지스트리 등록
아래는 자주 사용하는 레지스트리 이다. 필자는 원드라이브에 넣어두고 사용하지만 혹시 몰라 [여기서](https://raw.github.com/icaros7/icaros7.github.io/main/assets/2022-02-05-Windows-init/Cpas+UniversialTime.reg) 받을 수 있다.

## 키보드 리맵핑
CapsLock 키를 한영키로, 백슬레시(`\`)키를 백스페이스 키로

```
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Keyboard Layout]
"Scancode Map"=hex:\
00,00,00,00,\
00,00,00,00,\
05,00,00,00,\
38,e0,3a,00,\
3a,00,38,e0,\
0e,00,2b,00,\
2b,00,0e,00,\
00,00,00,00
```

## RealTimeIsUniversal 활성
해킨토시와 윈도우를 같이 사용하다보면 윈도우 부팅 시 시계가 맞지 않는다. 이는 UTC 기준으로 시간을 표기 시키면 해결 된다. (물론 표시되는건 UTC+9)

```
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\TimeZoneInformation]
"RealTimeIsUniversal"=dword:00000001
```

---
# 6. 재부팅 및 WSL용 64비트 커널 업데이트 설치
5번까지 다 완료가 되었다면 1회 재부팅 이후 [여기](https://docs.microsoft.com/en-us/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package)를 참조하여 WSL용 64비트 커널 업데이트를 설치

- [WSL2 Linux kernel update package for x64 machines](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)

# 7. WSL 기본 패키지 설치
WSL 기본 설정 완료 이후 작업 수행

## 패키지 서버 국내 서버로 변경
```
sudo -s vi /etc/apt/sources.list

# mirror.harukasan.org 으로 변경
:%s/deb.debian.org/mirror.harukasan.org/

# mirror.kakao.com 으로 변경
:%s/deb.debian.org/mirror.kakao.com/
```

## 패키지 설치 및 업데이트
```
sudo -s apt update
sudo -s apt install git curl gcc vim zsh -y
sudo -s apt upgrade -y
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

---
# 8. powerlevel10k 및 최애 플러그인 설치
## 폰트 설치
`powerlevel10k`의 경우 강력히 권장되는 폰트인 `MesloLGS NF` 폰트 설치 요구

[공식 git 내 Readme.md 중 폰트 관련 페이지](https://github.com/romkatv/powerlevel10k#meslo-nerd-font-patched-for-powerlevel10k)에서 폰트 파일 다운로드 하여 설치. 단, 설치 이후 사용 터미널 혹은 IDE등에서 해당 폰트로 변경 해야함.

## 테마 및 플러그인 설치
아래 명령어를 통해 `powerlevel10k`, `zsh-autosuggestions`, `zsh-syntax-highlighting` 를 설치 해 줍니다.

```
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

## zshrc 수정
이후 `~/.zshrc` 내 테마 설정 부분과 플러그인 목록에 아래를 참조하여 수정 하여 줍니다.

vim 팁:
  - `Ctrl + D`: 페이지 다운
  - `Ctrl + B`: 페이지 업
  - `GG`: 문서 처음으로
  - `Shift + G`: 문서 끝으로

```
powerlevel10k/powerlevel10k
zsh-autosuggestions
zsh-syntax-highlighting

# alias로 workdir 키워드 단축어 등록
alias workdir="cd /mnt/e/workspace"
```

이후 `source ~/.zshrc`를 통해 수정한 zshrc를 불러옵니다.

---
# 9. git 기본 설정
git config 를 위한 자주 사용하는 설정 들. 중간에 사용자 이름과 이메일에 본인 값을 넣어주면 된다.

```
git config --global user.name "iCAROS7"
git config --global user.email "hominlab@gmail.com"
git config --global init.defaultBranch main
git config --global pull.rebase false
```

# 10. ssh 인증서 생성
아래 명령어를 통해 rsa4096으로 생성 가능. 중간 이메일이 적힌 곳에 본인 이메일을 적으면 된다.

```
ssh-keygen -t rsa -b 4096 -C "hominlab@gmail.com"

# cat pub key
cat ~/.ssh/id_rsa.pub
```

# 11. macOS 커서 (테마)
개인적으로 좋아하는 커서 테마 이다. 바로 macOS 와 동일한 커서.

- [antiden/macOS-cursors-for-Windows](https://github.com/antiden/macOS-cursors-for-Windows)
- 동봉된 `install.inf` 파일을 사용하여 한번에 설치 가능

---
수정: 2022.02.05 14:30