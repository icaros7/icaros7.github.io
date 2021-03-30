---
title:  "Surface Pro 4 Big Sur 해킨토시 따라 설치하기"
categories:
    - 팁_Tip
tags:
    - Surface
    - 서피스
    - Hackintosh
    - 해킨토시
    - 커스텀맥
    - 초보
    - 카탈리나
    - 모하비
    - 빅서
    - 요세미티
toc: true
published: true
---
서피스 프로4에 해킨토시 설치 하기 및 실사용 후기!

**서론이 길다 느끼신 다면 상단 우측 원하시는 목차로 바로 이동하세요! 다운로드 과정 부터 시작 하시려면 2-2번!**

안녕하세요! 이번에 X코드 쓸일이 있어서 해킨토시를 설치하게 되었습니다. 진짜 맥북이 있었을 때에는 무거워서 찬밥 신세라 금방 팔아버렸지만 꼭 없으면 아쉽습니다.

힌지가 있으면서 가볍게 쓸 수 있는 M1 맥북 에어를 구매하고 싶지만 아직 썬더볼트가 2포트 모델 밖에 없기에 구매를 미루고 있습니다.

그러면 대체로 잠시 해킨토시의 힘을 빌려 봅시다.

해킨토시의 경우 최대한 넓은 의미로 보자면 Apple 사에서 공식적으로 지원하지 않는 하드웨어에 macOS를 설치하는 행위 입니다.

맥북, 아이맥, 아이맥프로, 맥미니 등이 아닌 데스크톱이나 랩톱에 설치하는 것도 해킨토시이고, 구형 애플 기기에 지원되지 않는 최신 macOS를 설치할 경우도 해킨토시의 원리를 사용 합니다.

# 1. 해킨토시 설치 전 알아두면 좋은 점
> 해킨토시에 관해 잘 아시는 분들은 이 문서를 읽지 않을 것이고...   
잘 모르시는 분들이 ***무작정 따라하기 좋은 글*** 을 만들기 위해 원리나 그런 것들은 집어 치우고 본론만 적겠습니다.

참고로 아무리 해킨토시에 관하여 잘 모르더라도 기본적인 컴퓨터 혹은 운영체제에 관한 지식은 있어야 합니다.

- Secure Boot 비활성화로 인한 전원 켜짐 시 Unlock 표시
- Secure Boot 비활성화로 인한 BitLocker 사용 불능
- 터치스크린
- 카메라 (마이크는 가능)
- Wi-Fi
- 블루투스

터치스크린의 경우 macOS 기기 중 터치가 되는 기기가 아직 없어 터치 스크린 드라이버가 없습니다. 몇몇 터치 가능 랩톱의 경우 터치 스크린 드라이버를 트랙패드로 잡아 트랙패드 처럼 사용하는 사례가 있습니다.

와이파이 및 블루투스의 경우 서피스 제품군은 몇몇 제품을 제외하고 모두 Marvell 컨트롤러를 사용합니다. 이는 macOS에서 지원하지 않는 컨트롤러라 별도의 드라이버가 요구되지만 이 역시도 드라이버의 미지원으로 인해 사용이 불능합니다.

그럼 어찌 인터넷을 연결하나? 저의 경우에는 별도의 유선 랜카드 혹은 아이폰 유선 테더링을 사용 합니다.

이런 사유로 인하여 해킨토시만을 설치하여 사용함은 불편함이 있습니다. 저의 경우에는 Windows 10과 듀얼 부팅 (맥과 윈도우를 동시 사용)하는 환경을 구성하였습니다.

# 2. 구성 환경 안내 및 다운로드
## 2-1. 구성 환경 안내
1. 서피스 환경
    - i5-6300U
    - DDR4 8GB RAM
    - 256GB SSD
        - 파티션 구성
            - 200MB - EFI
            - 120GB - Windows 10 Pro
            - 80GB - macOS Big Sur
            - 44GB - 파일 저장소
