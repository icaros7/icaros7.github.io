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
**본 글을 따라하는 것은 모두 스스로에게 책임이 있음을 미리 알려드립니다*
*

최종 업데이트 : 2021년 3월 29일
+ 게임툴즈(부스터) 관련 추가
+ 자잘한 번역 업데이트
+ 글 가독성 개선

저는 기본적으로 제 기본 회선에 아이폰Xs Max를 사용 중입니다. 그리고 투넘버 겸용(?) 부가 회선에 갤럭시 시리즈를 사용하여 지갑 처럼 같이 사용 하고 있습니다. 얼마전까지는 갤럭시S6을 사용하였는데 요전에 갤럭시A50으로 기기를 바꾸면서 S6은 이제 더이상 삼성페이에 의존하지 않을 수 있게 되었습니다.

저는 사용하며 Samsung Experience 8.0이 8.1이나 8.5와 다르게 매우 구버전이어서 사용이 불편하여 Modded 앱들을 사용할 정도로 불만족 이었습니다. 또한 게임 툴즈 역시 OneUI2.5와 다르게 완전히 화면이 꺼지지 않으며, GoodLock 기능이 없는 것, 보안폴더 내 앱이 Play framework에 접근이 불가능한 부분이 매우 불편하였으나 아래 과정을 통하여 갤럭시 노트8의 안드로이드 9.0 삼성 롬을 엑시노스7420 기기들에게 포팅시킨 롬 사용을 통하여 해결 하였습니다.

# 1. 득과 실
기본적으로 커스텀룸에 대한 이해 혹은 잘 모르는 분들을 기준으로 작성 합니다.

**본 글을 따라하시면 삼성전자의 보안 관련 기능과 메인보드 관련 A/S 진행이 <span style="color:red">불능</span>합니다.**   
**또한 모든 작업은 본 글을 따라하는 사용자에게 있으며, 복구 관련 문의는 받지 않습니다**

## 1-1. 득
- 삼성 갤럭시 노트8 9.0 펌웨어의 모든 기능 (OneUI 1.0 및 GoodLock 포함)
- CronosKernel (커스텀커널)을 통한 성능 및 배터리 최적화 (상당한 차이)

개인적으로 기대했던 사항은 아래와 같습니다.
- GoodLock 모듈 중 MultiStar를 통한 끊기지 않는 멀티 윈도우 기능
- 다크 모드
- Always On Display

## 1-2. 실
아래와 같은 기능들 사용이 불능 합니다. 꼭 읽어주세요.

- 삼성 페이
- 삼성 페스
- 삼성 헬스 (실행은 되는데 작동 중지 됩니다)
- Knox 일부 기능 (기본적으로 Knox Warranty가 깨집니다.)
- Knox 워런티 깨짐으로 인한 금융 앱 사용 불능 (우회 방법이 있겠지만 전 A50에서 사용 중이라 무관)

**※위 사항들은 한번 본 글을 따라하고 나면 다시 되돌릴 수 없음. 복구 원할 경우 삼성 A/S센터 내방하여 20만원 상당의 메인보드 교체 요구**

만약 저와 같은 것들을 포기하고 사용 하실 수 있으면서 제가 얻은 것들이 합리적이라고 생각 되신다면 좋은 팁이라 생각 됩니다.

## 1-3. Floyd Note8 Port V5.1 관련 버그 및 사용 불가 기능
아래와 같은 사항은 본 Floyd Note8 Port 롬의 버그 및 사용 불가 기능 입니다.

- 하드웨어 컴포저 (앱 렌더링 방식 중 하나이며, OneUI 소스가 공개가 안되어있어 사용이 불가능)
  - 일반적 사용자가 실사용 할 경우 체감은 안됨
- 카메라 관련 일부 사항
  - 직접 확인 결과 동영상 촬영은 별도 앱 필요
  - 사진의 경우 삼성 기본 카메라 앱 사용 가능 (자동 밝기 비활성시에만 사용 가능)
  - 서드파티 앱 (카카오톡, Whatsapp 등)에서 카메라를 실행하는 기능이 있는 경우 해당 기능 작동 유무 불확실
