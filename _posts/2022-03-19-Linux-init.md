---
title:  "Linux 기본 설정 및 팁.md"
categories:
    - 팁_Tip
tags:
    - 따라하기
    - bash
    - zsh
    - shell
    - Oh My Zsh
toc: true
published: true
---
필자의 Linux 기본 설정 및 팁!

안녕하세요. 매번 여러 운영체제를 재설치 한 뒤 초기 설정은 가장 귀찮은 작업 중 하나 입니다. 이를 위해 필자의 기본 설정을 공유를 겸한 스스로를 위한 메모 차 남겨 둡니다.

우선 리눅스의 경우 `AMD64` 아키텍처 기반의 `Debian 11`과 `Ubuntu 21.04`를 기준으로 작성 되었습니다. 일부 항목의 경우 `ARM64` 환경도 고려 하였습니다.

일부 특별 환경의 경우 `AMD64` 아키텍처 기반의 `Ubuntu 16.04` 및 `Ubuntu 18.04`를 기준으로 작성 되었습니다.

# 1. 기초 패키지 설치
기초적인 설정을 위해 정말 필요한 패키지만 기본 패키지 서버에서 받아 준다. 이후 패키지들은 패키지 서버를 바꾼 뒤 설치할 예정이다.

```
sudo -s apt install vim curl git -y
```

# 2. 패키지 서버 바꾸기
```
sudo -s vim /etc/apt/sources.list


# ftp.harukasan.org 으로 변경
:%s/deb.debian.org/ftp.harukasan.org/

# mirror.kakao.com 으로 변경
:%s/deb.debian.org/mirror.kakao.com/
```

# 3. 필수 패키지 설치
## 패키지 목록 갱신 및 업그레이드
```
sudo -s apt update
sudo -s apt upgrade
```

## 공통 패키지
```
# 20.04 / 18.04
sudo -s apt install neovim zsh gcc python python-pip python-dev xrdp net-tools ufw iptables -y

# 16.04
sudo -s apt install zsh gcc python python-pip python-dev net-tools -y

# Oh-My-Zsh 설치
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## 데스크톱 환경 설치
주로 Server 이미지를 사용하므로 xrdp 사용을 위해 `MATE` 데스크톱 환경을 설치.

```
sudo -s apt install tasksel -y
sudo -s tasksel install ubuntu-mate-core
```

## 보안 공부 환경
```
sudo -s apt install libc6-dbg libssl-dev libffi-dev build-essential gcc-multilib -y
python2 -m pip install --upgrade pip==20.3.4
python2 -m pip install --upgrade pwntools
```

## 필요 파일 수동 다운로드 및 설치
### 권장 버전
[MesloLGS NF 폰트 다운로드 페이지](https://github.com/romkatv/powerlevel10k/blob/master/font.md#manual-font-installation)

```
# lsd for amd64
wget https://github.com/Peltoche/lsd/releases/download/0.21.0/lsd_0.21.0_amd64.deb

# lsd for arm64
wget https://github.com/Peltoche/lsd/releases/download/0.21.0/lsd_0.21.0_arm64.deb

# bat for 20.04
sudo -s apt install bat -y
mkdir -p ~/.local/bin
ln -s /usr/bin/batcat ~/.local/bin/bat

# bat for 16.04 / 18.04 amd64
wget https://github.com/sharkdp/bat/releases/download/v0.12.1/bat_0.12.1_amd64.deb

sudo -s dpkg -i *.deb
````

# 4. 권장 레포지토리 복제
## 공통
powerlevel10k, AutoSuggestions 및 Highlighting 을 설치
```
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

## 보안 공부
```
git clone https://github.com/longld/peda.git ~/peda
git clone https://github.com/slimm609/checksec.sh ~/checksec
git clone https://github.com/scwuaptx/Pwngdb.git ~/Pwngdb
git clone https://github.com/Mipu94/peda-heap.git ~/peda-heap
```

# 5. 설치한 패키지 및 기타 설정
## Shell rc
```
zsh-autosuggestions
zsh-syntax-highlighting

alias workdir="cd /home/icaros7/workspace"
alias vim="nvim"
alias vi="nvim"
alias lll="lsd -al --no-symlink --group-dirs=first"
alias ll="lsd -l --no-symlink --group-dirs=first"
alias ls="lsd -al --group-dirs=first"
alias lt="lsd --tree --no-symlink"
alias cat="bat"

source ~/.*rc
```

## OpenSSH Server
```
sudo -s vim /etc/ssh/sshd_config

sudo -s systemctl restart ssh.service
```

## Xrdp server
```
sudo -s vim /etc/xrdp/xrdp.ini

sudo -s systemctl restart xrdp
```

## iptables
포트포워딩을 위한 iptables 설정
```
# 포트포워딩 추가
sudo -s iptables -t nat -A PREROUTING -i {인터페이스 이름} -p tcp --dport {외부 포트} -j REDIRECT --to-port {내부 포트}

# 포트포워딩 리스트
sudo -s iptables -t nat -L --line-numbers

# 포트포워딩 삭제 with Number
sudo -s iptables -t nat -D -PREROUTING {Number}
```

## UFW
```
sudo -s ufw enable

# (내부) 포트 허용
sudo -s ufw allow {내부 포트}
```

## 보안 공부
```
sudo -s cp ~/checksec/checksec /usr/local/bin
cp ~/Pwngdb/.gdbinit ~/
echo "source ~/peda/peda.py" >> ~/.gdbinit
```

# 6. ssh 인증서 생성
아래 명령어를 통해 rsa4096으로 생성 가능. 중간 이메일이 적힌 곳에 본인 이메일을 적으면 된다.

```
ssh-keygen -t rsa -b 4096 -C "hominlab@gmail.com"
```

# 7. git 기본 설정
git config 를 위한 자주 사용하는 설정 들. 중간에 사용자 이름과 이메일에 본인 값을 넣어주면 된다.

```
git config --global user.name "iCAROS7"
git config --global user.email "hominlab@gmail.com"
git config --global init.defaultBranch main
git config --global pull.rebase false
```