2. 조건
    - USB 구성 컴퓨터는 Windows 환경
    - Python 3 환경
    - macOS 11 BigSur와 Windows 10 모두 사용
    - 인터넷 연결 가능 환경 (별도의 랜카드 필요)
        - macOS에서 자체 지원하는 랜카드 혹은 macOS용 드라이버가 존재하는 랜카드
        - 본 글에서는 리얼텍 드라이버가 구성된 파일을 사용
        - 아이폰 혹은 셀룰러 아이패드가 있다면 해당 기기를 연결하여 테더링 가능 (대략 45GB 정도 다운로드)
    - 1GB 이상의 FAT32 UEFI 형식으로 포맷된 USB
    - ***모든 자료를 사전에 백업***
        - 최초 1회에 한해 Windows 10 파티션이 간혈적으로 포맷 되는 증상 있음
        - 필자 역시 해당되어 재설치
    - 가능하다면 서피스 이외에 별도의 데스크톱 혹은 랩톱
3. 구성 환경
    - OpenCore를 통한 해킨토시 설치
    - Windows 10 Pro 재설치
    - rEFInd를 통한 듀얼 부팅
4. 정의
    - 서피스 -> 서피스 프로4
    - USB -> macOS 설치용 USB

잘 모르시고 따라하신다면 그냥 **"아 이런게 다 있구나..."** 정도로 봐주시면 됩니다.

다음 과정은 두가지 길로 나누어 집니다. 하난 본인의 OpenCore를 구성하여 사용 하는 것이고, 다른 하나는 <a href="https://github.com/bigsadan/surface-pro-4-hackintosh" target="_blank">@bigsadan</a>씨의 이미 구성된 OpenCore 혹은 Clover 를 사용 하는 것 입니다.

본인의 OpenCore 구성의 경우 <a href="https://dortania.github.io/OpenCore-Install-Guide/" target="_blank">OpenCore 설치 가이드</a>를 참조하시면 구성이 가능 합니다. 또한 자세한 설명이 되어 있습니다. 본 글에서는 무작정 따라하는 해킨토시 Big Sur 설치를 목표로 @bigsadan씨의 구성된 파일을 사용하겠습니다. 일부 스크린샷의 경우 제가 구성한 OpenCore 임으로 다를 수 있으나 같은 결과가 나옵니다.

<span style="color:red; font-size:16pt">본 글은 모든 데이터를 지우고 클린 설치를 목표로 합니다. 자료 백업 필수!</span>

## 2-2. 파일 다운로드
그런 다음 필요한 파일을 준비해 보겠습니다.

1. <a href="https://www.python.org/downloads/" target="_blank">Python 3</a>
    - 이미 설치 되어있다면 재설치 불필요
    - 무엇인지 모른다면 위 링크로 들어가 <span style="color:yello">노란색 Download Python 3.x.x 버튼</span> 클릭 하여 다운로드 후 설치
2. <a href="https://github.com/bigsadan/surface-pro-4-hackintosh" target="_blank">@bigsadan씨의 해킨토시 구성파일</a>
    - 위 링크로 들어가 <span style="color:green">초록색 Code 버튼</span>을 눌러주신 뒤 "Download ZIP" 을 눌러 특정 장소에 저장
3. <a href="https://rufus.ie/" target="_blank">Rufus</a>
    - 위 링크로 들어가 하단 "Rufus x.xx 포터블"을 받아주시면 편리
4. <a href="https://github.com/acidanthera/OpenCorePkg/releases/" target="_blank">OpenCorePkg</a>
    - 위 링크로 들어가 가장 최신 버전의 "OpenCore-x.x.x-RELEASE.zip"을 특정 장소에 저장
5. <a href="https://github.com/corpnewt/GenSMBIOS" target="_blank">@corpnewt씨의 GenSMBIOS</a>
    - 위 링크로 들어가 <span style="color:green">초록색 Code 버튼</span>을 눌러주신 뒤 "Download ZIP" 을 눌러 특정 장소에 저장
