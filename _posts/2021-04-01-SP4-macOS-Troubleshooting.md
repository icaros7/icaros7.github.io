---
title:  "Surface Pro 4 Big Sur 해킨토시 문제 해결 및 도움"
categories:
    - Hackintosh
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
Surface Pro 4에 해킨토시 Big Sur 설치하며 생길 수 있는 문제들!

본 글의 메인 글은 [Surface Pro 4 Big Sur 해킨토시 무작정 따라 설치하기](https://minnote.net/%ED%8C%81_tip/Surface-Pro-4-Hackintosh/) 입니다.

# <a id="_normal"></a> 1. 기본적인 오류들
사실 이런 오류들엔 답이 없으며 너무 무수히 많은 변수가 있습니다. 도움을 받고 싶다면 해당 로그를 `그대로` Google 에 검색해 보는 것이 좋습니다. 혹시 해결하신 명확한 답변 혹은 솔루션이 있다면 제보를 해주시면 많은 분들께 도움이 됩니다.

# <a id="_WinHiber"></a> 2. OpenCore 부팅시 "Windows is hibernate"

        Windows is hibernate

위 메시지가 출력된다면 Windows 1회 부팅 이후 다시 시도 하시면 해결 됩니다.

# <a id="_diskutil"></a> 3. 디스크 지우기 혹은 파티셔닝 실행 시 강제로 꺼짐
제가 한참 걸렸던 부분 입니다. 이 부분은 원인은 파악하지 못하였지만 현재 파티션 구조에 의해 일어나는 문제인듯 합니다.

OpenCore 구성에 의해 각 파티션이 각기 다른 디스크로 인식이 되고 있었습니다. 따라서 실질적 물리 디스크는 1개인 파티션을 일부만 지우기를 시도하려 하여 그렇지 않나....싶습니다.

해결은 저는 **Windows** 설치 **USB** 내부에 있는 `diskpart`를 사용하여 해결하였습니다. 이 부분은 약간 복잡 합니다.

Windows 10 Pro 설치 파일의 경우 위에서 받은 Windows 10 Pro ISO 파일 내부에 있는 다음과 같은 폴더 및 파일들 입니다. ISO 파일 내부에는 더 많은 파일들이 있지만 전체 복사를 하셔도 무방하고, 아래와 같은 파일만 복사하셔도 무방합니다.   
![](/assets/2021-03-29-Surface-Pro-4-Hackintosh/10.png)

1. **USB**를 **별도 환경**에 연결
2. `_macOS` 폴더 생성 후 모든 파일 이동
3. **Windows** 설치 파일 (혹은 _Windows 폴더 내부 파일)을 모두 **USB** 최상단에 복사
4. 최종적으로 아래와 같은 구조가 탄생
    ![](/assets/2021-03-29-Surface-Pro-4-Hackintosh/8.png)
5. **USB**를 **서피스**에 연결하고 부팅
6. `Windows 설치` 앱이 실행된다면 `Shift + F10`을 눌러 명령 프롬프트 실행
7. 아래와 같은 코드 입력

        diskpart
        list disk
8. 출력값 중 본인의 **서피스** 디스크 용량과 같은 디스크 번호 기억
    - 본인 모델이 256GB라면 256GB에 가장 근접한 디스크를 선택
9. 아래와 같은 코드 입력
    - 디스크 번호가 0이라 가정
    - **macOS**에 할당할 파티션 크기는 80GB(=81,920MB)라 가정
    - **Windows**에 할당할 파티션 크기는 120GB(=122,880MB)라 가정
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
10. 명령 프롬프트를 닫고 **서피스**를 종료
11. **USB**를 **별도 환경**에 연결
12. `_Windows` 폴더 생성 후 모든 파일 이동
13. **macOS** 설치 파일 (혹은 _macOS 폴더 내부 파일)을 모두 **USB** 최상단에 복사
14. 최종적으로 아래와 같은 구조가 탄생
    ![](/assets/2021-03-29-Surface-Pro-4-Hackintosh/9.png)
15. 다시 `디스크 유틸리티` 실행 후 재시도

{% include ad_content.html %}

<hr>

# <a id="_raw"></a>4. **Windows** 부팅 실패 및 파란 Recovery
사실 이 문제는 여러가지 이유가 있지만 BCD라는 단어가 존재하며 macOS 사용하다가 급작스럽게 이런 현상을 겪으신다면 높은 확률로 처음에 언급드렸던 문제가 일어난 것 입니다.

바로 **Windows** 파티션이 raw 파일 시스템으로 변해버리는 현상 입니다. 자세한 원인은 아직 찾지 못하였지만 저의 경우엔 아래와 같은 상황에서 발생하였습니다. 계속 추가 예정 입니다.

- rEFIind가 아닌 <span style="color:red; font-size:18pt;">OpenCore 부트로더 상에서 **Windows**로 부팅 시도 시</span>

raw 파티션으로 **Windows** 시동 불능 상태가 되었다면 다음 두가지를 시도 해 볼 수 있습니다.

우선 공통적으로 해야할 사항 입니다.

Windows 10 Pro 설치 파일의 경우 위에서 받은 Windows 10 Pro ISO 파일 내부에 있는 다음과 같은 폴더 및 파일들 입니다. ISO 파일 내부에는 더 많은 파일들이 있지만 전체 복사를 하셔도 무방하고, 아래와 같은 파일만 복사하셔도 무방합니다.   
![](/assets/2021-03-29-Surface-Pro-4-Hackintosh/10.png)

1. **USB**를 **별도 환경**에 연결
2. `_macOS` 폴더 생성 후 모든 파일 이동
3. **Windows** 설치 파일 (혹은 _Windows 폴더 내부 파일)을 모두 **USB** 최상단에 복사
4. 최종적으로 아래와 같은 구조가 탄생
    ![](/assets/2021-03-29-Surface-Pro-4-Hackintosh/8.png)
5. **USB**를 **서피스**에 연결하고 부팅

## 4-1. **Windows** 설치 앱의 `chkdsk` 기능 사용
우선 raw 파일 시스템이 되는 경우 대부분 실제 모든 값이 0으로 덮어쓰여지는 Low Format이 되지 않고, 가장 앞부분의 정보 부분만 raw로 인식하게 혹은 파티션이 비어있도록 인식하는 Quick Format 상태로 존재 합니다.

이로 인해 **분명 NTFS 파일 구조를 취하고 있고, 정상적인 방법으로 Format이 이루어진것 같지 않은** 디스크는 디스크 검사 앱인 `chkdsk`에서 복구를 도와줍니다.

1. `Shift + F10`을 눌러 `명령 프롬프트` 실행
2. 아래와 같은 코드 입력

        diskpart
        list disk
3. 출력값 중 본인의 **서피스** 디스크 용량과 같은 디스크 번호 기억
    - 본인 모델이 256GB라면 256GB에 가장 근접한 디스크를 선택
4. 다음과 같은 명령어 입력
    - 디스크0이라 가정

        sel disk 0
        list vol
5. 결과값 중 Windows 10이 설치된 파티션에 문자 확인 및 기억
    - 필자의 경우 C드라이브로 설치했기에 C로 잡혀있으므로 다음 진행
    - 혹여나 결과 값 설치 파티션의 `Fs` 값이 `NTFS`인 경우 다음 해결 방안인 `4-2. 시동 복구` 진행
    - Windows 파티션과 크기가 같지만 별도의 문자가 없는 경우
        1. 3번 볼륨이라 가정
        2. `sel vol 3` 입력
        3. `assign letter=C` 입력
6. `exit` 입력 후 diskpart 종료
7. 다음과 같은 명령어 입력
    - C드라이브라 가정

        chkdsk C: /f
8. 디스크 검사 과정이 진행되며 raw 파티션으로 된 경우 대략 4KB 정도의 값을 복구 시도

## 4-2. **Windows** 설치 앱의 시동 복구 기능
이는 정말 단순히 EFI 파티션 내 **Windows Boot Manager**에 문제가 생겼을 때 가능한 방법 입니다.   
Surface 제품군의 경우 **Windows**에 한하여 문제가 발생하면 자동으로 복구를 해주지만 별도의 환경을 구성했으므로 직접 해줘야 합니다.

1. `문제 해결` 중 `고급 옵션` 선택
2. `시동 복구` 실행

이제 **Windows**가 부팅되길 기도하시면 됩니다.