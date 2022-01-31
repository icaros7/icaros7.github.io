---
title:  "macOS 기본 설정 및 팁.md"
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
필자의 macOS 기본 설정 및 팁!

안녕하세요. 매번 여러 운영체제를 재설치 한 뒤 초기 설정은 가장 귀찮은 작업 중 하나 입니다. 이를 위해 필자의 기본 설정을 공유를 겸한 스스로를 위한 메모 차 남겨 둡니다.

# 1. Oh-My-Zsh 설치
[ohmyz.sh](https://ohmyz.sh/#install) 접속 후 아래 명령어 복사
```
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

이와 동시에 개발자 명령어 커맨드 라인 설치 확인 창 뜰 경우 설치

# 2. powerlevel10k 및 최애 플러그인 설치
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

# 3. 한국어 키보드 ₩ 원화 기호 대신 ` 백쿼트 사용하기
[원본 글](http://www.devkuma.com/books/pages/1173) 참조

`~/Library/KeyBindings/DefaultkeyBindings.dict` 파일 생성 및 아래 항목 추가

```
{
        "₩" = ("insertText:", "`");
}
```

# 4. 키보드 리맵핑
[원본 글](https://chuyeow.wtf/2020/06/24/remapping-keys-on-macos) 및 [애플 공식 기술 문서 테이블 표](https://developer.apple.com/library/archive/technotes/tn2450/_index.html#//apple_ref/doc/uid/DTS40017618-CH1-KEY_TABLE_USAGES) 참조

`~/Library/LaunchAgents/com.icaros7.KeyRemapping.plist` 파일 생성 및 아래 항목 추가

```
<!--
  31: \, |
  2A: Backspace
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

# 5. brew 설치하기
[brew.sh](https://brew.sh/index_ko) 에서 설치 진행

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

# 6. 자주 쓰는 brew 패키지
```
brew install \
--cask yt-music \
--cask discord \
--cask between \
--cask intellij-idea \
--cask rider \
--cask authy \
--cask adguard \
--cask microsoft-remote-desktop \
--cask microsoft-edge
```

# 7. ssh 인증서 생성
아래 명령어를 통해 rsa4096으로 생성 가능
```
ssh-keygen -t rsa -b 4096 -C "abc@def.com"
```

# 8. Xcode.app 직접 다운로드
앱스토어에서 설치 시 무척 오래 걸리며 종종 설치 실패가 뜨는 경우가 있다. 이럴 경우 [애플 개발자 다운로드 페이지](https://developer.apple.com/download/all/)에서 `Xcode`를 직접 다운로드 받으면 해결

# 9. 마우스 휠 반전
터치 패드의 경우 현존 대부분의 운영체제가 손가락을 아래로 할 경우 스크롤이 반전되어 페이지가 위로 올라간다. 다만 마우스 휠의 경우 애플 매직 마우스2, 마이크로소프트 아치 마우스 처럼 터치식이 아닌 경우 macOS에서 스크롤이 반전되어 이질감이 느껴진다.

이를 위해 **마우스만** 스크롤을 반전하기 위해 다음 어플리케이션을 사용한다.

[Scroll Reverser](https://pilotmoon.com/scrollreverser/)

---
수정: 2022.01.31 21:59