6. <a href="https://www.microsoft.com/ko-kr/software-download/windows10" target="_blank">Windows 10 Pro ISO 파일</a>
    - 이는 서피스 이외 별도 데스크톱/랩톱에 저장하길 권장
    - 윈도우10 프로 설치를 위해 ISO를 필요
    - 별도의 방법을 통해 ISO 파일 혹은 설치 wim 파일등을 받으시면 됩니다.
    - 윈도우10 설치를 병핼 할 경우 아래 USB 포맷 과정에서 꼭 `Large FAT32`로 포맷
    - 혹은 swm 파일로 분할 설치 <a href="https://prolite.tistory.com/1023" target="_blank">여기</a> 참조


위 파일을 모두 다운로드 받으셨다면 본격적으로 해킨토시 서피스 프로를 만들어 보겠습니다.

## 2-3. 간략한 과정

1. OpenCore 를 통해 드라이버 및 애플 하드웨어로 인식 시키기
2. macOS 리커버리로 부팅
3. 리커버리 복구 옵션을 통해 macOS 디스크에 설치
4. 완료

# 3. macOS 리커버리 다운로드
3번에서 받은 파일 중 OpenCorePkg 폴더가 기준 입니다.

1. Utilites 폴더 내 macrecovery 폴더로 이동
2. Shift 키를 누른채 폴더 빈 공간을 마우스 오른쪽 클릭
3. 메뉴 중 "여기서 Powershell 창 열기" 클릭
4. 아래 명령어 입력

        cmd
        python macrecovery.py -b Mac-E43C1C25D4880AD6 -m 00000000000000000 download

5. 대략 600MB가 넘는 파일 다운로드
6. 같은 폴더 내에 **"BaseSystem.dmg 및 BaseSystem.chunklist"** 가 받아졌다면 성공

# 4. USB 및 디스크 구성
## 4-1. USB 구성
EFI 시스템에 사용되는 USB는 아래 두가지 조건을 만족하여야 합니다.

- 파티션 구조가 GPT 일것
- 포맷이 FAT32 (Large 포함) 일것

대부분의 USB는 해당 조건을 만족하지 못하므로 Rufus 라는 프로그램을 통해 포맷을 통해 설정해주어야 합니다. 이 과정에서 **USB 내 모든 자료가 지워집니다.**

위에서 받은 Rufus 앱을 실행하고 USB를 연결 한 뒤 아래와 같은 설정으로 맞춥니다.

