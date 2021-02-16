---
title:  "갤럭시S6 (SM-G920S) 커스텀 안드로이드 9.0 (OneUI 1.0) 설치하기"
categories:
    - 팁_Tip
tags:
    - G920S
    - G920K
    - G920L
    - G925
    - G928
    - N920
    - 갤럭시S6
    - 9.0_업데이트
toc: true
---
갤럭시S6 다시 살려 사용하기! 안드로이드 9.0 및 OneUI 1.0 업데이트

안녕하세요!   
**본 글을 따라하는 것은 모두 스스로에게 책임이 있음을 미리 알려드립니다**

저는 기본적으로 제 기본 회선에 아이폰Xs Max를 사용 중입니다. 그리고 투넘버 겸용(?) 부가 회선에 갤럭시 시리즈를 사용하여 지갑 처럼 같이 사용 하고 있습니다. 얼마전까지는 갤럭시S6을 사용하였는데 요전에 갤럭시A50으로 기기를 바꾸면서 S6은 이제 더이상 삼성페이에 의존하지 않을 수 있게 되었습니다.

저는 사용하며 Samsung Experience 8.0이 8.1이나 8.5와 다르게 매우 구버전이어서 사용이 불편하여 Modded 앱들을 사용할 정도로 불만족 이었습니다. 또한 게임 툴즈 역시 OneUI2.5와 다르게 완전히 화면이 꺼지지 않으며, GoodLock 기능이 없는 것, 보안폴더 내 앱이 Play framework에 접근이 불가능한 부분이 매우 불편하였으나 아래 과정을 통하여 갤럭시 노트8의 안드로이드 9.0 삼성 롬을 엑시노스7420 기기들에게 포팅시킨 롬 사용을 통하여 해결 하였습니다.

# 1. 득과 실
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
※위 사항들은 Knox Warranty 깨짐으로 인한 추후 삼성 공식 롬으로 복구를 하셔도 동일한 사항 입니다.

만약 저와 같은 것들을 포기하고 사용 하실 수 있으면서 제가 얻은 것들이 합리적이라고 생각 되신다면 좋은 팁이라 생각 됩니다.

저는 가장 큰 차이점이 안드로이드 9.0 업데이트로 인한 앱 호환성, 다크모드, GoodLock, OneUI 를 얻는 것과 기존 7.0 삼성 순정 롬 대비 매우 매우 쾌적한 성능과 배터리 러닝 타임이 주 목적 이었습니다.

하지만 Knox 워런티를 포기해야하는 만큼 이 부분에 관하여 자세히 모르시거나 갤럭시S6 한대만을 사용하고 계신다면 추천드리고 싶지 않습니다.

또한 아래와 같은 것들이 Floyd Note 8 Port v5 기준으로 버그가 있습니다.
- 하드웨어 컴포저 (앱 렌더링 방식 중 하나이며, OneUI 소스가 공개가 안되어있어 사용이 불가능)
- 카메라가 서드파티 앱에서 제대로 동작하지 않으며, WhatsApp 사용이 불가능하다는 피드백이 많음 (기본 카메라 역시 자동밝기 사용시 사용 불가능)
- S6/S6엣지 모델의 경우 삼성 갤러리 실행 시 화면이 깜빡거립니다. 밝기가 살짝 줄어들었다 다시 늘어납니다.

# 2. 준비물 및 환경 구성 안내
![](../assets/2021-02-16-Galaxy-S6-CustomFW/1.jpg)   
본 글은 아래와 같은 환경을 기준으로 작성 되었습니다.

- 한국 SKT 정발 갤럭시S6 SM-G920S (zeroflteskt 기준, KT G920K, LGU G920L 모두 가능)
  - 해외판 갤럭시S6, 갤럭시S6 Edge (SM-G925x), S6 Edge+ (SM-G928x), 노트5 (SM-N920x) 등의 기기 역시
  - 같은 방법으로 가능하지만 이 글을 100프로 따라하면 안됩니다. 각 기기에 맞는 TWRP 이미지와 기기 설정 과정이 필요 합니다.
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
- Magisk (선택사항) / <a href="https://magiskmanager.com/" target="_blank">magiskmanager.com</a>
  - "Then, Download the Magisk file from below"를 검색하신 다음 아래에 있는 Download Magisk *.*를 받아주시면 됩니다.
