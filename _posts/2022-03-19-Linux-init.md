---
title:  "Linux 기본 설정 및 팁.md"
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
필자의 Linux 기본 설정 및 팁!

안녕하세요. 매번 여러 운영체제를 재설치 한 뒤 초기 설정은 가장 귀찮은 작업 중 하나 입니다. 이를 위해 필자의 기본 설정을 공유를 겸한 스스로를 위한 메모 차 남겨 둡니다.

우선 리눅스의 경우 `AMD64` 아키텍처 기반의 `Asahi Linux 16`, `Debian 11` 그리고 `Ubuntu 22.04`를 기준으로 작성 되었습니다. 일부 항목의 경우 `ARM64` 환경도 고려 하였습니다.

일부 특별 환경의 경우 `AMD64` 아키텍처 기반의 `Ubuntu 16.04` 및 `Ubuntu 18.04`를 기준으로 작성 되었습니다.

# 1. 기초 패키지 설치
## 패키지 목록 갱신
```
sudo -s apt update
sudo -s pacman -Su
```

## 기본 설치
기초적인 설정을 위해 정말 필요한 패키지만 기본 패키지 서버에서 받아 준다. 이후 패키지들은 패키지 서버를 바꾼 뒤 설치할 예정이다.

```
sudo -s apt install vim curl git dialog -y
sudo -s pacman -Sy vim curl wget git

```

# 2. 패키지 서버 바꾸기
```
sudo -s vim /etc/apt/sources.list


# ftp.harukasan.org 으로 변경
:%s/deb.debian.org/ftp.harukasan.org/g

# mirror.kakao.com 으로 변경
:%s/deb.debian.org/mirror.kakao.com/g

# mirror.misakamikoto.network 으로 변경 (aarch64)
:%s/archive.ubuntu.com/mirror.misakamikoto.network/g

```

# 3. 필수 패키지 설치
## 공통 패키지
```
# Add neovim stable PPA
sudo -s add-apt-repository ppa:neovim-ppa/stable -y

# Add Nodejs PPA
sudo -s apt install ca-certificates curl gnupg -y && sudo -s mkdir -p /etc/apt/keyrings && curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo -s gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg

## For 20.04 or higher
NODE_MAJOR=20

## For 18.04 or lower
NODE_MAJOR=16

echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list

# Ubuntu 18.04 or higher
sudo -s apt install neovim nodejs zsh python3-pip dnsutils openssh-server xrdp net-tools tmux htop snap build-essential gcc -y

# Ubuntu 16.04
sudo -s apt install zsh gcc python python-pip python-dev net-tools tmux htop snap build-essential gcc -y

# Oh-My-Zsh 설치
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Fuzzing 용 도구
sudo -s apt install flex bison libncurses-dev libelf-dev libssl-dev debootstrap qemu-system -y

# Signal Analysis 용 도구
sudo -s apt install gnuradio octave libhackrf-dev libuhd-dev uhd-host libuhd-dev uhd-host liboctave-dev -y

# Snap Package
sudo -s snap install bpytop authy

```

## 데스크톱 환경 설치
주로 Server 이미지를 사용하므로 xrdp 사용을 위해 `MATE` 혹은 `KDE Plasama` 데스크톱 환경을 설치.

```
sudo -s apt install tasksel -y
sudo -s tasksel install ubuntu-mate-core
sudo -s tasksel install kde-desktop

```

## 보안 공부 환경
```
sudo -s apt install libc6-dbg libssl-dev libffi-dev build-essential gcc-multilib -y
python2 -m pip install --upgrade pip==20.3.4 && python2 -m pip install --upgrade pwntools

```