- S6 및 S6 Edge 의 경우 삼성 갤러리 앱 실행시 일시적 화면 깜빡거림
- 삼성 갤러리 앱 클라우드 동기화 시 사진이 제대로 표시되지 않음
  - 내장 사진의 경우 정상 표시
- 충전기 연결 및 연결 해제시 간혈적으로 와이파이 사용이 불능한 현상 발생
- 게임 툴즈(게임 부스터) 사용 불능 현상
  - 본 글 가장 하단 해결 법 별도 첨부

# 2. 준비물 및 환경 구성 안내
![](../assets/2021-02-16-Galaxy-S6-CustomFW/1.jpg)   
본 글은 아래와 같은 환경을 기준으로 작성 되었습니다.

- 한국 SKT 정발 갤럭시S6 (SM-G920S)
  - zeroflteskt 기준, KT SM-G920K, LGU SM-G920L 모두 가능
  - 해외 갤럭시S6, S6 Edge (SM-G925x), S6 Edge+ (SM-G928x), 노트5 (SM-N920x) 모두 가능, 하지만 본 글에선 S6을 기준으로 하므로 별도 파일 및 과정 필요
- 사용 유심 통신사 SKT
- 별도 배터리 개조 상태가 아닌 모든 것이 기본 상태
  - 무엇인지 모른다면 기본 상태
- Windows 10 랩톱

또한 필요한 것들을 설명 드리겠습니다. 다운로드 링크가 첨부 된 경우 제가 사용 한 파일들 입니다. 제가 신원을 보장 드리는 사이트는 아니므로 직접 구하셔도 무방합니다.

- PC와 데이터 통신이 가능한 USB 케이블
- Floyd Note8 Port V5.1 / <a href="https://forum.xda-developers.com/t/rom-9-0-oneui-n920x-g92x-floyd-note8-port-v5-1.3882804/" target="_blank"> ananjaser1211씨의 포스트</a>
  - 위 링크에서 두번째 쓰레드의 "Downloads" 부분 참조
  - 한국에선 GoogleDrive를 통하여 다운로드 권장
- 삼성 모바일 USB 드라이버 / <a href="https://developer.samsung.com/mobile/android-usb-driver.html" target="_blank">developer.samsung.com</a>
  - 상단의 "Samsung_USB_Driver~~~"라 적힌 둥근 버튼 클릭
- 모바일 오딘 3.12.3 / <a href="https://odindownload.com/download/#.YCttnZdxcow" target="_blank">odindownload.com</a>
  - "SamsungOdin 3.12.3" 를 검색 후 하단에 있는 Download Now 이미지를 클릭
- zeroflte(G920SLK) 용 TWRP 이미지 / <a href="https://dl.twrp.me/zeroflteskt/" target="_blank">twrp.me</a>
  - 가장 최신 버전의 "twrp-3.3.1-0-zeroflteskt.img.tar" 를 클릭
  - SKT, KT, LGU 공용 파일
  - 국내판 갤럭시S6 기준
- Magisk (선택사항) / <a href="https://magiskmanager.com/" target="_blank">magiskmanager.com</a>
  - "Then, Download the Magisk file from below"를 검색하신 다음 아래에 있는 "Download Magisk *.*"를 클릭
- 인터넷 사용 가능 환경 (약 2.6GB 정도 사용)
- 랩톱의 경우 충분한 배터리와 어느정도 충전이 완료된 갤럭시S6
- **모든 기기 내 내용물은 사전 백업 (공장 초기화 과정 필수)**

# 3. 설치 환경 준비
## 3-1. 기본 준비
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

# 4. 부트로더 언락 및 리커버리 사용
## 4-1. 커스텀 리커버리 TWRP 설치
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

## 4-2. 리커버리 부팅
위 과정이 완료가 되었다면 하늘색 화면에서 아직 멈추어 있을 것 입니다.

아래와 같은 과정을 통하여 리커버리로 부팅 합니다.

한번 과정을 전체를 다 본 후에 따라하여 주십시오.

