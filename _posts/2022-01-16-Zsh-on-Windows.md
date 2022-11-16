---
title:  "[따라하기] Windows 에서 Zsh 및 Oh My Zsh 사용하기"
categories:
    - TIPs-Windows
tags:
    - 따라하기
    - bash
    - zsh
    - 윈도우
    - Windows
    - shell
    - Oh My Zsh
toc: true
published: false
---
Windows 환경에서 Zsh 사용하기!

안녕하세요.

이 글은 저를 위한 글이자 Windows 환경에서도 Zsh 환경이 편하신 분들을 위해 적는 팁이면서 자습서 입니다.

# 1. 프로그램 선택하기
본인이 사용할 커맨드 프로그램을 먼저 선택을 해 주셔야 합니다. 필자의 경우 WSL (Windows Subsystem Linux, 이하 WSL)를 통해 Linux 가상 머신을 `Windows Terminal` 앱을 통해 실행하며 그곳에서 진행을 하였습니다.

그 외 `Git for Windows`를 설치할 경우 같이 설치되는 `MingW64`나 `PowerShell`에 설치하는 분들도 계시지만 저의 경우에는 PowerShell의 경우 Zsh 사용에 제한적이라는 이야기를 들어 폰트 설정이 편한 `Windows Terminal` 앱과 `WSL` 동시 사용을 위해 그렇게 하였습니다.

따라서 본 글에서는 `WSL`을 이용한 방법을 소개해 드리겠습니다.

# 2. 기본 환경 구성
## 2-1. WSL 및 가상 머신 활성화 시키기
우선 본인의 데스크톱 또는 랩톱 환경이 가상화 기술이 지원 되어야 합니다. CPU가 지원된다 한들 BIOS 또는 UEFI 단에서 활성화가 되어야 합니다. 요 근래 나오는 대부분의 랩톱들은 활성화가 되어있어 따로 설정 할 필요가 없지만, 데스크톱의 경우 별도로 확인을 하여야 합니다.