## 필요 파일 수동 다운로드 및 설치
### 권장 버전
[MesloLGS NF 폰트 다운로드 페이지](https://github.com/romkatv/powerlevel10k/blob/master/font.md#manual-font-installation), [Nanum Gothic 폰트 다운로드 페이지](https://creativestudio.kr/1734)

```
# MesloLGS NF Downlaod
curl -o lgs-regualr.ttf https://raw.githubusercontent.com/romkatv/powerlevel10k-media/master/MesloLGS%20NF%20Regular.ttf \
-o lgs-bold.ttf https://raw.githubusercontent.com/romkatv/powerlevel10k-media/master/MesloLGS%20NF%20Bold.ttf \
-o lgs-italic.ttf https://raw.githubusercontent.com/romkatv/powerlevel10k-media/master/MesloLGS%20NF%20Italic.ttf \
-o lgs-boldital.ttf https://raw.githubusercontent.com/romkatv/powerlevel10k-media/master/MesloLGS%20NF%20Bold%20Italic.ttf

# 나눔고딕은 수동 다운로드
unzip ./*.zip && sudo -s mv ./*.ttf /usr/share/fonts/ && fc-cache -rvf

# pacman의 경우 debtrap 사용

# lsd
## For amd64
wget https://github.com/lsd-rs/lsd/releases/download/v1.0.0/lsd-musl_1.0.0_amd64.deb

## For arm64
wget https://github.com/lsd-rs/lsd/releases/download/v1.0.0/lsd-musl_1.0.0_arm64.deb

# bat
## For 20.04 or higher
sudo -s apt install bat -y && mkdir -p ~/.local/bin && ln -s /usr/bin/batcat ~/.local/bin/bat

## For 16.04 / 18.04 amd64
wget https://github.com/sharkdp/bat/releases/download/v0.23.0/bat_0.23.0_amd64.deb

# VS Code
## For amd64
wget https://code.visualstudio.com/docs/?dv=linuxarm64_deb

## For arm64
wget https://code.visualstudio.com/download#

sudo -s dpkg -i *.deb

```

### Browser

- amd64 아키텍처: [Microsoft Edge Deb](https://www.microsoft.com/en-us/edge/download?form=MA13FJ) or [Direct 117.0.2045.47 Deb](https://packages.microsoft.com/repos/edge/pool/main/m/microsoft-edge-stable/microsoft-edge-stable_117.0.2045.47-1_amd64.deb?brand=M102)
  - Firefox 제거: `sudo -s snap remove firefox && sudo -s apt purge firefox`
- aarch64 아키텍처: `sudo -s snap install chromium && sudo -s snap remove firefox` 

### `keyd` Remapping
```
git clone https://github.com/rvaiya/keyd
cd keyd && make && sudo -s make install
sudo -s systemctl enable keyd && sudo systemctl start keyd
sudo -s vim /etc/keyd/default.conf

```
```
[ids]

*

[main]

capslock = rightalt
rightalt = capslock
backslash = backspace
backspace = backslash

```


# 4. 권장 레포지토리 복제
## 공통
powerlevel10k, AutoSuggestions 및 Highlighting 을 설치
```
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k && git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions && git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

```

## 보안 공부
```
git clone https://github.com/longld/peda.git ~/peda && git clone https://github.com/slimm609/checksec.sh ~/checksec && git clone https://github.com/scwuaptx/Pwngdb.git ~/Pwngdb && git clone https://github.com/Mipu94/peda-heap.git ~/peda-heap

```

# 5. 설치한 패키지 및 기타 설정
## Shell rc
```
powerlevel10k/powerlevel10k

plugins=(
    git
    zsh-autosuggestions
    zsh-syntax-highlighting
)

setopt inc_append_history

export GOROOT=/opt/compiler/go
export LINARO=/opt/compiler/linaro
export DIR_WRK=$HOME/workspace
export DIR_KER=$DIR_WRK/kernel
export DIR_IMG=$DIR_WRK/image

alias workdir="cd $DIR_WRK"
alias kerdir="cd $DIR_WRK/kernel/linux"
alias syzdir="cd $DIR_WRK/syzkaller"
alias vim="nvim"
alias vi="nvim"
alias lll="lsd -al --no-symlink --group-dirs=first"
alias ll="lsd -l --no-symlink --group-dirs=first"
alias ls="lsd --group-dirs=first"
alias lt="lsd --tree --no-symlink"
alias cat="batcat"
alias kqm="sudo -s pkill -9 qemu"
alias tmx="sh ~/tmux-st.sh"

# For higher than Intel 12th gen
alias updateall="sudo -v && syzdir && git pull origin master && make && kerdir && git pull origin master && make TARGETARCH=amd64 TARGETOS=linux -j`nproc` && syzdir && sudo taskset -c 0-15 sh ./run_x86-64.sh"

# For lower than Intel 11th gen
alias updateall="sudo -v && syzdir && git pull origin master && make && kerdir && git pull origin master && make TARGETARCH=amd64 TARGETOS=linux -j`nproc` && syzdir && sudo sh ./run_x86-64.sh"

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
sudo -s cp $HOME/checksec/checksec /usr/local/bin && cp $HOME/Pwngdb/.gdbinit $HOME/ && echo "source $HOME/peda/peda.py" >> $HOME/.gdbinit

```

## tmux
```
vim ~/.tmux.conf
set -g default-terminal "xterm"
set -g default-terminal "xterm-256color"
bind s split-window -h
bind v split-window -v
bind i resize-pane -U 2
bind j resize-pane -L 2
bind l resize-pane -R 2
bind k resize-pane -D 2

```

```
vim ~/tmux-st.sh

#!/bin/sh
tmux rename-window user
tmux new-session -d
tmux split-window -h
tmux selectp -t 1
tmux split-window -h
tmux select-layout even-horizontal
tmux selectp -t 2
tmux split-window -v
tmux attach-session

```

# 6. ssh 인증서 생성
아래 명령어를 통해 rsa4096으로 생성 가능. 중간 이메일이 적힌 곳에 본인 이메일을 적으면 된다.

```
ssh-keygen -t rsa -b 4096 -C "hominlab@gmail.com"

```

# 7. git 기본 설정
git config 를 위한 자주 사용하는 설정 들. 중간에 사용자 이름과 이메일에 본인 값을 넣어주면 된다.

```
git config --global user.name "iCAROS7" && git config --global user.email "hominlab@gmail.com" && git config --global init.defaultBranch main && git config --global pull.rebase false

```

# 8. Neovim 설정
## init.vim
```
mkdir -p $HOME/.config/nvim && touch $HOME/.config/nvim/init.vim && sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'

```
```
call plug#begin('~/.vim/plugged')

Plug 'neoclide/coc.nvim', {'branch': 'release'}
Plug 'nvim-treesitter/nvim-treesitter', {'do': ':TSUpdate'}
Plug 'preservim/nerdtree'
Plug 'vim-airline/vim-airline'
Plug 'vim-airline/vim-airline-themes'
Plug 'ronakg/quickr-cscope.vim'
Plug 'ctrlpvim/ctrlp.vim'
Plug 'blueyed/vim-diminactive'
Plug 'svermeulen/vim-cutlass'
Plug 'Shirk/vim-gas'

" Plug 'majutsushi/tagbar'
Plug 'preservim/tagbar'

Plug 'nanotech/jellybeans.vim'
" Plug 'morhetz/gruvbox'

call plug#end()

set number
set showmatch
set hlsearch
set clipboard=unnamedplus
set tabstop=4
" set mouse=a

if has("syntax")
	" 문법 강조를 수행
	syntax on	
endif

if has('nvim')
  inoremap <silent><expr> <c-space> coc#refresh()
else
  inoremap <silent><expr> <c-@> coc#refresh()
endif

let g:airline_powerline_fonts = 1
let g:airline_theme = 'luna'
let g:airline#extensions#tabline#formatter = 'unique_tail'
let g:airline_statusline_ontop = 1
let g:airline#extensions#tabline#enabled = 1
let g:airline#extensions#tabline#show_tabs = 1

```

## CoC 언어 서버
```
CocInstall coc-clangd coc-r-lsp coc-pyright coc-html coc-java coc-tsserver coc-json coc-go coc-css coc-cmake coc-sh

```

## TreeSitter 문법
```
TSInstall arduino bash c c_sharp cmake cpp css dockerfile git_rebase gitcommit gitignore go html java javascript json json5 kotlin llvm make markdown markdown_inline python r ruby sql yaml vim

```

# 9. Parallels Tools 설치
먼저 Parallels에서 Tool Install image를 마운트 해야한다. 혹시 ISO 파일이 필요하다면 Parallels.app/Contents/resource/tool 내에 있다.

```
sudo -s mkdir /mnt/cdrom && sudo -s mount /dev/cdrom /mnt/cdrom && sudo -s /mnt/cdrom/install

sudo -s init 6

```

---
수정일: 2023.10.03 12:30
