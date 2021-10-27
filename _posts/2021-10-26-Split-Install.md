---
title: "[따라하기] Windows ISO install.wim 파일 분할하기"
categories:
    - 팁_Tip
tags:
    - 따라하기
    - 윈도우
    - Windows
    - install.wim
    - dism
    - fat32
published: false
---
install.wim 파일 4기가 분할 하여 fat32 시스템에 넣기

안녕하세요. 본 글은 스스로를 위한 글에도 해당이 됩니다.

UEFI 사용 시 부팅 가능한 USB를 만들기 위하여 GPT 형식의 FAT32 디스크를 만들고는 합니다. 하지만 최근 윈도우 ISO의 설치 파일은 4기가가 넘어가므로 그 중 가장 큰 용량을 차지하는 `install.wim` 역시 자연스럽게 4기가가 넘어갑니다. 

평소에는 전혀 문제가 되지 않지만 FAT32 파일 시스템을 쓰는 경우 시스템 특성상 4GB보다 큰 파일을 사용 할 수 없습니다. 굳이 해당 파일 시스템인 곳에 4GB가 넘는 파일을 넣는다면 중간에 복사 오류로 넣을 수 없을 것 입니다.