1. **<u>볼륨 상 + 볼륨 하</u> + 홈 + 전원** 버튼을 3초동안 꾹 눌러 강제 재부팅 시킵니다.
2. 화면이 꺼지는 순간 3번을 따라합니다.
3. **<u>볼륨 상</u> + 홈 + 전원** 버튼을 Galaxy S6 로고가 없어질때 까지 꾹 누릅니다.

# 5. 데이터 관리
만일 삼성 기본 롬으로 간편하게 되돌아오고 싶다면 백업을 먼저 해야 합니다. 저는 시스템과 데이터 백업은 따로 하지 않았습니다. 하지만 Modem 파티션과 EFI 파티션 백업은 권장 드립니다.   
아래와 같은 화면이 성공적으로 나왔다면 TWRP 부팅에 성공 한 것 입니다.

![](/assets/2021-02-16-Galaxy-S6-CustomFW/5.jpg)

## 5-1. 백업
**<span style="color:green">초록색</span>** 박스 부분 Backup 을 터치하여 원하는 항목을 선택 합니다.

1. 만일 순정 롬으로 복구를 하고 싶다면 System, Data, Dalivik/ART Cache, Cache를 체크 한 뒤 아래 Swipe를 해줍니다.
2. 다시 Backup 으로 돌아가 Modem 과 EFI를 체크 한 뒤 Swipe를 하여 백업 합니다.
   1. EFI와 Modem 파티션은 위 순정 롬 백업과 별개로 한번에 하지 않는 것을 추천 드립니다.
3. PC와 USB로 연결하면 MTP가 기본적으로 동작 됩니다. 평소 하듯이 내부 저장소\TWRP 폴더를 통째로 PC 어딘가에 백업 해 둡니다.

3번은 아래 **3-6. 와이프** 단계에서 내장 SD카드 초기화를 원치 않으시면 안하셔도 무방합니다. 저는 혹시 모를 상황을 대비하여 언제나 클라우드 혹은 데이터 저장소에 백업 합니다.

## 5-2. 데이터 와이프
1. **<span style="color:red">빨간색</span>** 박스 부분 Wipe - Advanced Wipe를 들어옵니다.
2. 아래와 같은 항목을 체크 합니다. Internal Stroage 를 체크 시 내장 SD카드가 초기화 됩니다.
   1. Dalivik/ART Cache
   2. System
   3. Data
   4. Internal Stroage (내장 SD카드 초기화를 원치 않으시면 체크 해제)
      1. 순정롬에서 오시는 경우 추천 드립니다. (영향은 없지만 깨끗한 사용을 위함. 롬 제작자 역시 권장)
   5. Cache

# 6. Floyd ROM 설치
1. PC와 연결 합니다. MTP로 연결되니 내부 저장소 폴더로 이동하여 주세요.
2. 아래 두 파일을 원하는 위치로 복사 합니다.
   1. FloydRom_V5.1_Exynos7420.zip
   2. Magisk-v21.4.zip (선택사항)
3. TWRP 메인 화면에서 **<span style="color:blue">파란색</span>** 박스 부분 **"Install"** 터치 후 FloydRom_V5.1_Exynos7420.zip 를 선택 합니다.
4. 아래 "Swipe to confrom Flash"를 스와이프 합니다.

# 7. Floyd ROM 옵션 선택
다음 부턴 Step-By-Step 으로 최대한 모든 장면을 촬영하였으며, 옵션 부분을 간단히 글로 번역 해 드리겠습니다. 본인이 필요한 옵션을 적절히 선택하여 주세요.

사진 상 선택 된 옵션은 제 옵션 입니다. 이는 **국내 갤럭시S6 SM-G920x용 옵션**임을 다시한번 강조 드립니다.   
해외 SM-G920x, SM-G925x, SM-G928x, SM-N920x의 경우 각 기기에 맞는 TWRP 이미지와 옵션을 선택하여야 합니다.

## 7-1. Floyd 롬 정보 안내
![](/assets/2021-02-16-Galaxy-S6-CustomFW/7.jpg)
- 간단한 롬에 관한 정보 입니다. Next를 터치