- 인터넷 사용 가능 환경 (약 2.6GB 정도 사용)
- 랩톱의 경우 충분한 배터리와 어느정도 충전이 완료된 갤럭시S6
- **모든 기기 내 내용물은 사전 백업 (공장 초기화 과정 필수)**

# 3. 설치 과정
## 3-1. 환경 준비
우선 아래와 같은 것들을 모두 사전에 준비 합니다.

- 다운로드 받은 아래 파일들을 모두 기억 할 수 있는 장소에 저장 합니다. (경로에 한국어가 들어가지 않으면 더 좋습니다.)
  - FloydRom_V5.1_Exynos7420.zip
  - Magisk-v21.4.zip (선택 사항. 이걸 알아보신다면 충분히 사용 가능하리라 믿습니다.)
  - twrp-3.3.1-0-zeroflteskt.img.tar
  - Odin3_v3.12.3.zip
- 삼성 USB 모바일 드라이버는 "SAMSUNG_USB_Driver_for_Mobile_Phones.exe"를 실행하여 설치 합니다.
- Odin_v3.12.3은 압축을 해제 합니다.

## 3-2. 다운로드 모드 진입
다음 갤럭시S6을 다운로드 모드로 진입 하여야 합니다. 아래와 같은 조작을 통하여 가능 합니다.   
전원을 끈 뒤 **<u>볼륨 하</u> + 홈 + 전원 버튼**을 동시에 아래와 같은 화면이 나올 때 까지 꾸욱 누릅니다.

![](/assets/2021-02-16-Galaxy-S6-CustomFW/2.jpeg)

다음 볼륨 상 키를 눌러 계속 진행을 선택 합니다.

![](/assets/2021-02-16-Galaxy-S6-CustomFW/4.jpeg)   
Knox Warranty 0x0의 마지막 모습 입니다...

## 3-3. 커스텀 리커버리 TWRP 설치
<u>**본 과정은 삼성의 Knox Warranty를 0x0이 아닌 다른 값으로 변형하여 워런티가 깨집니다**</u>  
**<u>모든 과정은 사용자에게 책임이 있으며</u>, 이로 인한 모든 상황을 해결할 마음과 용기가 있는 경우에만 시도 하십시오. 본 글은 한국 내수 기기를 기준으로 작성되었습니다. 그외 해외 기기로 글을 그대로 따라하지 마십시오.**

![](/assets/2021-02-16-Galaxy-S6-CustomFW/3.png)

1. "Odin_v3.12.3.exe"를 실행 합니다.
2. 갤럭시S6을 연결 합니다.
   1. 연결 성공 시 **<span style="color:red">빨간색</span>** 박스 부분에 0번으로 기기가 인식 됩니다.
3. **<span style="color:blue">파란색</span>** 박스 부분 Option 탭에서 "Auto Reboot"를 해제 합니다.
4. **<span style="color:green">초록색</span>** 박스 부분 "AP" 버튼을 클릭하여 TWRP tar 이미지를 선택 합니다.
5. 모든것이 완료 되었다면 **<span style="color:purple">보라색</span>** 박스 부분의 "Start" 버튼을 눌러주시면 리커버리가 설치 됩니다.
6. 성공 시 **<span style="color:red">빨간색</span>** 박스 부분에 Pass 가 적힙니다.

실패할 경우 AP에 정상적인 파일을 플래싱 한 것이 맞는지 다시 한번 더 확인 해주세요. AP가 맞는 경우 TWRP 파일을 다시 재 다운로드 하여 선택하여 주세요.

{% include ad_content.html %}

<hr>

## 3-4. 리커버리 부팅
위 과정이 완료가 되었다면 Cyan 색 화면에서 아직 멈추어 있을 것 입니다.

아래와 같은 과정을 통하여 리커버리로 부팅 합니다.

한번 과정을 전체를 다 본 후에 따라하여 주십시오.

1. **<u>볼륨 상 + 볼륨 하</u> + 홈 + 전원** 버튼을 3초동안 꾹 눌러 강제 재부팅 시킵니다.
2. 화면이 꺼지는 순간 3번을 따라합니다.
3. **<u>볼륨 상</u> + 홈 + 전원** 버튼을 Galaxy S6 로고가 없어질때 까지 꾹 누릅니다.

## 데이터 백업 혹은 와이프
만일 삼성 기본 롬으로 간편하게 되돌아오고 싶다면 백업을 먼저 해야 합니다. 저는 시스템과 데이터 백업은 따로 하지 않았습니다. 하지만 Modem 파티션과 EFI 파티션 백업은 권장 드립니다.   
아래와 같은 화면이 성공적으로 나왔다면 TWRP 부팅에 성공 한 것 입니다.

