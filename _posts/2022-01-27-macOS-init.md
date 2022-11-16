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

# 2. Oh-My-Zsh 설치
[ohmyz.sh](https://ohmyz.sh/#install) 접속 후 아래 명령어 복사
```
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

# 3. powerlevel10k 및 최애 플러그인 설치
`powerlevel10k`의 경우 강력히 권장되는 폰트인 `MesloLGS NF` 폰트 설치 요구

[공식 git 내 Readme.md 중 폰트 관련 페이지](https://github.com/romkatv/powerlevel10k#meslo-nerd-font-patched-for-powerlevel10k)에서 폰트 파일 다운로드 하여 설치. 단,  `iTerm2` 등의 서드파티 터미널 사용 시 생략 가능

아래 명령어를 통해 `powerlevel10k`, `zsh-autosuggestions`, `zsh-syntax-highlighting` 를 설치 해 줍니다.

```
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

이후 `~/.zshrc` 내 테마 설정 부분과 플러그인 목록에 아래를 참조하여 수정 하여 줍니다.

```
powerlevel10k/powerlevel10k
zsh-autosuggestions
zsh-syntax-highlighting
```

# 4. 한국어 키보드 ₩ 원화 기호 대신 ` 백쿼트 사용하기
[원본 글](http://www.devkuma.com/books/pages/1173) 참조

`~/Library/KeyBindings/DefaultkeyBinding.dict` 파일 생성 및 아래 항목 추가

```
{
        "₩" = ("insertText:", "`");
}
```

# 5. 키보드 리맵핑
[원본 글](https://chuyeow.wtf/2020/06/24/remapping-keys-on-macos) 및 [애플 공식 기술 문서 테이블 표](https://developer.apple.com/library/archive/technotes/tn2450/_index.html#//apple_ref/doc/uid/DTS40017618-CH1-KEY_TABLE_USAGES) 참조

`~/Library/LaunchAgents/com.icaros7.KeyRemapping.plist` 파일 생성 및 아래 항목 추가

```
<!--
  31: \, 2A: Backspace
  E6: R_Option, 49: Insert
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
            "HIDKeyboardModifierMappingSrc": 0x7000000E6,
            "HIDKeyboardModifierMappingDst": 0x700000049
          },
	  {
            "HIDKeyboardModifierMappingSrc": 0x700000049,
            "HIDKeyboardModifierMappingDst": 0x7000000E6
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

# 6. brew 설치하기
[brew.sh](https://brew.sh/index_ko) 에서 설치 진행

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

# 7. 자주 쓰는 brew 패키지
## 공통 패키지
```
brew install \
--cask yt-music \
--cask discord \
--cask between \
--cask intellij-idea \
--cask rider \
--cask visual-studio-code \
--cask adguard \
--cask scroll-reverser \
--cask keka \
--cask authy \
--cask microsoft-remote-desktop \
--cask microsoft-edge

brew install neovim ruby
```

## 맥북 혹은 랩톱 환경 용
```
brew install \
--cask keyboard-cleaner
```

## 아이맥 혹은 데스크톱 환경 용
```
brew install \
--cask sensiblesidebuttons
```

# 8. ssh 인증서 생성
아래 명령어를 통해 rsa4096으로 생성 가능. 중간 이메일이 적힌 곳에 본인 이메일을 적으면 된다.

```
ssh-keygen -t rsa -b 4096 -C "hominlab@gmail.com"
```

# 9. git 기본 설정
git config 를 위한 자주 사용하는 설정 들. 중간에 사용자 이름과 이메일에 본인 값을 넣어주면 된다.

```
git config --global user.name "iCAROS7"
git config --global user.email "hominlab@gmail.com"
git config --global init.defaultBranch main
git config --global pull.rebase false
```

---
수정: 2022.07.15 14:00