## 7-2. Floyd 롬 변경 사항 안내
![](/assets/2021-02-16-Galaxy-S6-CustomFW/8.jpg)
- 롬 ChangeLog 입니다. Next를 터치

## 7-3. Floyd 롬 기본 옵션

![](/assets/2021-02-16-Galaxy-S6-CustomFW/9.jpg)
- Wipe Option - 초기화 옵션
  - No를 선택 합니다. 필자의 글을 따라 한 경우 이미 Wipe가 되어있습니다.
- Bootanimation - 원하는 부트애니메이션 선택 옵션
  - 위 3개의 경우 커스텀 애니메이션 이며 저는 사용해본적이 없습니다.
  - Samsung White의 경우 하얀 화면에 Samsung 이라 검은글씨로 적히는 하얀 색상의 자급제 기기를 구매하면 볼수 있는 화면
  - Samsung Black의 경우 검은 화면에 Samsung 이라 하얀글씨로 적히는 검정 색상의 자급제 기기를 구매하면 볼수 있는 화면
- Other - 기타 옵션
  - 필자의 글을 따라 한 경우 이미 EFI가 백업 되어있으며, hosts 파일 역시 일반 사용자가 편집할 일이 없으므로 모두 체크 해제

## 7-4. 기기 특성 선택
![](/assets/2021-02-16-Galaxy-S6-CustomFW/10.jpg)
- SimCount - 유심 슬롯 개수 옵션
  - 한국 정발판은 듀얼심 모델이 없으므로 기본값 SingleSim을 선택
- Device Variant - 기기 특수 옵션
  - Korean Variants를 선택하여 G920S/L/K 용 파티션 테이블을 적용. 이를 체크 안할 경우 부팅이 불가능
- S6 Flat Devices Only - 갤럭시S6 배터리 옵션
  - Skip - 갤럭시S6 이외 (엣지, 엣지+, 노트5) 기기 용
  - Stock Battery - 배터리 개조를 하지 않은 기기의 경우. **잘 모를경우 선택**
  - Extended Battery - 갤럭시S7 엣지용 3600mAh 대용량 배터리로 개조한 경우 선택
## 7-5. 통신사 관련 설정
![](/assets/2021-02-16-Galaxy-S6-CustomFW/11.jpg)
- 통신사 설정 옵션
  - INT/KOR 의 경우 국제판이나 자급제용 옵션
    - 국내 자급제는 노트5만 존재
  - SKC - SKT 사용 시 선택
  - KTC - KT 사용 시 선택
  - LUC - LGU 사용 시 선택

해당 사항을 통신사에 맞게 설정 하신다 한들 기본 앱은 설치되지 않으며, APN 등 시스템 구성에 사용 됩니다.

공기계로 사용하신다 한들 갤럭시S6의 경우 아무 통신사나 선택 하여야 합니다.

## 7-6. 기능 관련 설정
![](/assets/2021-02-16-Galaxy-S6-CustomFW/12.jpg)
- Smart Manager - 기기 관리 기능
  - International 판으로 선택
  - 중국의 경우 법률로 인하여 Play 서비스 사용이 불능하여 별도 옵션 존재
- Wallpaper / Ringtone and other sounds - 배경화면 / 벨소리 및 기타 사운드
  - S10+ 9.0 펌웨어 기본 모음
  - Note8 9.0 펌웨어 기본 모음
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

## 7-7. 폰트 설정
![](/assets/2021-02-16-Galaxy-S6-CustomFW/13.jpg)
- Main Fonts - 기본 폰트
  - 안드로이드9와 10의 폰트가 다름
  - 한국어의 경우 동일하여 아무 것이나 선택하여도 무방. 영어만 영향 받음
- Emoji - 이모티콘
  - Samsung - 삼성
  - AOSP - 안드로이드 기본
  - iOS - iOS 기본 이모티콘 (또한 핸드폰이나 노트북등의 이모티콘이 애플 기기나 애플로고로 대체)
- Extra Fonts - 추가 폰트
  - Stock Samsung - 삼성 기본 폰트
  - Google - 구글 기본 폰트
  - 1120 - 뭔지 모릅니다.