![](/assets/2021-02-16-Galaxy-S6-CustomFW/5.jpg)

### 3-5. 백업
**<span style="color:green">초록색</span>** 박스 부분 Backup 을 터치하여 원하는 항목을 선택 합니다.

1. 만일 순정 롬으로 복구를 하고 싶다면 System, Data, Dalivik/ART Cache, Cache를 체크 한 뒤 아래 Swipe를 해줍니다.
2. 다시 Backup 으로 돌아가 Modem 과 EFI를 체크 한 뒤 Swipe를 하여 백업 합니다.
   1. EFI와 Modem 파티션은 위 순정 롬 백업과 별개로 한번에 하지 않는 것을 추천 드립니다.
3. PC와 USB로 연결하면 MTP가 기본적으로 동작 됩니다. 평소 하듯이 내부 저장소\TWRP 폴더를 통째로 PC 어딘가에 백업 해 둡니다.

3번은 아래 **3-6. 와이프** 단계에서 내장 SD카드 초기화를 원치 않으시면 안하셔도 무방합니다. 저는 혹시 모를 상황을 대비하여 언제나 클라우드 혹은 데이터 저장소에 백업 합니다.

### 3-6. 와이프
1. **<span style="color:red">빨간색</span>** 박스 부분 Wipe - Advanced Wipe를 들어옵니다.
2. 아래와 같은 항목을 체크 합니다. Internal Stroage 를 체크 시 내장 SD카드가 초기화 됩니다.
   1. Dalivik/ART Cache
   2. System
   3. Data
   4. Internal Stroage (내장 SD카드 초기화를 원치 않으시면 체크 해제)
      1. 순정롬에서 오시는 경우 추천 드립니다. (영향은 없지만 깨끗한 사용을 위함. 롬 제작자 역시 권장)
   5. Cache

### 3-7. Floyd ROM Aroma Installer 실행
1. PC와 연결 합니다. MTP로 연결되니 내부 저장소 폴더로 이동하여 주세요.
2. 아래 두 파일을 원하는 위치로 복사 합니다.
   1. FloydRom_V5.1_Exynos7420.zip
   2. Magisk-v21.4.zip (선택사항)
3. TWRP 메인 화면에서 **<span style="color:blue">파란색</span>** 박스 부분 **"Install"** 터치 후 FloydRom_V5.1_Exynos7420.zip 를 선택 합니다.
4. 아래 "Swipe to confrom Flash"를 스와이프 합니다.

### 3-8. Floyd ROM 옵션 선택
다음 부턴 Step-By-Step 으로 최대한 모든 장면을 촬영하였으며, 옵션 부분을 간단히 글로 번역 해 드리겠습니다. 본인이 필요한 옵션을 적절히 선택하여 주세요.

사진 상 선택 된 옵션은 제 옵션 입니다. 이는 **국내 갤럭시S6 SM-G920x용 옵션**임을 다시한번 강조 드립니다.   
해외 SM-G920x, SM-G925x, SM-G928x, SM-N920x의 경우 각 기기에 맞는 TWRP 이미지와 옵션을 선택하여야 합니다.

![](/assets/2021-02-16-Galaxy-S6-CustomFW/7.jpg)
- 간단한 롬에 관한 정보 입니다. Next를 터치

![](/assets/2021-02-16-Galaxy-S6-CustomFW/8.jpg)
- 롬 ChangeLog 입니다. Next를 터치

![](/assets/2021-02-16-Galaxy-S6-CustomFW/9.jpg)
- Wipe Option - 초기화 옵션
  - No를 선택 합니다. 필자의 글을 따라 한 경우 이미 Wipe가 되어있습니다.
- Bootanimation - 원하는 부트애니메이션 선택 옵션
  - 위 3개의 경우 커스텀 애니메이션 이며 저는 사용해본적이 없습니다.
  - Samsung White의 경우 하얀 화면에 Samsung 이라 검은글씨로 적히는 하얀 색상의 자급제 기기를 구매하면 볼수 있는 화면
  - Samsung Black의 경우 검은 화면에 Samsung 이라 하얀글씨로 적히는 검정 색상의 자급제 기기를 구매하면 볼수 있는 화면
