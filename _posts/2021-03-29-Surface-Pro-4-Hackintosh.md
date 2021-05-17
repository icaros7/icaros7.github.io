---
title:  "Surface Pro 4 Big Sur 해킨토시 무작정 따라 설치하기"
categories:
    - 해킨토시_Hackintosh
tags:
    - Surface
    - 서피스
    - Hackintosh
    - 무작정 따라하기
    - 커스텀맥
    - 빅서
    - macOS
toc: true
published: true
---
서피스 프로4에 해킨토시 설치 하기 및 실사용 후기!

체인지로그
- 2021년 3월 31일 07:30
    - 문제 해결 섹션 보강
    - 스크린샷 추가
- 2021년 3월 1일 13:40
    - 불필요 요소 제거
    - 글 다듬기
    - 문제 해결 글 분리

<hr>

안녕하세요. 이번 글은 다음과 같은 방향을 추구하거나 그러한 목표를 갖는 분들께 추천 드립니다.

- 기본적인 컴퓨터 사용 능력이 있음
- 해킨토시에 대해 잘 모르지만 써보고싶음
- 해킨토시에 대해 배워보고 싶음
- macOS 를 사용 경험이 있음

![](assets/2021-03-29-Surface-Pro-4-Hackintosh/12.png)

> **설치하게 된 계기는?**   

맥북은 이미 떠나보냈지만 XCode 사용할 일이 있어 급급히 해킨토시를 설치하게 되었습니다.

해킨토시의 경우 최대한 넓은 의미로 보자면 Apple 사에서 공식적으로 지원하지 않는 하드웨어에 macOS를 설치하는 행위 입니다.

맥북, 아이맥, 아이맥프로, 맥미니 등이 아닌 데스크톱이나 랩톱에 설치하는 것도 해킨토시이고, 구형 애플 기기에 지원되지 않는 최신 macOS를 설치할 경우도 해킨토시의 원리를 사용 합니다.

# 1. 해킨토시 설치 전 알아두면 좋은 점
> 본 글은 <span style="font-weight:bold; font-size:16pt;">일딴 따라해보기!</span> 를 추구하고 있습니다.

우선 macOS 해킨토시를 사용하게 될 경우 아래와 같은 사항을 알아두셔야 합니다.

- Secure Boot 비활성화로 인한 전원 켜짐 시 Unlock 표시
    - 부팅시 Surface 로고 상단에 빨간 잠물쇠가 표시
- Secure Boot 비활성화로 인한 BitLocker 사용 불능
- 터치스크린
    - 아직 macOS 기기 중 터치 가능 기기가 없음
    - 일부 랩톱의 경우 터치스크린을 트랙패드로 사용
- 카메라 (마이크는 가능)
- Wi-Fi / 블루투스
    - Surface 제품군 대부분이 Marvell 컨트롤러 사용
    - macOS 드라이버 부제로 사용 불능

그럼 `어찌 인터넷을 연결하나?` 저의 경우에는 별도의 유선 랜카드 혹은 아이폰 유선 테더링을 사용 합니다.

이런 사유로 인하여 해킨토시만을 설치하여 사용함은 불편함이 있습니다. 저의 경우에는 Windows 10과 듀얼 부팅 (맥과 윈도우를 동시 사용)하는 환경을 구성하였습니다.

또한 **macOS 설치 과정은 사실 매우 짧고 간편**합니다. 하지만 대부분이 Windows 와 듀얼 부팅을 위한 글 입니다.

# 2. 구성 환경 안내 및 다운로드
## 2-1. 정의
아래 단어 및 정의는 글에서 앞으로 계속 사용되는 단어들입니다. 미리 이런 뜻이구나 혹은 줄여쓰는 구나를 알아주시면 감사하겠습니다.