## 7-8. GoodLock 옵션
![](/assets/2021-02-16-Galaxy-S6-CustomFW/14.jpg)
GoodLock 옵션들 입니다. 여기서 체크 시 데이터 영역이 아닌 시스템 영역에 설치 됩니다. 저는 GoodLock Manager만 시스템 영역에 설치 하였고, 나머지는 그냥 사용하고 있는 모듈을 체크해 보았습니다.

**※GoodLock 앱을 사용하기 위해선 최초 1회 Galaxy Store 에서 업데이트를 해주지 않으면 사용이 불가능**

## 7-9. 기타 통신사 트윅
![](/assets/2021-02-16-Galaxy-S6-CustomFW/15.jpg)
한국 이외에 CSC 영역에 포함되는 특수 기능들 입니다. 필요하신 옵션을 선택 해 주시면 됩니다.

- Extra Toogles - 퀵셋팅 부가 토글들
  - 스펙트럼 토글 등 불필요 토글 추가
- Network speed in statusbar - 상단바에 실시간 네트워크 속도 아이콘을 표시
- Data icon in power menu - 전원 메뉴에 데이터 버튼 추가
- Call recording - 녹화 녹음
- Disable Anti-Malware in SmartManager/Device care - 국내 갤럭시 Mcafee 기능을 비활성화
- LTE only network mode - LTE만 사용 옵션을 추가
  - 국내에선 불필요
- Data ysage in quick panel - 밝기 슬라이더 아래 데이터 사용량 표시기 추가
- LTE data icon - 체크 해제시 4G 아이콘으로 나옴
- Places tab in dialer - 기본 전화 앱에 Places 탭 추가
  - 국내 미사용
- Other CSC tweaks - 기타 CSC 기능들 추가
  - 무엇이 있는지 궁금하여 추가하였으나 차이를 모르겠음

## 7-10. 안드로이드Q 앱
![](/assets/2021-02-16-Galaxy-S6-CustomFW/16.jpg)
안드로이드10 기본 앱들을 설치하고 싶은 경우 선택 합니다. 체크를 안하셔도 안드로이드9 앱이 설치 됩니다.

- Clock - 시계
- Calendar - 달력
- Weather - 날씨
- Messagers - 메시지
- EdgePanels - Edge 기능을 사용한 경우 엣지 패널을 추가 합니다.
- Gallery - 갤러리
- SmartCapture - 스마트 캡처

## 7-11. 삼성 기본 앱 
![](/assets/2021-02-16-Galaxy-S6-CustomFW/17.jpg)
- Bixby Apps - 빅스비 앱
  - 빅스비홈 구버전 Samsung Daily
  - 빅스비 루틴
  - 빅스비 보이스 및 기상음
- Samsung Apps - 삼성 앱
  - 계산기
  - 듀얼 메신저
    - 본 롬에 있는 듀얼 메신저의 경우 패치 버전이라 모든 앱 복제 가능
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

# 8. 롬 설치
![](/assets/2021-02-16-Galaxy-S6-CustomFW/16.jpg)
모든 것이 다 선택 되었다면 Install 버튼을 눌러 줍니다. 누를 경우 나오는 메시지는 제작자가 본 커스텀롬을 만드는 작업에 많은 시간과 정성을 투자하였음을 알려주는 내용 입니다. 만일 마음에 드신다면 커피 한잔 값을 도네이션 해주는 것도 좋습니다 :)

# 9. 첫 부팅
모든 과정이 성공적으로 완료 되었다면, Reboot 를 하여 줍니다. 첫 부팅시 Samsung 부트 애니메이션 위치가 깨지며, 상당히 많은 시간이 소요 됩니다.
중간에 종료하지 말고 계속 기다려주세요.

모든 과정이 완료되면 기존 사용하시는 갤럭시 처럼 사용하여 주시면 됩니다. 또한 Play 스토어와 Galaxy 스토어에서 모든 앱들을 다 최신 버전으로 업데이트를 해주셔야 정상적인 사용이 가능합니다.