- Other - 기타 옵션
  - 필자의 글을 따라 한 경우 이미 EFI가 백업 되어있으며, hosts 파일 역시 일반 사용자가 편집할 일이 없으므로 모두 체크 해제

![](/assets/2021-02-16-Galaxy-S6-CustomFW/10.jpg)
- SimCount - 유심 슬롯 개수 옵션
  - 한국 정발판은 듀얼심 모델이 없으므로 기본값 SingleSim을 선택
- Device Variant - 기기 특수 옵션
  - Korean Variants를 선택하여 G920S/L/K 용 파티션 테이블을 적용. 이를 체크 안할 경우 부팅이 불가능 합니다.
- S6 Flat Devices Only - 갤럭시S6 배터리 옵션
  - Skip - 갤럭시S6 이외 (엣지, 엣지+, 노트5) 기기 용
  - Stock Battery - 배터리 개조를 하지 않은 기기의 경우. 잘 모를경우 선택
  - Extended Battery - 갤럭시S7 엣지용 3600mAh 대용량 배터리로 개조한 경우 선택

![](/assets/2021-02-16-Galaxy-S6-CustomFW/11.jpg)
- 통신사 설정 옵션
  - INT/KOR 의 경우 국제판이나 자급제용 옵션입니다. 국내 자급제는 노트5만 존재합니다.
  - SKC - SKT 통신사 기능이 추가 (설정 및 기본 APN에 영향을 미치며, 통신사 앱은 없습니다.)
  - KTC - KT 통신사 기능이 추가
  - LUC - LGU 통신사 기능이 추가

![](/assets/2021-02-16-Galaxy-S6-CustomFW/12.jpg)
- Smart Manager - 잘 모르겠습니다.
  - 중국판이 아닌 International 판으로 선택하였음
- Wallpaper / Ringtone and other sounds - 배경화면 / 벨소리 및 기타 사운드
  - S10+ 9.0 펌웨어 기본 모음
  - Note8 9.0 펌웨어 기본 모음 (배경의 경우 Infinity 기능을 잘 몰라 S10+용을 사용하였음)
- Edge Features - 엣지 기능
  - Edge Lite - 엣지 라이트닝만 포함
  - Edge Full - 엣지 라이트닝 및 엣지 패널 포함
  - No Edge - 엣지 기능을 제거. (이후 엣지 관련 옵션 사용 불능)
- Other Features - 기타 기능. 번역이 필요한 부분만 작성
  - Yuva features - USB백업, S전원설정, S프로텍트, 울트라 데이터 세이버, 메모리 세이버, 보안 와이파이, 삼성 Max 기능 (모두 사용안하여 체크 안함)
  - AdAway - 시스템 앱으로 애드블록과 비슷한 광고 차단 앱을 설치
  - Youtube Vanced - 시스템 앱으로 Youtube Vanced라 불리우는 Modded 유튜브 앱 탑제 (Youtube Premium 일부 기능 기본 탑제)
  - Secure Folder - 보안 폴더
  - SamsungCovers - 삼성 스마트커버를 사용하는 경우 체크 (검정 화면에서 화면이 켜지지 않는 이슈 발생 가능)

![](/assets/2021-02-16-Galaxy-S6-CustomFW/13.jpg)
- Main Fonts - 기본 폰트
  - 안드로이드9와 10의 폰트가 서로 다릅니다. 한국어의 경우 동일 한 것으로 알고 있습니다.
- Emoji - 이모티콘
  - Samsung - 삼성
  - AOSP - 안드로이드 기본
  - iOS - iOS 기본 이모티콘 (또한 핸드폰이나 노트북등의 이모티콘이 애플 기기나 애플로고로 대체 됩니다.)
- Extra Fonts - 추가 폰트
  - Stock Samsung - 삼성 기본 폰트
  - Google - 구글 기본 폰트
  - 1120 - 뭔지 모릅니다.

![](/assets/2021-02-16-Galaxy-S6-CustomFW/14.jpg)
GoodLock 옵션들 입니다. 여기서 체크 시 데이터 영역이 아닌 시스템 영역에 설치 됩니다. 저는 GoodLock Manager만 시스템 영역에 설치 하였고, 나머지는 그냥 사용하고 있는 모듈을 체크해 보았습니다.