|단어|정의|비고|
|---|---|---|
|서피스|Surface Pro 4|모든 글은 Surface Pro 4를 기준으로 설명|
|macOS|해킨토시로 설치될 macOS||
|Windows|Windows 10 Pro|Surface Pro에 포함된 Win 10 Pro 사용|
|USB|해킨토시 설치용 USB|이 USB는 계속 사용|
|특정 위치|준비물을 다운로드 받은 폴더|개개인이 원하시는 곳|
|별도 환경|Surface 이외 PC|데스크톱, 랩톱등 모두 가능|
|`이런 텍스트`|메뉴 및 파일/폴더명|눌러야하거나 사용되는 파일/폴더가 저런 식으로 표시

## 2-2. 구성 환경 안내
1. 필요한 것들
    - Python 3 환경이 구성된 Surface 이외 인터넷 가능 PC
        - Python 환경 구성은 아래 다운로드에서도 가능
        - 본 글은 `Windows 10 Pro 64비트 환경`에서 진행
    - Surface를 인터넷에 연결할 방법
        - 필자의 경우 별도 USB 랜카드가 존재하여 사용
        - 본 글을 따라한다면 대부분의 유선 `Realtek` 드라이버는 존재
    - 1GB 이상의 빈 USB
        - 내부 파일이 모두 초기화. 백업 필수
    - Surface 내 모든 자료 외부로 백업
    - **시간 / 인내심 / 구글 검색 능력 / 간단한 영어**
2. 구성 환경
    - `OpenCore`를 통한 macOS 설치
    - `Windows` 설치
    - `rEFInd`를 통한 듀얼 부팅

다음 과정은 두가지 길로 나누어 집니다. 하난 본인의 OpenCore를 구성하여 사용 하는 것이고, 다른 하나는 <a href="https://github.com/bigsadan/surface-pro-4-hackintosh" target="_blank">@bigsadan</a>씨의 이미 구성된 OpenCore 혹은 Clover 를 사용 하는 것 입니다.

본인의 OpenCore 구성의 경우 <a href="https://dortania.github.io/OpenCore-Install-Guide/" target="_blank">OpenCore 설치 가이드</a>를 참조하시면 구성이 가능 합니다. 또한 자세한 설명이 되어 있습니다. 본 글에서는 무작정 따라하는 해킨토시 Big Sur 설치를 목표로 @bigsadan씨의 구성된 파일을 사용하겠습니다. 일부 스크린샷의 경우 제가 구성한 OpenCore 임으로 다를 수 있으나 같은 결과가 나옵니다.

<span style="color:red; font-size:16pt">본 글은 모든 데이터를 지우고 클린 설치를 목표로 합니다. 자료 백업 필수!</span>

## 2-3. 파일 다운로드
그런 다음 필요한 파일을 준비해 보겠습니다.

1. <a href="https://www.python.org/downloads/" target="_blank">Python 3</a>
    - 이미 설치 되어있다면 재설치 불필요
    - 무엇인지 모른다면 위 링크로 들어가 <span style="color:yello">노란색 Download Python 3.x.x 버튼</span> 클릭 하여 다운로드 후 설치
2. <a href="https://github.com/bigsadan/surface-pro-4-hackintosh" target="_blank">**@bigsadan**씨의 해킨토시 구성파일</a>
    - 위 링크로 들어가 <span style="color:green">초록색 Code 버튼</span>을 눌러주신 뒤 `Download ZIP` 을 눌러 **특정 위치**에 저장
3. <a href="https://github.com/corpnewt/GenSMBIOS" target="_blank">**@corpnewt**씨의 GenSMBIOS</a>
    - 위 링크로 들어가 <span style="color:green">초록색 Code 버튼</span>을 눌러주신 뒤 `Download ZIP` 을 눌러 **특정 위치**에 저장
4. <a href="https://github.com/acidanthera/OpenCorePkg/releases/" target="_blank">OpenCorePkg</a>
    - 위 링크로 들어가 가장 최신 글 하단의 `OpenCore-x.x.x-RELEASE.zip`을 **특정 위치**에 저장