또한 Floyd Note8 Port 롬에는 Root와 관련된 기능이 탑제되어있지 않습니다. 따라서 사용을 원하실 경우 첫 부팅 이후 Magisk 를 리커버리로 가셔서 Install 해주시면 됩니다.

# 10. 후기
AOD, 엣지 라이트닝부터 성능과 배터리 러닝타임까지. 전 모든 부분에서 기본 롬 대비 매우 만족하며 사용 중입니다. 혹시라도 추가로 전달 해드려야 할것 같은 부분이 생기면 글에 추가 해두겠습니다 :)

이번에 미밴드5도 하나 받게 되어 배송중인데 어떤 부분이 다를까 기대 됩니다.

감사합니다 :)

# 11. 추가 사항
## 11-1. 게임툴즈 (게임부스터, 게임옵티마이저) 사용 관련
해당 롬에 기본적으로 게임 툴즈를 설치 중에 체크를 하셔도 사용이 불능 합니다. 이는 기본 앱으로 탑제된 Game Optimizer 문제로서 별도의 게임 옵티마이저를 설치해 주셔야 사용이 가능 합니다.

아무 버전을 설치하시면 불가능하고, 아래 링크의 APK 또는 특정 버전을 설치 하신 뒤 Galaxy Store 에서 업데이트를 하시면 됩니다.

참고로 게임 툴즈를 사용하시려면 아래와 같은 요구사항이 있습니다.

- Floyd Note8 Port 설치 중 Game Tools 옵션 체크
- 네비게이션 바 (사용하지 않더라도 최초 1회 필요) (확인 필요)

### 11-1-1. 구성요소 다운로드 및 업데이트
- Samsung Game Optimizing Service 3.4.01.0
  - <a href="https://samsung-game-optimizing-service.kr.uptodown.com/android/download/3304090" target="_blank">UptoDown</a>
  - 특정 버전만 설치가 되는 듯 합니다. 3.4.01.0 버전 설치를 확인하여 제가 사용한 파일을 알려드리는 겁니다.
  - 찝찝하시면 따로 되는 버전을 직접 찾아 설치 하신 뒤 Galaxy Store 에서 업데이트를 하시면 됩니다.
- Game Tools는 Floyd 롬 설치 시 선택 하였다면 설치가 되어있습니다.
  - 선택을 안하신 경우 롬 재설치 과정을 통해 설치하여 주세요
  - 시스템 파일 관리가 가능 하신 경우 아래 경로에 Game Tools apk 가 있습니다. /system/priv-app에 규격에 맞게 넣어주세요
    - 롬파일/flod/apps/GameTools_Dream  
- Galaxy Store 에서 게임 툴즈 (게임 부스터로 업데이트), Game Optimizing Service 를 모두 업데이트

### 11-1-2. 사용 안내
모두 설치가 끝난 경우 게임을 실행하시면 아래 두 항목을 통해 사용이 가능 합니다.

1. 내비게이션 바가 설치된 경우
  - 내비바 좌우로 게임 부스터 관련 버튼이 추가 됩니다.
2. 내비게이션 바가 없는 경우
  - 게임 실행 후 상단바를 내리면 게임 부스터 관련 알림이 있습니다. 해당 알림 클릭 시 작동됩니다.
3. 내비게이셔 바가 없지만 알림도 안뜨는 경우
  - Galaxy Store 에서 게임 런처를 다운로드 후 게임 런처를 통해 실행해 봅니다.
  - 그럼에도 안뜨는 경우 아래 사항을 따라합니다.
    1. 롬 재설치를 통해 내비게이션바 활성화
    2. 설정 - 디스플레이 - 내비게이션바 - 제스처 사용 설정
    3. 롬 재설치를 통해 내비게이션바 비활성화
  - 시스템 파일 관리가 가능하다면 다음 절차를 통해 재설치 없이 가능
    1. Floyd 롬파일/floyd/apps/nav/framework-res__auto_generated_rro.apk 추출
    2. /system/vendor/overlay 에 덮어 쓰면 내비바 활성 (기존 파일 백업 필수)
    3. 설정 - 디스플레이 - 내비게이션바 - 제스처 사용 설정 후 기존 백업 파일로 복구