![](https://dortania.github.io/OpenCore-Install-Guide/assets/img/format-usb-rufus.43feba9e.png)   
- 부트 선택을 "부팅 할 수 없음" 으로 선택
- 파티션 방식을 `GPT`로 선택
- 파일 시스템을 `"FAT32" 혹은 "대용량 FAT32"` 선택

선택을 다 하셨다면 "시작" 버튼을 눌러 포맷 합니다.

## 4-2. BitLocker 끄기
OpenCore 구성에 의해 BitLocker가 켜져있는 경우 디스크 파티션 변경이 불가능 합니다. 따라서 내 PC에서 C드라이브 아이콘에 잠물쇠가 있는 경우 아래 지침을 따라 BitLocker를 꺼주셔야 합니다.

자세한 내용은 <a href="https://www.samsungsvc.co.kr/solution/25356" target="_blank">삼성전자서비스 Windows10, 장치 암호화 (bitlocker) 해제 방법</a> 문서를 참고하시면 좋을 듯 합니다.

# 5. OpenCore 부트로더 구성 및 파일 복사

1. @bigsadan씨의 해킨토시 구성 파일 내 11.x 폴더로 이동
2. 모든 파일을 선택 및 복사
3. USB 최상위에 복사
4. USB에 "com.apple.recovery.boot" 라는 이름의 폴더를 생성
5. 과정3에서 받은 BaseSystem.dmg 및 BaseSystem.chunklist를 `com.apple.recovery.boot` 폴더 내에 복사
6. 모든 과정을 마치고 나면 USB내 구조가 아래 형태

        USB
        └EFI 폴더
         └BOOT 폴더
         └OC 폴더
        └com.apple.recovery.boot 폴더
         └BaseSystem.dmg
         └BaseSystem.chunklist

# 6. OpenCore 설정 파일 개인화
과정3에서 받은 GenSMBIOS 를 사용하여 OpenCore 설정 파일을 개인화 합니다.

## 6-1. 모델 식별자 및 UUID 변경
1. 우선 GenSMBIOS.bat 을 실행
    - 아래와 같은 창이 나온다면 "더보기"를 누르면 실행 버튼 표시   
    ![](assets/2021-03-29-Surface-Pro-4-Hackintosh/1.png)
2. 2번 Select config.plist 를 선택하기 위해 `2`를 입력 후 엔터
3. USB/EFI 폴더 내 OC 안 `config.plist` 파일을 CMD창 (검은창)으로 드래그앤드롭
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
기본적으로 @bigsadan씨의 `config.plist`는 러시아어 입니다. 이를 한국어로 바꾸기 위해 간단히 메모장 등으로 수정 필요합니다. 물론 이 과정이 없어도 대충 감으로 러시아어 사이를 해치며 언어 선택란에 들어가 한국어를 선택하여도 괜찮지만 멀끔한 화면을 위해 한국어로 변경해 줍니다.

1. 언어를 한국어로 설정 하기 위해 `config.plist`를 메모장 등으로 열기
    - `config.plist` 파일을 다른 앱으로 열기 메뉴 활용 혹은 메모장 파일 열기 창에서 아래와 같이 모든 파일 선택   
    ![](assets/2021-03-29-Surface-Pro-4-Hackintosh/3.png)
2. `prev-lang:kbd` 를 검색 한 뒤 아래줄을 다음과 같이 수정

        기존
        <key>prev-lang:kbd</key>
		<data>cnUtUlU6MjUy</data>

        수정 (두번째 줄 data -> string 으로 변경 후 ko:0 으로 값 변경)
        <key>prev-lang:kbd</key>
		<string>ko:0</string>

# 7. Surface UEFI 설정 변경
이제 사용자가 준비할 준비물은 모두 준비가 되었습니다. 다음으로 필요한 것은 UEFI 설정 변경 입니다.

우선 USB를 최우선적으로 부팅 과정에서 인식하도록 설정을 해야 합니다. 또한 Secure Boot 를 해제하여 OpenCore 부트로더를 사용 가능하게 변경 해야 합니다.

UEFI 부팅 방법, 설정 등에 관한 자세한 내용은 <a href="https://support.microsoft.com/ko-kr/surface/surface-uefi%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-df2c8942-dfa0-859d-4394-95f45eb1c3f9" target="_blank">Microsoft Surface UEFI를 사용하는 방법</a> 문서에 자세히 나와있어 같이 읽어주시면 좋겠습니다.

1. UEFI로 부팅 (위 Microsoft 문서 참조)
2. Security(보안) 탭으로 이동하여 Secure Boot란 하단에 `Change Configuration`을 선택 후 `None` 선택
3. Boot Configuration(부팅 구성) 탭으로 이동해 `USB Storage`를 드래그앤드롭하여 가장 위로 이동
4. Exit(끝내기) 탭으로 이동하여 `Restart now` 선택

# 8. OpenCore 부팅
서피스를 재시작 할 경우 검은 화면에 하얀글씨가 나오며 OpenCore 화면을 볼 수 있습니다.

        OpenCore Boot Menu

        1. macOS Installer (external)
        2. Reset NVRAM

위와 비슷한 메뉴가 나오며 이중 2번을 선택시 USB 내용물 중 일부가 지워지므로 주의

1. `macOS Installer (external)`를 방향키 혹은 숫자키를 통해 선택
2. 잠시 기다리면 macOS 복구 화면으로 부팅 성공
    - 혹여나 중간에 멈춘다면 마지막 Error 등을 구글에 검색

## 8-1. 오류 해결> Windows is hibernate
Windows is hibernate 라는 메시지가 출력된다면 Windows 1회 부팅 이후 다시 시도

# 9. 디스크 유틸리티를 통한 디스크 조정

1. 리커버리 메뉴 중 디스크 유틸리티 실행   
![](https://support.apple.com/library/content/dam/edam/applecare/images/ko_KR/macos/Big-Sur/macos-big-sur-recovery-disk.jpg)
2. 아무 파티션을 선택한 뒤 우측 상단 지우기 버튼 클릭 하여 디스크 전체 초기화
![](https://support.apple.com/library/content/dam/edam/applecare/images/ko_KR/macos/Big-Sur/macos-big-sur-recovery-mode-disk-utility-container-disc.jpg)
3. OpenCore 구성에 의해 디스크 파티션을 초기화 하고 난 후면 재시작 필요
    - 따라서 전원버튼을 5초이상 길게 누르거나 좌측 상단 시스템 재시동 이용
4. 디스크 유틸리티를 다시 실행
5. 우측 상단 파티션 기능을 사용하여 원하는 파티션 조절
    - macOS를 설치할 파티션은 APFS 로 포맷
    - Windows를 설치할 파티션 혹은 파일 저장 파티션은 MS-DOS FAT으로 포맷
    - 파티션 구성을 안할 경우 SSD 전체를 macOS가 사용하여 윈도우 설치가 불능
6. 디스크 파티셔닝이 끝났다면 디스크 유틸리티를 종료

## 9-1. 오류 해결> 디스크 지우기 혹은 파티셔닝 실행 시 강제로 꺼짐
제가 한참 걸렸던 부분 입니다. 이 부분은 원인은 파악하지 못하였지만 현재 파티션 구조에 의해 일어나는 문제인듯 합니다.

OpenCore 구성에 의해 각 파티션이 각기 다른 디스크로 인식이 되고 있었습니다. 따라서 실질적 물리 디스크는 1개인 파티션을 일부만 지우기를 시도하려 하여 그렇지 않나....싶습니다.

해결은 저는 Windows 10 설치 USB 내부에 있는 diskpart 를 사용하여 해결하였습니다. 이 부분은 약간 복잡 합니다.

1. USB를 서피스 이외의 데스크톱/랩톱에 연결
2. USB 폴더 구조를 아래와 같이 변경 (`_macOS` 폴더 생성 후 모든 파일 이동)

        USB
        └_macOS
         └EFI 폴더
         └com.apple.recovery.boot 폴더
3. Windows 10 Pro ISO 내부 파일을 모두 USB 최상단에 복사
4. USB를 다시 서피스에 연결하고 부팅
5. `Windows 설치` 앱이 실행된다면 `Shift + F10`을 눌러 명령 프롬프트 실행
    - 혹은 다음 버튼을 누른 다음 `문제 해결` -> `명령 프롬프트` 순으로 선택
6. 아래와 같은 코드 입력

        diskpart
        list disk
7. 출력값 중 본인의 서피스 디스크 용량과 같은 디스크 번호 기억
8. 아래와 같은 코드 입력
    - 디스크 번호가 0이라 가정
    - macOS에 할당할 파티션 크기는 80GB(=81,920MB)라 가정
    - Windows에 할당할 파티션 크기는 120GB(=122,880MB)라 가정
    - 본인의 상황에 맞게 설정

        sel disk 0
        clean
        convert gpt
        cre par efi size=250
        for quick fs=fat32 label="System"
        cre par pri size=81920
        for quick fs=fat32 label="macOS"
        cre par pri size=122880
        for quick fs=ntfs label="Windows"
        exit
9. 명령 프롬프트를 닫고 서피스를 종료 합니다.
10. USB를 다시 서피스 이외의 데스크톱/랩톱에 연결
11. USB 폴더 구조를 아래와 같이 변경 (`_Windows` 폴더 생성 후 모든 파일 이동)

        USB
        └_Windows 폴더
        └_macOS 폴더
12. `_macOS`에 있는 모든 파일을 최상위로 다시 꺼내기

        USB
        └_macOS 폴더
        └_Windows 폴더
        └EFI 폴더
        └com.apple.recovery.boot 폴더
13. 다시 디스크 유틸리티 실행 후 재시도

# 10. macOS Big Sur 설치 및 설정
## 10-1. 온라인으로부터 설치
![](https://support.apple.com/library/content/dam/edam/applecare/images/ko_KR/macos/Big-Sur/macos-big-sur-recovery-disk.jpg)   
드디어 macOS를 설치할 차례 입니다.

위 항목에서 `macOS Big Sur 다시 설치` 를 누른 뒤 APFS로 포맷했던 디스크를 선택하여 다음을 눌러주시면 인터넷에서 macOS Big Sur를 스스로 다운로드하여 설치를 진행 합니다.

이 과정은 약 30-60분 정도 소요됩니다.

## 10-2. macOS 설정
모든 과정이 끝났다면 다시시작을 하면 OpenCore 메뉴가 아래와 같이 변했을 것 입니다.

        OpenCore Boot Menu

        1. macOS Installer (external)
        2. macOS
        3. Reset NVRAM
이 중 2번을 선택하면 macOS로 부팅되며, 리얼맥과 동일하게 초기 설정을 진행하여 주시면 됩니다.

# 11. Windows 10 Pro 설치 및 설정
## 11-1. Windows 10 Pro 설치 준비
1. USB를 서피스 이외의 데스크톱/랩톱에 연결
2. USB 폴더 구조를 아래와 같이 변경 (`_macOS` 폴더 생성 후 모든 파일 이동)

        USB
        └_macOS
         └EFI 폴더
         └com.apple.recovery.boot 폴더
3. Windows 10 Pro ISO 내부 파일을 모두 USB 최상단에 복사
    - 모두 복사할 필요는 없지만 설명 생략
4. USB를 다시 서피스에 연결하고 부팅
5. 일반적인 과정을 통해 Windows 10 Pro를 재설치 합니다.

참고로 위 과정에서 받은 Windows 10 Pro 이미지는 모든 PC에 공통으로 들어가는 파일 입니다. 따라서 Surface 관련 요소가 탑제되어있지 않습니다. 시스템 정보에 나오는 Surface 로고도 없어지며 인터넷 연결 없이는 Surface 관련 드라이버도 설치되어있지 않습니다. 인터넷 연결 상태라면 당연히 자동으로 설치 됩니다.

이를 원치 않는 경우 <a href="https://support.microsoft.com/ko-kr/surface-recovery-image?ui=ko-KR&rs=ko-KR&ad=KR" target="_blank">Microsoft Surface 복구 이미지 다운로드 문서</a>를 참조하여 Surface 용 이미지를 받아야 합니다. 하지만 이는 대부분 구버전 윈도우10 이므로 업데이트 하는 시간이 소요되어 저는 잘 사용하지 않습니다.

설치가 완료되었다면 이제 그냥 서피스를 전원을 키신다면 Windows 10 으로 부팅되며 USB를 연결할 경우 OpenCore가 로딩되어 macOS로 부팅이 가능 합니다.

하지만 언제까지나 USB를 계속 꽂아둘수는 없으므로 듀얼 부팅 환경을 구성하겠습니다.


# 12. EFI 파티션 관리
## 12-1. rEFInd 다운로드
<a href="https://www.rodsbooks.com/refind/" target="_blank">rEFInd Boot Manager 홈페이지</a>에서 최신버전의 binary ZIP을 받아주시면 됩니다.

페이지 구조가 어지러워 잘 모르겠다면...

1. 목차 중 <a href="https://www.rodsbooks.com/refind/getting.html" target="_blank">`Getting rEFInd`</a> 클릭
2. `A binary zip file` 클릭

적절한 위치에 압축을 풀어 둡니다.

## 12-2. EFI 파티션 마운트
### 12-2-1. macOS에서 마운트
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

### 12-2-2. Windows에서 마운트
`EFI Mounter` 같은 서드파티 앱을 다운로드 하시거나 아래 명령어들을 통해 수동으로 가능합니다.

1. `윈도우키 + X`를 누름
2. `Windows Powershell (관리자)` 실행
3. 아래 명령어 입력

        cmd
        diskpart
        list disk
4. 출력값 중 본인의 서피스 디스크 용량과 같은 디스크 번호 기억
5. 아래 명령어 입력
    - 디스크0 이라 가정

        sel disk 0
        list part
6. 이중 System 파티션 번호 기억
7. 아래 명령어 입력
    - 파티션1이라 가정

        sel part 1
        assign letter=S
        exit
8. 이후 S드라이브에 EFI가 마운트 됩니다.

Windows 10에서 하시는 경우 EFI 드라이브에 접근하기 위해선 **관리자 권한**으로 실행된 앱만 가능 합니다.

Windows 탐색기는 이상하게 관리자 권한으로 실행하여도 작동되지 않습니다. 저는 그래서 그림판이나 메모장을 주로 이용 합니다. 

작업관리자 실행 - 상단 파일 메뉴 - 실행 - `mspaint` 혹은 `notepad` 입력 후 하단 `관리자 권한으로 실행` 체크 후 실행

위와 같은 과정을 거치신 다음 파일 열기 등 파일 탐색기 창에 나오는 다이얼로그를 띄우시면 거기서 EFI 파티션 접근이 가능합니다.

## 12-3. rEFInd 설치

1. EFI 파티션 폴더에 `refind`라는 폴더 생성
2. 11-1에서 받고 압축 푼 폴더 내 `refind` 폴더로 이동
3. `icons` 폴더, `refind_x64` 파일 및 `refind.conf-sample` 파일 EFI 파티션 내 `refind` 폴더에 복사
4. `refind.conf-sample` 파일 이름을 `refind.conf`로 변경
5. EFI 파티션 구조가 아래와 같아야함

        EFI
        └BOOT 폴더
        └APPLE 폴더 (삭제)
        └Microsoft 폴더
        └refind 폴더
         └icons 폴더
         └refind_x64.efi
         └refind.conf

## 12-4. 시스템 EFI에 OpenCore 설치

- USB 내 OC 폴더를 EFI 파티션 내 복사

        EFI
        └BOOT 폴더
        └Microsoft 폴더
        └refind 폴더
        └OC 폴더
        └그외 기타

## 12-5. rEFInd 테마 및 설정
자세히 다루지는 않겠습니다만, refind.conf 를 텍스트 편집기로 열어보시면 아래와 같은 값들이 있습니다.

        # 뭐라뭐라
        #enable_touch

        # 뭐라뭐라
        #enable_mouse

위 값 중 `#enable_~~`의 앞에 #를 지워주시면 터치와 마우스 사용이 가능해집니다. macOS에서 사용 가능한것이 아닌 rEFInd 에서 활성 입니다.

또한 테마도 지원하여 여러가지 이쁜 테마들이 존재합니다. 이는 제가 직접 다루지 않겠습니다.

필자는 <a href="https://github.com/bobafetthotmail/refind-theme-regular" target="_blank">@bobafetthotmail씨의 refular 테마</a>를 아이콘 및 폰트 크기를 가장 크게해서 다크모드로 사용 중 입니다.

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

# 14. 기타 문제 해결 및 마치며
## 14-1. 마치며...
위와 같은 과정을 거치고 나면 저와 완벽히 동일한 구성이 되었습니다. Windows 10 Pro 와 macOS Big Sur를 듀얼부팅 가능한 상태 입니다.

나름 시간이 상당히 걸리기도 하였고 무엇이든지 `나빼고 모두 문제가 없지만 나만 오류가 나는 현상`에 의해 검색에 의존하기도 사실 약간 어렵습니다.

그래서 최대한 제 환경과 동일하게 만들기 위해 이해보단 직접 무작정 따라하는 강좌를 만들게 되었습니다. 시간이 되시거나 관심이 있으시다면 이게 왜 그렇게 동작하는지를 조금 더 유심히 봐주시면 좋을 것 같습니다.

## 14-2. 기타 문제 해결
### 14-2-1. 윈도우 부팅시 파란 화면에 Recovery
아마 높은 확률로 제가 처음 언급드린 **최초 1회에 한해 Windows 파티션이 raw로 포맷되는 현상**을 겪으신듯 합니다.

이는 아직 원인을 잘 모르겠으며 대부분의 커뮤니티에서도 최초 1회에 한하여 그런듯 합니다. 따라서 윈도우10 재설치 및 13번 rEFInd 사용 항목을 한번 더 시행해 주시면 됩니다.