**※GoodLock 앱을 사용하기 위해선 최초 1회 Galaxy Store 에서 업데이트를 해주지 않으면 사용이 불가능 합니다.**  해외의 경우 GoodLock을 지원하지 않는 국가가 있어 업데이트 시 사용이 불가능해지고...구버전의 경우 삼성 내부 서버 업데이트로 인하여 사용이 불가능 하지만, 국내는 무관 합니다.

![](/assets/2021-02-16-Galaxy-S6-CustomFW/15.jpg)
한국 이외에 CSC 영역에 포함되는 특수 기능들 입니다. 필요하신 옵션을 선택 해 주시면 됩니다.
- Extra Toogles - 퀵셋팅 부가 토글들 인것 같습니다.
- Network speed in statusbar - 상단바에 실시간 네트워크 속도 아이콘을 표시
- Data icon in power menu - 전원 메뉴에 데이터 버튼 추가
- Call recording - 녹화 녹음
- Disable Anti-Malware in SmartManager/Device care - 국내 갤럭시 Mcafee 기능을 비활성화
- LTE only network mode - LTE만 사용 옵션을 추가. 일부 HD Voice를 지원하지 않는 별정 통신사의 경우 통화 시 3G를 사용하므로 체크 해제
- Data ysage in quick panel - 밝기 슬라이더 아래 데이터 사용량 표시기 추가
- LTE data icon - 체크 해제시 4G 아이콘으로 나옴
- Places tab in dialer - 기본 전화 앱에 Places 탭 추가. 국내 미사용
- Other CSC tweaks - 기타 CSC 기능들 추가. 무엇이 있는지 궁금하여 추가하였으나 차이를 모르겠음

![](/assets/2021-02-16-Galaxy-S6-CustomFW/16.jpg)
안드로이드10 기본 앱들을 설치하고 싶은 경우 선택 합니다. 체크를 안하셔도 안드로이드9 앱이 설치 됩니다.
- Clock - 시계
- Calendar - 달력
- Weather - 날씨
- Messagers - 메시지
- EdgePanels - Edge 기능을 사용한 경우 엣지 패널을 추가 합니다.
- Gallery - 갤러리
- SmartCapture - 스마트 캡처
  
![](/assets/2021-02-16-Galaxy-S6-CustomFW/16.jpg)
- Bixby Apps - 빅스비 앱
  - 빅스비홈 구버전 Samsung Daily
  - 빅스비 루틴
  - 빅스비 보이스 및 기상음
- Samsung Apps - 삼성 앱
  - 계산기
  - 듀얼 메신저 (카카오톡 등을 하나 더 만들어 줍니다.)
  - 이메일
  - 게임 런처
  - 게임 툴즈
  - 음악
  - 리마인더
  - 비디오
  - 비디오 리코더
  - 디지털 웰빙
- Other Apps - 기타 앱
  - ANT+ 기능 앱 (ANT 기기 없으면 체크 해제)
  - 구글 어시스턴트
  - 구글 크롬
  - Microsoft 윈도우와 연결 앱
  - 홈화면 1페이지 앱
  - 심툴킷 (뭔지 모르지만 국내 갤럭시 펌웨어에 존재)

![](/assets/2021-02-16-Galaxy-S6-CustomFW/16.jpg)
모든 것이 다 선택 되었다면 Install 버튼을 눌러 줍니다. 누를 경우 나오는 메시지는 제작자가 본 커스텀롬을 만드는 작업에 많은 시간과 정성을 투자하였음을 알려주는 내용 입니다. 만일 마음에 드신다면 커피 한잔 값을 도네이션 해주는 것도 좋습니다 :)

## 4. 첫 부팅
모든 과정이 성공적으로 완료 되었다면, Reboot 를 하여 줍니다. 첫 부팅시 Samsung 부트 애니메이션 위치가 깨지며, 상당히 많은 시간이 소요 됩니다.
중간에 종료하지 말고 계속 기다려주세요.

모든 과정이 완료되면 기존 사용하시는 갤럭시 처럼 사용하여 주시면 됩니다. 또한 Play 스토어와 Galaxy 스토어에서 모든 앱들을 다 최신 버전으로 업데이트를 해주셔야 정상적인 사용이 가능합니다.

또한 Floyd Note8 Port 롬에는 Root와 관련된 기능이 탑제되어있지 않습니다. 따라서 사용을 원하실 경우 Magisk 를 리커버리로 가셔서 Install 해주시면 됩니다.

이번에 미밴드5도 하나 받게 되어 배송중인데 어떤 부분이 다를까 기대 됩니다.

감사합니다 :)