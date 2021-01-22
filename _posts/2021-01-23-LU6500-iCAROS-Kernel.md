---
title:  "[LU6500] iCAROS Kernel Official"
categories:
    - iCAROS7 Works
tags:
    - LU6500
    - bssq
    - 옵티머스q2
toc: true
redirect_from:
    - /180
    - /193
    - /216
    - /217
    - /218
---

본 글은 [여기](https://minnote.net/icaros7%20works/BSSQ-Download/)에서 더 많은 정보를 확인 하실 수 있습니다.

블로그 이전 하는 동안 이전 2013~2014년경 배포 하였던 제 자료 중 복구가 가능 한 것들을 추스렸습니다.
LU6500 옵티머스Q2 관련 자료 만 모았습니다. 여러 버전을 소유한 경우 가장 최신 버전만 작성 하였습니다.

이때 github 미사용 및 사용 중이던 파일 호스트 사이트들이 대부분 문을 닫아 자료 복구가 대부분 안됩니다.

{% include ad_content.html %}

<hr>

## 사용법
- APX모드를 사용하여 boot 영역에 flash
- TWRP, CWM 등을 사용 하여 boot 영역에 설치

# 업데이트06
가장 최신 버전인 업데이트06 은 파일 유실로 복구가 불능 합니다.

# 업데이트05
## LU650137 펌웨어
1. <a href="https://drive.google.com/file/d/1PB8mDbw61BaJ6jjYjd4vn4xjUs6j00rr/view?usp=sharing" target="_blank">구글 드라이브</a>

## 체인지로그
- 램디스크 압축 옵션 변경 GZIP -> None 부팅속도 향상
- 커널 압축 옵션 변경 GZIP -> Lzo

# 업데이트04
## LU650137 펌웨어
1. <a href="https://drive.google.com/file/d/1q1g_AkL53HXU1xGrnirhImNjyd0ttC9s/view?usp=sharing" target="_blank">구글 드라이브</a>

# 업데이트03
업데이트03의 경우 LU650137펌웨어용 커널은 유실 되었습니다.

## LU650137 펌웨어 터치 드라이버 제외
1. <a href="https://drive.google.com/file/d/1LFPdHVyH1CXeLo2HhLfM7vNiKVSJWA0P/view?usp=sharing" target="_blank">구글 드라이브</a>

일명 귀신터치라 불리는 터치 패널 문제로 혼자 터치가 되는 현상을 겪고 계신 분 중 아예 터치를 포기하고 하드웨어 키보드로 사용 하실 분들을 위한 커널 입니다.
설치 시 터치 스크린이 작동되지 않습니다.

**홈 버튼 대체제와 잠금 보안을 해제 하신 뒤 설치하여 주십시오**

## CM10 커스텀룸
1. <a href="https://drive.google.com/file/d/1W72yMbMyW0_4eYgmhhq1v6wle6g5WkY_/view?usp=sharing" target="_blank">구글 드라이브</a>

CM10 용 iCAROS 커널 업데이트03 입니다.

## 체인지로그
- Scary 가버너 탑제 및 기존 죽인 가버너 살림
- 충전 이미지 표시 오류 수정
- 램디스크 내 더미 파일 수정
- sio I/O 가버너 추가
- 750Mhz 추가
- 터치 LED 전압 수정 (Thanks to 백원만)
- CWM 업데이트 ZIP 스크립트 수정