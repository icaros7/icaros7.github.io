---
title:  "[LU6500] iCAROS Official Rooting Kit / 루팅툴"
categories:
    - iCAROS7 Works
tags:
    - LU6500
    - bssq
    - 옵티머스q2
toc: true
redirect_from:
    - /194
    - /228
---
LG 옵티머스Q2 (LG-LU6500) 관련 iCAROS7 루팅 및 루팅툴 관련 모음

본 글은 [여기](https://minnote.net/icaros7%20works/BSSQ-Download/)에서 더 많은 정보를 확인 하실 수 있습니다.

블로그 이전 하는 동안 이전 2013~2014년경 배포 하였던 제 자료 중 복구가 가능 한 것들을 추스렸습니다.
LU6500 옵티머스Q2 관련 자료 만 모았습니다. 여러 버전을 소유한 경우 가장 최신 버전만 작성 하였습니다.

이때 github 미사용 및 사용 중이던 파일 호스트 사이트들이 대부분 문을 닫아 자료 복구가 대부분 안됩니다.

{% include ad_content.html %}

<hr>

# 사용 환경
본 글 내의 모든 어플리케이션은 윈도우 환경에서 사용 가능한 CUI 툴 입니다.

# iCAROS BSSQ One Tool Box
LG bssq 보드 사용 기종 공용 루팅 툴 입니다.

## v2
1. [미디어파이어](http://www.mediafire.com/download/t32ncpcjail4b5u/Icaros_BSSQ_One_Tool_Box_Dev.2.zip)

![](/assets/2021-01-23-LU6500-iCAROS-RootKit/1.jpg)

## v1
1. [구글 드라이브](https://drive.google.com/file/d/1tG9OxCGXHvSGOamCtSwtcn_AFBlyV_4p/view?usp=sharing)

# iCAROS Half Root
윈도우 환경에서 사용 가능한 CUI 루트 권한을 취득하지 않은 채 기본 앱을 지우는 시스템 툴 입니다.

## v1
1. <a href="https://drive.google.com/file/d/1sUbETGBxGKktLel9sCFeIfYvBQyksh37/view?usp=sharing" target="_blank">구글 드라이브</a>

# iCAROS One Windows
윈도우 환경에서 사용 가능한 CUI 128 펌웨어 루팅 툴 입니다.

## 1.3
1. <a href="https://drive.google.com/file/d/1LvVM4BPtyQjs5Nj8iLrfBb2rPfNrU4i4/view?usp=sharing" target="_blank">구글 드라이브</a>

# LG star, bssq 보드 APX 드라이버 설치 방법
## 드라이버 다운로드
1. [구글 드라이브](https://drive.google.com/file/d/1bKY5WYZ7_khyupuRRdfj76EWfp1N0a6A/view?usp=sharing)
2. [티스토리](https://minnote.tistory.com/attachment/cfile2.uf@210F473B55F44A9710135A.zip)

## Windows 8 이상의 환경
윈도우 8 이상의 운영체제 사용시 타사 INF 파일에 디지털 서명 정보가 없음으로 인해 설치가 불가능 합니다.

따라서 고급 부팅 모드에서만 설치가 가능 합니다.

1. Windows 키와 X 키를 눌러 시스템 메뉴를 띄웁니다.
2. "다시시작" 버튼을 **Shift 키를 누른 채로** 누릅니다.
   ![](/assets/2021-01-23-LU6500-iCAROS-RootKit/8.png)
3. "문제 해결"
4. "고급 옵션"
5. "시작 옵션"
6. "다시 시작"
7. 7번 서명되지 않은 드라이버 로드 선택
8. 아래 절차를 수행 합니다.

## Windows 7 이하의 환경

1. 기기와 배터리를 탈착 합니다.
   ![](/assets/2021-01-23-LU6500-iCAROS-RootKit/2.jpg)
2. Vol+ 키와 Vol-키를 누른 상태로 USB를 연결 합니다.
   ![](/assets/2021-01-23-LU6500-iCAROS-RootKit/3.png)
3. 장치 관리자를 실행 합니다.
4. **"APX"** 항목 드라이버 업데이트를 선택 합니다.
   ![](/assets/2021-01-23-LU6500-iCAROS-RootKit/5.png)   
   위 사진은 예시 입니다. 설치가 완료 될 경우 "NVIDIA USB Boot-recovery driver for Mobile Devices"가 인식 됩니다.
5. "컴퓨터에서 드라이버 소프트웨어 찾아보기"를 선택 합니다.
   ![](/assets/2021-01-23-LU6500-iCAROS-RootKit/6.png)
6. 사전에 받은 드라이버 폴더를 선택 합니다.
   ![](/assets/2021-01-23-LU6500-iCAROS-RootKit/7.png)