5. <a href="https://rufus.ie/" target="_blank">Rufus</a>
    - 위 링크로 들어가 하단 `Rufus x.xx 포터블`을 받아 바로 실행 가능
6. <a href="https://www.microsoft.com/ko-kr/software-download/windows10" target="_blank">Windows 10 Pro ISO 파일</a>
    - 이는 서피스 이외 **별도 환경**에 저장하길 권장
    - **Windows** 설치를 위해 ISO를 필요
    - 별도의 방법을 통해 ISO 파일 혹은 설치 wim 파일등을 받으시면 됩니다.
    - 최신의 **Windows** 설치를 할 경우 아래 USB 포맷 과정에서 꼭 `Large FAT32`로 포맷
    - 혹은 swm 파일로 분할 설치 <a href="https://prolite.tistory.com/1023" target="_blank">여기</a> 참조

위 파일을 모두 다운로드 받으셨다면 본격적으로 설치 과정을 시작해 보겠습니다.

# 3. **macOS** 리커버리 다운로드
3번에서 받은 파일 중 4번째 OpenCorePkg 폴더가 기준 입니다.

1. `Utilites 폴더` 내 `macrecovery 폴더`로 이동
2. Shift 키를 누른채 폴더 빈 공간을 마우스 오른쪽 클릭
3. 메뉴 중 `여기서 Powershell 창 열기` 클릭
4. 아래 명령어 입력

        cmd
        python macrecovery.py -b Mac-E43C1C25D4880AD6 -m 00000000000000000 download

5. 대략 600MB가 넘는 파일 다운로드
6. 같은 폴더 내에 `BaseSystem.dmg` 및 `BaseSystem.chunklist` 가 받아졌다면 성공

# 4. **USB** 및 디스크 구성
## 4-1. **USB** 구성
EFI 시스템에 사용되는 USB는 아래 두가지 조건을 만족하여야 합니다.

- 파티션 구조가 GPT 일것
- 포맷이 FAT32 (Large 포함) 일것

대부분의 **USB**는 해당 조건을 만족하지 못하므로 Rufus 라는 프로그램을 통해 포맷을 통해 설정해주어야 합니다. 이 과정에서 *USB 내 모든 자료가 지워집니다.*

위에서 받은 `Rufus 앱`을 실행하고 **USB**를 연결 한 뒤 아래와 같은 설정으로 맞춥니다.

