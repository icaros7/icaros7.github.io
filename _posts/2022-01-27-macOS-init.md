---
title:  "macOS 기본 설정 및 팁.md"
categories:
    - TIPs-macOS
tags:
    - 따라하기
    - bash
    - zsh
    - shell
    - Oh My Zsh
toc: true
published: true
---
필자의 macOS 기본 설정 및 팁!

안녕하세요. 매번 여러 운영체제를 재설치 한 뒤 초기 설정은 가장 귀찮은 작업 중 하나 입니다. 이를 위해 필자의 기본 설정을 공유를 겸한 스스로를 위한 메모 차 남겨 둡니다.


# 1. Xcode.app 및 커맨드라인 도구 직접 다운로드
가장 오래 걸리는 걸 가장 먼저 해야한다. 앱스토어에서 설치 시 무척 오래 걸리며 종종 설치 실패가 뜨는 경우가 있다. 이럴 경우 [애플 개발자 다운로드 페이지](https://developer.apple.com/download/all/)에서 `Xcode` 및 `Command Line Tools for Xcode`를 직접 다운로드 받으면 해결

## 1-1. 커맨드 라인 도구만 설치하기
XCode 대신 Vim이나 CLion을 쓰는 환경도 자주 구성하는 것 같다. 이럴 경우 터미널을 띄우고 `git` 명령어를 한번만 사용해주면 설치가 시작 된다.

# 2. Oh-My-Zsh 설치
[ohmyz.sh](https://ohmyz.sh/#install) 접속 후 아래 명령어 복사
```
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

# 3. powerlevel10k 및 최애 플러그인 설치
`powerlevel10k`의 경우 강력히 권장되는 폰트인 `MesloLGS NF` 폰트 설치 요구

[공식 git 내 Readme.md 중 폰트 관련 페이지](https://github.com/romkatv/powerlevel10k#meslo-nerd-font-patched-for-powerlevel10k)에서 폰트 파일 다운로드 혹으 curl을 통해 다운로드 하여 설치. 단,  `iTerm2` 등의 서드파티 터미널 사용 시 생략 가능

```
curl -o regualr.ttf https://raw.githubusercontent.com/romkatv/powerlevel10k-media/master/MesloLGS%20NF%20Regular.ttf \
-o bold.ttf https://raw.githubusercontent.com/romkatv/powerlevel10k-media/master/MesloLGS%20NF%20Bold.ttf \
-o italic.ttf https://raw.githubusercontent.com/romkatv/powerlevel10k-media/master/MesloLGS%20NF%20Italic.ttf \
-o boldital.ttf https://raw.githubusercontent.com/romkatv/powerlevel10k-media/master/MesloLGS%20NF%20Bold%20Italic.ttf
```

아래 명령어를 통해 `powerlevel10k`, `zsh-autosuggestions`, `zsh-syntax-highlighting` 를 설치 해 줍니다.

```
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k && git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions && git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

# 4. 키보드 관련
```
mkdir ~/Library/LaunchAgents ~/Library/KeyBindings && touch ~/Library/KeyBindings/DefaultkeyBinding.dict ~/Library/LaunchAgents/com.icaros7.KeyRemapping.plist
```
## 4-1. 한국어 키보드 ₩ 원화 기호 대신 \` 백쿼트 사용하기
[원본 글](http://www.devkuma.com/books/pages/1173) 참조

`~/Library/KeyBindings/DefaultkeyBinding.dict` 파일 생성 및 아래 항목 추가

```
{
        "₩" = ("insertText:", "`");
}
```

# 4-2. 키보드 리맵핑
[원본 글](https://chuyeow.wtf/2020/06/24/remapping-keys-on-macos) 및 [애플 공식 기술 문서 테이블 표](https://developer.apple.com/library/archive/technotes/tn2450/_index.html#//apple_ref/doc/uid/DTS40017618-CH1-KEY_TABLE_USAGES) 참조

`~/Library/LaunchAgents/com.icaros7.KeyRemapping.plist` 파일 생성 및 아래 항목 추가

```
<!--
  31: \, 2A: Backspace
  E4: R_Command, 49: Insert
-->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.example.KeyRemapping</string>
    <key>ProgramArguments</key>
    <array>
        <string>/usr/bin/hidutil</string>
        <string>property</string>
        <string>--set</string>
        <string>{"UserKeyMapping":[
	  {
            "HIDKeyboardModifierMappingSrc": 0x7000000E4,
            "HIDKeyboardModifierMappingDst": 0x700000049
          },
	  {
            "HIDKeyboardModifierMappingSrc": 0x700000049,
            "HIDKeyboardModifierMappingDst": 0x7000000E4
          },
	  {
            "HIDKeyboardModifierMappingSrc": 0x700000031,
            "HIDKeyboardModifierMappingDst": 0x70000002A
          },
          {
            "HIDKeyboardModifierMappingSrc": 0x70000002A,
            "HIDKeyboardModifierMappingDst": 0x700000031
         }
        ]}</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
</dict>
</plist>
```

# 5. `.zshrc` 구성
```
powerlevel10k/powerlevel10k
plugins=(
	git
	zsh-autosuggestions
	zsh-syntax-highlighting
)

alias workdir="cd /Users/icaros7/workspace"
alias vim="nvim"
alias vi="nvim"
alias lll="lsd -al --no-symlink --group-dirs=first"
alias ll="lsd -l --no-symlink --group-dirs=first"
alias ls="lsd -al --group-dirs=first"
alias lt="lsd --tree --no-symlink"
alias cat="batcat"
```

# 6. brew 및 Rosetta2 설치하기
[brew.sh](https://brew.sh/index_ko) 에서 설치 진행

```
sudo softwareupdate --install-rosetta
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
echo '# Set PATH, MANPATH, etc., for Homebrew.' >> /Users/icaros7/.zprofile && echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/icaros7/.zprofile && eval "$(/opt/homebrew/bin/brew shellenv)"
```

# 7. 자주 쓰는 brew 패키지
## 공통 패키지
```
sudo -v && brew install \
--cask yt-music \
--cask discord \
--cask between \
--cask intellij-idea \
--cask rider \
--cask clion \
--cask aldente \
--cask sensiblesidebuttons \
--cask visual-studio-code \
--cask scroll-reverser \
--cask keka \
--cask authy \
--cask microsoft-remote-desktop \
--cask microsoft-edge \
--cask silicon-info \
--cask anydesk \
--cask fig \
--cask adguard \
--cask samsung-dex

sudo -v && brew install neovim ruby bat node lsd tmux htop
```

## 맥북 혹은 랩톱 환경 용
```
brew install \
--cask keyboard-cleaner
```

# 8. ssh 인증서 생성
아래 명령어를 통해 rsa4096으로 생성 가능. 중간 이메일이 적힌 곳에 본인 이메일을 적으면 된다.

```
ssh-keygen -t rsa -b 4096 -C "hominlab@gmail.com"
```

# 9. git 기본 설정
git config 를 위한 자주 사용하는 설정 들. 중간에 사용자 이름과 이메일에 본인 값을 넣어주면 된다.

```
git config --global user.name "iCAROS7" && git config --global user.email "hominlab@gmail.com" && git config --global init.defaultBranch main && git config --global pull.rebase false
```

# 10. 자주 쓰는 서드파티 앱 및 도구
- [Fluffy](https://github.com/tml1024/FluffyDisplay): 가상 모니터 생성 앱

## 10-1. Neovim 설정
```
mkdir -p /Users/icaros7/.config/nvim && touch /Users/icaros7/.config/nvim/init.vim
sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim --create-dirs \
       https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
       
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

## 10-2. CoC 언어 서버 설치
```
CocInstall coc-clangd coc-r-lsp coc-pyright coc-html coc-java coc-tsserver coc-json coc-go coc-css coc-cmake coc-sh
```

## 10-3. TreeSitter 문버 설치
```
TSInstall arduino bash c c_sharp cmake cpp css dockerfile git_rebase gitcommit gitignore go html java javascript json json5 ko
tlin llvm make markdown markdown_inline python r ruby sql yaml vim
```

---
수정: 2023.01.28 12:00
