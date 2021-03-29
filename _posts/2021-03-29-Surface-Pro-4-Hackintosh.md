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
published: false
---
서피스 프로4에 해킨토시 설치 하기 및 실사용 후기!

**서론이 길다 느끼신 다면 상단 우측 원하시는 목차로 바로 이동하세요! 다운로드 과정 부터 시작 하시려면 2-2번!**

안녕하세요! 이번에 X코드 쓸일이 있어서 해킨토시를 설치하게 되었습니다. 진짜 맥북이 있었을 때에는 무거워서 찬밥 신세라 금방 팔아버렸지만 꼭 없으면 아쉽습니다.

힌지가 있으면서 가볍게 쓸 수 있는 M1 맥북 에어를 구매하고 싶지만 아직 썬더볼트가 2포트 모델 밖에 없기에 구매를 미루고 있습니다.

그러면 대체로 잠시 해킨토시의 힘을 빌려 봅시다.

해킨토시의 경우 최대한 넓은 의미로 보자면 Apple 사에서 공식적으로 지원하지 않는 하드웨어에 macOS를 설치하는 행위 입니다.

맥북, 아이맥, 아이맥프로, 맥미니 등이 아닌 데스크톱이나 랩톱에 설치하는 것도 해킨토시이고, 구형 애플 기기에 지원되지 않는 최신 macOS를 설치할 경우도 해킨토시의 원리를 사용 합니다.

# 1. 해킨토시 원리 및 알아둘 점
해킨토시의 원리는 부트로더에서 부팅 시 에드온을 통해 BIOS의 값들을 덮어쓰기 하는 방식 입니다.

따라서 UEFI와 Secure Boot, TPM 등을 사용하는 서피스 시리즈의 경우 아래와 같은 사항을 확실히 인지하셔야 합니다. 또한 해킨토시 설치 시 사용이 불능 한 것도 알려드리겠습니다.

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
    - 1GB 이상의 FAT32 UEFI 형식으로 포맷된 USB
    - ***모든 자료를 사전에 백업***
        - 최초 1회에 한해 Windows 10 파티션이 간혈적으로 포맷 되는 증상 있음
        - 필자 역시 해당되어 재설치
3. 구성 환경
    - OpenCore를 통한 해킨토시 설치
    - rEFInd를 통한 듀얼 부팅

잘 모르시고 따라하신다면 그냥 **"아 이런게 다 있구나..."** 정도로 봐주시면 됩니다.

다음 과정은 두가지 길로 나누어 집니다. 하난 본인의 OpenCore를 구성하여 사용 하는 것이고, 다른 하나는 <a href="https://github.com/bigsadan/surface-pro-4-hackintosh" target="_blank">@bigsadan</a>씨의 이미 구성된 OpenCore 혹은 Clover 를 사용 하는 것 입니다.

본인의 OpenCore 구성의 경우 <a href="https://dortania.github.io/OpenCore-Install-Guide/" target="_blank">OpenCore 설치 가이드</a>를 참조하시면 구성이 가능 합니다. 또한 자세한 설명이 되어 있습니다. 본 글에서는 무작정 따라하는 해킨토시 Big Sur 설치를 목표로 @bigsadan씨의 구성된 파일을 사용하겠습니다. 일부 스크린샷의 경우 제가 구성한 OpenCore 임으로 다를 수 있으나 같은 결과가 나옵니다.

## 2-2. 파일 다운로드
그런 다음 필요한 파일을 준비해 보겠습니다.

- <a href="https://www.python.org/downloads/" target="_blank">Python 3</a>
    1. 이미 설치 되어있다면 재설치 불필요
    2. 무엇인지 모른다면 위 링크로 들어가 <span style="color:yello">노란색 Download Python 3.x.x 버튼</span> 클릭 하여 다운로드 후 설치
- <a href="https://github.com/bigsadan/surface-pro-4-hackintosh" target="_blank">@bigsadan씨의 해킨토시 구성파일</a>
    1. 위 링크로 들어가 <span style="color:green">초록색 Code 버튼</span>을 눌러주신 뒤 "Download ZIP" 을 눌러 특정 장소에 저장
- <a href="https://rufus.ie/" target="_blank">Rufus</a>
    1. 위 링크로 들어가 하단 "Rufus x.xx 포터블"을 받아주시면 편리
- <a href="https://github.com/acidanthera/OpenCorePkg/releases/" target="_blank">OpenCorePkg</a>
    1. 위 링크로 들어가 가장 최신 버전의 "OpenCore-x.x.x-RELEASE.zip"을 특정 장소에 저장

위 파일을 모두 다운로드 받으셨다면 본격적으로 해킨토시 서피스 프로를 만들어 보겠습니다.

# 3. macOS 리커버리 다운로드
3번에서 받은 파일 중 OpenCorePkg 폴더가 기준 입니다.

1. Utilites 폴더 내 macrecovery 폴더로 이동
2. Shift 키를 누른채 폴더 빈 공간을 마우스 오른쪽 클릭
3. 메뉴 중 "여기서 Powershell 창 열기" 클릭
4. 아래 명령어 입력

        cmd
        python macrecovery.py -b Mac-E43C1C25D4880AD6 -m 00000000000000000 download
5. 대략 600MB가 넘는 파일 다운로드
6. **"BaseSystem.dmg 및 BaseSystem.chunklist"** 가 받아졌다면 성공

# 