![](https://dortania.github.io/OpenCore-Install-Guide/assets/img/format-usb-rufus.43feba9e.png)   
- 부트 선택을 `부팅 할 수 없음` 으로 선택
- 파티션 방식을 `GPT`로 선택
- 파일 시스템을 `"FAT32" 혹은 "대용량 FAT32"` 선택

선택을 다 하셨다면 `시작` 버튼을 눌러 포맷 합니다.

## 4-2. BitLocker 끄기
OpenCore 구성에 의해 BitLocker가 켜져있는 경우 디스크 파티션 변경이 불가능 합니다. 따라서 시작 전 **서피스**의 내 PC에서 C드라이브 아이콘에 잠물쇠가 있는 경우 아래 지침을 따라 BitLocker를 꺼주셔야 합니다.

자세한 내용은 <a href="https://www.samsungsvc.co.kr/solution/25356" target="_blank">삼성전자서비스 Windows10, 장치 암호화 (bitlocker) 해제 방법</a> 문서를 참고하시면 좋을 듯 합니다.

# 5. OpenCore 부트로더 구성 및 파일 복사

1. **@bigsadan**씨의 해킨토시 구성 파일 내 `11.x 폴더`로 이동
2. 모든 파일을 선택 및 복사
3. **USB** 최상위에 복사
4. **USB**에 `com.apple.recovery.boot` 라는 이름의 폴더를 생성
5. 과정3에서 받은 `BaseSystem.dmg` 및 `BaseSystem.chunklist`를 **USB** 내 `com.apple.recovery.boot 폴더` 내에 복사
6. 모든 과정을 마치고 나면 **USB** 구조가 아래 형태   
    ![](assets/2021-03-29-Surface-Pro-4-Hackintosh/5.png)

{% include ad_content.html %}

<hr>

# 6. OpenCore 설정 파일 개인화
3번에서 받은 파일 중 3번째 `GenSMBIOS` 를 사용하여 OpenCore 설정 파일을 개인화 합니다.

## 6-1. 모델 식별자 및 UUID 변경
1. 우선 `GenSMBIOS.bat` 을 실행
    - 아래와 같은 창이 나온다면 "더보기"를 누르면 실행 버튼 표시   
    ![](assets/2021-03-29-Surface-Pro-4-Hackintosh/1.png)
2. 2번 `Select config.plist` 를 선택하기 위해 `2`를 입력 후 엔터
3. **USB**의 `EFI/OC 폴더` 안 `config.plist` 파일을 명령 프롬프트로 드래그앤드롭
4. 엔터를 눌러 `config.plist` 를 선택 한 뒤 메인 메뉴로 복귀
5. 3번 Generate SMBIOS를 선택하기 위해 `3`을 입력 후 엔터
6. 아래 표를 참고하여 원하는 기종의 모델 인식자 번호를 입력

|인식자번호|CPU 타입|GPU 타입|설명|
|---|---|---|---|
|`MacBook9,1`|7w 듀얼코어|HD 515|맥북 기본형 모델|
|`MacBookPro13,1`|15w 듀얼코어|Iris 540|13인치 맥북프로 기본형 모델|
|`MacBookPro13,2`|15w 듀얼코어|Iris 550|13인치 맥북프로 고급형 모델|
|`MacBookPro13,3`|45w 쿼드코어|HD 530 + 라데온Pro 450|15인치 맥북 프로|

필자는 현실적으로 가장 서피스 프로4에 가까운 맥북 프로 13인치 기본형 모델인 `MacBookPro13,1`을 선택   
![](assets/2021-03-29-Surface-Pro-4-Hackintosh/2.png)

이는 자동으로 `config.plist`에 자동으로 값이 입력되었으므로 따로 신경쓰지 않아도 됩니다.

7. 4번 Generate UUID를 선택하기 위해 `4`를 입력 후 엔터

## 6-2. 기본 언어 한국어로 변경
기본적으로 **@bigsadan**씨의 `config.plist`는 러시아어 입니다. 이를 한국어로 바꾸기 위해 간단히 메모장 등으로 수정 필요합니다. 물론 이 과정이 없어도 대충 감으로 러시아어 사이를 해치며 언어 선택란에 들어가 한국어를 선택하여도 괜찮지만 멀끔한 화면을 위해 한국어로 변경해 줍니다.

1. 언어를 한국어로 설정 하기 위해 `config.plist`를 메모장 등으로 열기
    - `config.plist` 파일을 다른 앱으로 열기 메뉴 활용 혹은 메모장 파일 열기 창에서 아래와 같이 모든 파일 선택   
    ![](assets/2021-03-29-Surface-Pro-4-Hackintosh/3.png)
2. `prev-lang:kbd` 를 검색 한 뒤 아래줄을 다음과 같이 수정

        기존
        <key>prev-lang:kbd</key>
		<data>cnUtUlU6MjUy</data>

        - 두번째 줄 data -> string 으로 변경
        - ko:0 으로 값 변경

        수정
        <key>prev-lang:kbd</key>
		<string>ko:0</string>
        

# 7. Surface UEFI 설정 변경
이제 사용자가 준비할 준비물은 모두 준비가 되었습니다. 다음으로 필요한 것은 UEFI 설정 변경 입니다.

우선 **USB**를 최우선적으로 부팅 과정에서 인식하도록 설정을 해야 합니다. 또한 Secure Boot 를 해제하여 OpenCore 부트로더를 사용 가능하게 변경 해야 합니다.

UEFI 부팅 방법, 설정 등에 관한 자세한 내용은 <a href="https://support.microsoft.com/ko-kr/surface/surface-uefi%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-df2c8942-dfa0-859d-4394-95f45eb1c3f9" target="_blank">Microsoft Surface UEFI를 사용하는 방법</a> 문서에 자세히 나와있어 같이 읽어주시면 좋겠습니다.

1. UEFI로 부팅 (위 Microsoft 문서 참조)
2. Security(보안) 탭으로 이동하여 Secure Boot란 하단에 `Change Configuration`을 선택 후 `None` 선택
3. Boot Configuration(부팅 구성) 탭으로 이동해 `USB Storage`를 드래그앤드롭하여 가장 위로 이동
4. Exit(끝내기) 탭으로 이동하여 `Restart now` 선택

# 8. OpenCore 부팅
**서피스**를 재시작 할 경우 검은 화면에 하얀글씨가 나오며 OpenCore 화면을 볼 수 있습니다.

        OpenCore Boot Menu

        1. macOS Installer (external)
        2. Reset NVRAM

위와 비슷한 메뉴가 나오며 이중 2번을 선택시 **USB** 내용물 중 일부가 지워지므로 주의

1. `macOS Installer (external)`를 방향키 혹은 숫자키를 통해 선택
2. 잠시 기다리면 macOS 복구 화면으로 부팅 성공

<hr>
<span style="font-size:14pt;font-weight:bold;">문제 해결</span>

- <a href="https://minnote.net/%ED%8C%81_tip/SP4-macOS-Troubleshooting//#_WinHiber" target="_blank">`Windows is hibernate` 해결하기</a>
- <a href="https://minnote.net/%ED%8C%81_tip/SP4-macOS-Troubleshooting//#_normal" target="_blank">그 외 기타 부팅 오류</a>

# 9. 디스크 유틸리티를 통한 디스크 조정

1. 리커버리 메뉴 중 `디스크 유틸리티` 실행   
![](https://support.apple.com/library/content/dam/edam/applecare/images/ko_KR/macos/Big-Sur/macos-big-sur-recovery-disk.jpg)
2. 아무 파티션을 선택한 뒤 우측 상단 `지우기` 버튼 클릭 하여 디스크 전체 초기화
![](https://support.apple.com/library/content/dam/edam/applecare/images/ko_KR/macos/Big-Sur/macos-big-sur-recovery-mode-disk-utility-container-disc.jpg)
3. OpenCore 구성에 의해 디스크 파티션을 초기화 하고 난 후면 재시작 필요
    - 따라서 `전원 + 볼륨Up + 볼륨Dn`을 5초이상 길게 누르거나 좌측 상단 시스템 재시동 이용
4. `디스크 유틸리티`를 다시 실행
5. 우측 상단 `파티션` 버튼을 사용하여 원하는 파티션 조절
    - **macOS**를 설치할 파티션은 APFS 로 포맷
    - **Windows**를 설치할 파티션 혹은 파일 저장 파티션은 MS-DOS FAT으로 포맷
    - 파티션 구성을 안할 경우 SSD 전체를 **macOS**가 사용하여 **Windows** 설치가 불능
6. 디스크 파티셔닝이 끝났다면 `디스크 유틸리티`를 종료


<hr>
<span style="font-size:14pt;font-weight:bold;">문제 해결</span>

- <a href="https://minnote.net/%ED%8C%81_tip/SP4-macOS-Troubleshooting//#_diskutil" target="_blank">디스크 지우기 혹은 파티셔닝 실행 시 강제로 꺼짐</a>)

# 10. macOS Big Sur 설치 및 설정
## 10-1. 온라인으로부터 설치
![](https://support.apple.com/library/content/dam/edam/applecare/images/ko_KR/macos/Big-Sur/macos-big-sur-recovery-disk.jpg)   
드디어 macOS를 설치할 차례 입니다.

위 항목에서 `macOS Big Sur 다시 설치` 를 누른 뒤 APFS로 포맷했던 디스크를 선택하여 다음을 눌러주시면 인터넷에서 macOS Big Sur를 스스로 다운로드하여 설치를 진행 합니다.

이 과정은 약 30-60분 정도 소요됩니다.

## 10-2. **macOS** 설정
모든 과정이 끝났다면 다시시작을 하면 OpenCore 메뉴가 아래와 같이 변했을 것 입니다.

        OpenCore Boot Menu

        1. macOS Installer (external)
        2. macOS
        3. Reset NVRAM

이 중 2번을 선택하면 **macOS**로 부팅되며, 리얼맥과 동일하게 초기 설정을 진행하여 주시면 됩니다.

또한 혹시라도 **Windows**가 필요 없으시다면 [12. EFI 파티션 관리](#_efi) 부분의 macOS 에서 EFI 파티션을 마운트하시고 OpenCore 설치만 하시면 됩니다.

# 11. **Windows** 설치 및 설정
## <a id="_11_1"></a>11-1. **Windows** 설치 준비
Windows 10 Pro 설치 파일의 경우 위에서 받은 Windows 10 Pro ISO 파일 내부에 있는 다음과 같은 폴더 및 파일들 입니다. ISO 파일 내부에는 더 많은 파일들이 있지만 전체 복사를 하셔도 무방하고, 아래와 같은 파일만 복사하셔도 무방합니다.   
![](/assets/2021-03-29-Surface-Pro-4-Hackintosh/10.png)

1. **USB**를 **별도 환경**에 연결
2. `_macOS` 폴더 생성 후 모든 파일 이동
3. **Windows** 설치 파일 (혹은 _Windows 폴더 내부 파일)을 모두 **USB** 최상단에 복사
4. 최종적으로 아래와 같은 구조가 탄생
    ![](/assets/2021-03-29-Surface-Pro-4-Hackintosh/8.png)
5. 일반적인 과정을 통해 **Windows** 설치

참고로 위 과정에서 받은 **Windows** 이미지는 모든 PC에 공통으로 들어가는 파일 입니다. 따라서 Surface 관련 요소가 탑제되어있지 않습니다. 시스템 정보에 나오는 Surface 로고도 없어지며 인터넷 연결 없이는 Surface 관련 드라이버도 설치되어있지 않습니다. 인터넷 연결 상태라면 드라이버는 당연히 자동으로 설치 됩니다.

이를 원치 않는 경우 <a href="https://support.microsoft.com/ko-kr/surface-recovery-image?ui=ko-KR&rs=ko-KR&ad=KR" target="_blank">Microsoft Surface 복구 이미지 다운로드 문서</a>를 참조하여 Surface 용 이미지를 받아야 합니다. 하지만 이는 대부분 구버전 **Windows** 이므로 업데이트 하는 시간이 소요되어 저는 잘 사용하지 않습니다.

설치가 완료되었다면 이제 그냥 **서피스**를 전원을 켠 후 **Windows**으로 부팅되며 **USB**를 연결할 경우 OpenCore가 로딩되어 **macOS**로 부팅이 가능 합니다.

하지만 언제까지나 **USB**를 계속 꽂아둘수는 없으므로 듀얼 부팅 환경을 구성하겠습니다.


# <a id="_efi"></a>12. EFI 파티션 관리
## 12-1. rEFInd 다운로드
<a href="https://www.rodsbooks.com/refind/" target="_blank">rEFInd Boot Manager 홈페이지</a>에서 최신버전의 `binary ZIP`을 받아주시면 됩니다.

페이지 구조가 어지러워 잘 모르겠다면...

1. 목차 중 <a href="https://www.rodsbooks.com/refind/getting.html" target="_blank">`Getting rEFInd`</a> 클릭
2. `A binary zip file` 클릭

적절한 위치에 압축을 풀어 둡니다.

## 12-2. EFI 파티션 마운트
### 12-2-1. **macOS**에서 마운트
`EFI Mounter` 같은 서드파티 앱을 다운로드 하시거나 아래 명령어들을 통해 수동으로 가능합니다.

1. `터미널` 실행
2. 아래 코드 입력

        diskutil list
3. TYPE이 `EFI`인 200+@MB 가량의 파티션 찾기
4. 해당 파티션의 `IDENTIFIER` 외우기
5. 아래 코드 입력
    - `disk0s1`이라 가정

        sudo mkdir /Volumes/EFI
        sudo mount -t msdos /dev/disk0s1 /Volumes/EFI
6. 이후 Finder 에서 `무제` 혹은 `SYSTEM` 이라는 항목으로 접근 가능   
![](assets/2021-03-29-Surface-Pro-4-Hackintosh/4.png)

### <a id="_12_2_2"></a>12-2-2. **Windows**에서 마운트
`EFI Mounter` 같은 서드파티 앱을 다운로드 하시거나 아래 명령어들을 통해 수동으로 가능합니다.

1. `윈도우키 + X`를 누름
2. `Windows Powershell (관리자)` 실행
3. 아래와 같은 코드 입력

        cmd
        diskpart
        list disk
4. 출력값 중 본인의 **서피스** 디스크 용량과 같은 디스크 번호 기억
    - 본인 모델이 256GB라면 256GB에 가장 근접한 디스크를 선택
5. 아래 명령어 입력
    - 디스크0 이라 가정

        sel disk 0
        list part
6. 이중 `System` 파티션 번호 기억
7. 아래 명령어 입력
    - 파티션1이라 가정

        sel part 1
        assign letter=S
        exit
8. 이후 S드라이브에 EFI가 마운트 됩니다.

**Windows**에서 하시는 경우 EFI 드라이브에 접근하기 위해선 **관리자 권한**으로 실행된 앱만 가능 합니다. Windows 탐색기는 이상하게 관리자 권한으로 실행하여도 작동되지 않습니다. 저는 그래서 그림판이나 메모장을 주로 이용 합니다. 

1. 작업관리자 실행
2. 상단 파일 메뉴
3. 실행
4. `mspaint` 혹은 `notepad` 입력
5. 하단 `관리자 권한으로 실행` 체크 후 실행

위와 같은 과정을 거치신 다음 `파일 열기` 등 파일 탐색이 가능한 다이얼로그를 띄우시면 거기서 EFI 파티션 접근이 가능합니다.

## 12-3. rEFInd 설치

1. EFI 파티션 폴더에 `refind`라는 폴더 생성
2. 11-1에서 받고 압축 푼 폴더 내 `refind` 폴더로 이동
3. `icons` 폴더, `refind_x64` 파일 및 `refind.conf-sample` 파일 EFI 파티션 내 `refind` 폴더에 복사
4. `refind.conf-sample` 파일 이름을 `refind.conf`로 변경
5. EFI 파티션 구조가 아래와 같아야함
    - APPLE 폴더가 존재한다면 삭제   
    ![](assets/2021-03-29-Surface-Pro-4-Hackintosh/6.png)

## 12-4. 시스템 EFI에 OpenCore 설치

**USB** 내 OC 폴더를 EFI 파티션 내 복사   
![](assets/2021-03-29-Surface-Pro-4-Hackintosh/11.png)   
(위 사진은 필자 상태. 무조건 같지 않아도 됩니다. 중요한 것은 OC 폴더)


## 12-5. rEFInd 테마 및 설정
자세히 다루지는 않겠습니다만, `refind.conf`를 텍스트 편집기로 열어보시면 아래와 같은 값들이 있습니다.

        # 뭐라뭐라
        #enable_touch

        # 뭐라뭐라
        #enable_mouse

위 값 중 `#enable_~~`의 앞에 `#`를 지워주시면 터치와 마우스 사용이 가능해집니다. **macOS**에서 사용 가능한것이 아닌 rEFInd 에서 활성 입니다.

또한 테마도 지원하여 여러가지 이쁜 테마들이 존재합니다. 이는 제가 직접 다루지 않겠습니다.

필자는 <a href="https://github.com/bobafetthotmail/refind-theme-regular" target="_blank">**@bobafetthotmail**씨의 refular 테마</a>를 아이콘 및 폰트 크기를 가장 크게해서 다크모드로 사용 중 입니다.

# 13. rEFInd 사용 설정
저는 `Windows Boot Manager`를 기반으로 구성하였습니다. 이는 제 방법이며 이외에도 여러가지가 있지만 저를 따라 구성하시는 분들은 그냥 따라하시면 충분히 가능합니다!

이는 `Windows Boot Manager`를 수정해야하므로 Windows 환경으로 부팅하여 따라해주시면 됩니다.

1. `윈도우키 + X`를 누름
2. `Windows Powershell (관리자)` 실행
3. 아래 명령어 입력
    - 본인의 EFI 폴더를 대문자로 만들었다면 대문자로 바꿔서 입력


        cmd
        bcdedit /set {bootmgr} path \efi\refind\refind_X64.efi

이제 모든 구성이 완료되었습니다.

# 14. 글을 마치며
위와 같은 과정을 거치고 나면 저와 완벽히 동일한 구성이 되었습니다. **Windows 10 Pro** 와 **macOS Big Sur**를 듀얼부팅 가능한 상태 입니다.

나름 시간이 상당히 걸리기도 하였고 무엇이든지...

> **나빼고 모두 문제가 없지만 나만 오류가 나는 현상**

이때문에 검색에 의존하기도 사실 약간 어렵습니다.

그래서 최대한 제 환경과 동일하게 만들기 위해 이해보단 직접 무작정 따라하는 강좌를 만들게 되었습니다. 시간이 되시거나 관심이 있으시다면 이게 왜 그렇게 동작하는지를 조금 더 유심히 봐주시면 좋을 것 같습니다.

또한 이 하단 부터는 진행과정에서 해당되지 않던 사소한 팁들 입니다.

<hr>
<span style="font-size:14pt;font-weight:bold;">문제 해결</span>

- <a href="https://minnote.net/%ED%8C%81_tip/SP4-macOS-Troubleshooting//#_raw" target="_blank">**Windows** 부팅 실패 및 파란 Recovery</a>

# 15. 사소한 팁
## 15-1. OpenCore 기본 항목 정하기
제가 사용한 @bigsadan씨의 파일을 사용하였다면 `OpenCore.config` 파일 내 `MISC -> Security -> AllowSetDefault` 항목이 `true`로 되어있을 것 입니다. 이 항목을 사용한다면 OpenCore Boot Menu 화면에서 본인이 원하는 항목에 상하 방향키를 이용하여 커서를 두신 다음 `Ctrl + Enter` 키를 누르면 해당 항목이 기본 값이 됩니다.

## 15-2. OpenCore Reset NVRAM 없애기
`OpenCore.config` 파일 내 `MICS -> Security -> AllowNvramReset` 항목을 `false`로 하시면 Reset NVRAM 항목이 없어집니다.

## 15-3. ProperTree 로 plist 편하게 편집
<a href="https://github.com/corpnewt/ProperTree" target="_blank">@corpnewt씨의 ProperTree</a> 앱을 사용하신다면 무척 편리한 GUI plist 편집기 사용이 가능합니다. Python 기반이라 Python 설치가 필요합니다. 또한 Python이 설치가 가능한 환경이면 모두 가능합니다.   
![](assets/2021-03-29-Surface-Pro-4-Hackintosh/7.png)

이 편한 것을 왜 안알려주었냐...라고 하시면 설치 과정에서 **@bigsadan**씨의 파일을 이용하시며 실제로 수정할 것이 별로 없어 그냥 메모장으로 하였습니다.