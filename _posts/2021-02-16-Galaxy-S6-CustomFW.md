---
title:  "갤럭시S6 (SM-G920S)를 2021년에 사용하기"
categories:
    - 팁_Tip
tags:
published: false
toc: true
---
갤럭시S6 다시 살려 사용하기! 안드로이드 9.0 업데이트

안녕하세요!   
**본 글을 따라하는 것은 모두 스스로에게 책임이 있음을 미리 알려드립니다**

저는 기본적으로 제 기본 회선에 아이폰Xs Max를 사용 중입니다. 그리고 투넘버 겸용(?) 부가 회선에 갤럭시 시리즈를 사용하여 지갑 처럼 같이 사용 하고 있습니다. 얼마전까지는 갤럭시S6을 사용하였는데 요전에 갤럭시A50으로 기기를 바꾸면서 S6은 이제 더이상 삼성페이에 의존하지 않을 수 있게 되었습니다.

# 득과 실
우선 본 글에선 아래와 같은 사항을 통하여 갤럭시S6을 사용 합니다.

- Floyd Note 8 Port 펌웨어 사용 (안드로이드 9.0, OneUI 1.0, 그말은 GoodLock 도 사용 가능)
- CronosKernel 사용 (성능 및 배터리 최적화, 차이 상당히 큽니다)
- 갤럭시 노트 및 상위 S 기능 사용 (AOD, 엣지라이트닝 등)
- 다크 모드
- GoodLock 모듈 중 끊기지 않는 멀티 윈도우 (게임 2개 동시 실행 가능)

이 5가지를 얻기 위하여 전 아래와 같은 것들을 포기 하였습니다.

- 삼성 페이
- 삼성 페스
- 삼성 헬스 (실행은 되는데 사용 중 중지 됩니다)
- Knox 일부 기능 (기본적으로 Knox Warranty가 깨집니다.)
- Knox 워런티 깨짐으로 인한 금융 앱 사용 불능 (우회 방법이 있겠지만 전 A50에서 사용 중이라 무관)

만약 저와 같은 것들을 포기하고 사용 하실 수 있으면서 제가 얻은 것들이 합리적이라고 생각 되신다면 좋은 팁이라 생각 됩니다.

저는 가장 큰 차이점이 안드로이드 9.0 업데이트로 인한 앱 호환성, 다크모드, GoodLock, OneUI 를 얻는 것과 기존 7.0 삼성 순정 롬 대비 매우 매우 쾌적한 성능과 배터리 러닝 타임이 주 목적 이었습니다.

하지만 Knox 워런티를 포기해야하는 만큼 이 부분에 관하여 자세히 모르시거나 갤럭시S6 한대만을 사용하고 계신다면 추천드리고 싶지 않습니다.

# 준비물 및 환경 구성 안내
![](../assets/2021-02-16-Galaxy-S6-CustomFW/1.jpg)   
본 글은 아래와 같은 환경을 기준으로 작성 되었습니다.

- 한국 SKT 정발 갤럭시S6 SM-G920S (zeroflteskt 기준, KT G920K, LGU G920L 모두 가능)
- 사용 통신사 SKT
- 별도 배터리 개조 상태가 아닌 모든 것이 기본 상태
- Windows 10 Pro 20H2가 설치된 Surface Pro 4

또한 필요한 것들을 설명 드리겠습니다. 다운로드 링크가 첨부 된 경우 제가 사용 한 파일들 입니다. 제가 신원을 보장 드리는 사이트는 아니므로 직접 구하셔도 무방합니다.

- Micro-USB B타입 - USB A타입 케이블
- Floyd Note8 Port V5.1 / <a href="https://forum.xda-developers.com/t/guide-use-samsung-pay-and-knox-on-your-rooted-device.3549449/" target="_blank"> ananjaser1211씨의 포스트</a>
  - 위 링크에서 두번째 쓰레드의 "Downloads" 부분에서 한국에선 GoogleDrive를 통하여 다운로드 받는게 가장 빠를 듯 합니다.
- 삼성 모바일 USB 드라이버 / <a href="https://developer.samsung.com/mobile/android-usb-driver.html" target="_blank">developer.samsung.com</a>
  - 상단의 "Samsung_USB_Driver~~~"라 적힌 둥근 버튼을 눌러주시면 됩니다.
- 모바일 오딘 3.12.3 / <a href="https://odindownload.com/download/#.YCttnZdxcow" target="_blank">odindownload.com</a>
  - "SamsungOdin 3.12.3" 를 검색해 주시고 그 아래에 있는 Download Now 이미지를 눌러주시면 됩니다.
- zeroflte(G920SLK) 용 TWRP 이미지 / <a href="https://dl.twrp.me/zeroflteskt/" target="_blank">twrp.me</a>
  - 가장 최신 버전의 "twrp-3.3.1-0-zeroflteskt.img.tar" 를 받아주시면 됩니다.
- Magisk / <a href="https://magiskmanager.com/" target="_blank">magiskmanager.com</a>
  - "Then, Download the Magisk file from below"를 검색하신 다음 아래에 있는 Download Magisk *.*를 받아주시면 됩니다.
- 인터넷 사용 가능 환경 (약 2.6GB 정도 사용)
- 랩톱의 경우 충분한 배터리와 어느정도 충전이 완료된 갤럭시S6

{% include ad_content.html %}

<hr>

# 설치 과정
## 환경 준비
우선 아래와 같은 것들을 모두 사전에 준비 합니다.

- 다운로드 받은 아래 파일들을 모두 기억 할 수 있는 장소에 저장 합니다. (경로에 한국어가 들어가지 않으면 더 좋습니다.)
  - FloydRom_V5.1_Exynos7420.zip
  - Magisk-v21.4.zip
  - twrp-3.3.1-0-zeroflteskt.img.tar
  - Odin3_v3.12.3.zip
- 삼성 USB 모바일 드라이버는 "SAMSUNG_USB_Driver_for_Mobile_Phones.exe"를 실행하여 설치 합니다.
- Odin_v3.12.3은 압축을 해제 합니다.

다음 갤럭시S6을 다운로드 모드로 진입 하여야 합니다. 아래와 같은 조작을 통하여 가능 합니다.   
전원을 끈 뒤 **볼륨 하 + 홈 + 전원 버튼**을 동시에 아래와 같은 화면이 나올 때 까지 꾸욱 누릅니다.
