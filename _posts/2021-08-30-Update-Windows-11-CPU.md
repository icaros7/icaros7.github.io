---
title:  "[뉴스] Windows 11 공식 지원 CPU 추가"
categories:
    - 뉴스_News
tags:
    - 윈도우11
    - 라이젠
    - 스카이레이크
    - 카비레이크
    - 서밋릿지
toc: true
---
새로운 코어 7세대 및 라이젠 1세대의 운명은?

뉴스 원문: [Windows Blogs의 Update on Windows 11 minimum system requirements and the PC Health Check app](https://blogs.windows.com/windows-insider/2021/08/27/update-on-windows-11-minimum-system-requirements-and-the-pc-health-check-app/)

# Windows 11 지원 프로세서 목록 갱신
안녕하세요. 지난 8월 27일 Microsoft 가 `Windows 11 지원 되는 Intel/AMD 프로세서` 목록을 업데이트 하였습니다.

이는 지난 6월 28일날 [Microsoft 공식 블로그를 통해 언급한](https://blogs.windows.com/windows-insider/2021/06/28/update-on-windows-11-minimum-system-requirements/) 내용에 해당하는 업데이트 입니다.

> As we release to Windows Insiders and partner with our OEMs, we will test to identify devices running on Intel 7th generation and AMD Zen 1 that may meet our principles.

> Windows 인사이더 및 OEM들과 협력 관계를 맺으며 인텔 7세대 및 AMD 젠 1이 실행 되고 있는 기기가 우리의 요구사항 조건을 충족 할 수 있는지 테스트 할 것 입니다. (오역 주의)

이 내용에 따라 지원 리스트가 업데이트 된 것 입니다. 여기에는 기대하던 인텔 코어 7세대 및 AMD 라이젠 1세대의 지원 여부가 반영 되었습니다.

# 새롭게 추가된 프로세서
## 인텔 프로세서
아래는 이번에 새롭게 추가 된 CPU 리스트 중 인텔 Core 시리즈들 입니다.

- 인텔 Core i9-7980XE
- 인텔 Core i9-7960X
- 인텔 Core i9-7940X
- 인텔 Core i7-7920X
- 인텔 Core i7-7900X
- 인텔 Core i7-7820HQ (단, 일부 기기만 해당)
- 인텔 Core i7-7820X
- 인텔 Core i7-7800X
- 인텔 Core i7-7740X
- 인텔 Core i7-7640X
- 인텔 Core i5-7640X

2017년 1분기 이후 출시된 일부 코어X 및 제온W CPU에 대한 지원을 추가한 것 입니다.

{% include ad_content.html %}

<hr>

위 리스트 중 눈에 띄는 것은 i7-7820HQ에 대한 내용 입니다. Microsoft는 보안 수준을 만족하는 일부 기기에만 해당 되는 것을 밝혔으며 해당 기기를 콕 집어 [Windows 11 Intel® Core™ i7-7820hq 지원 시스템](https://docs.microsoft.com/ko-kr/windows-hardware/design/minimum/supported/supported-systems)이라는 별도의 문서에서 명시하고 있습니다.

- Dell Precision 5520
- Lenovo ThinPad T470p
- Surface Studio 2

## AMD 프로세서
이번 업데이트에서 한가지 주목 할 점은 `Windows 11 지원 되는 AMD 프로세서`의 내용 역시 업데이트 되었지만 지원 CPU 리스트는 변하지 않았다는 것 입니다. "AMD와 함께 신중한 분석 결과, **윈도우11 지원 CPU에 추가하지 않기로** 하였다." 라고 말하였습니다.

# 공식 지원?
애시당초, 언급한 인텔 코어 7세대와 AMD 라이젠 1세대가 윈도우11에 공식 지원 CPU에 들지 못한 것은 MBEC(Mode-based execute control for EPT) 혹은 GMET(Guest Mode Execute Trap) 라 불리는 확장 명령어를 지원하지 않아서 입니다.

이는 Windows 10에서 보안을 목적으로 기존 선택적으로 고를 수 있었던 HVCI(Hypervisor-Enforced Code Integrity) 기능을 Windows 11 에서는 기본 값으로 사용하게 되었습니다. 이로 인하여 기본적으로 해당 명령어를 지원하지 않는 CPU들이 자연스럽게 지원이 되지 않았습니다.

![](/assets/2021-08-30-Update-Windows-11-CPU/1.png)

물론 해당 명령어가 없는 CPU 일지언정 Windows 11 최소 사양을 만족한다면 Windows 11 실제 사용도 가능하며, 해당 기능을 활성화 할 수 있습니다. 하지만 명령어를 통해 성능 저하를 최소화 한 CPU들과 다르게 모든 연산이 CPU에 부하를 주어 성능 저하가 상당하다고 합니다.

# 글을 마치며
안타깝게도 필자의 경우 모든 기기가 MBEC 혹은 GMET를 지원하지 않아 윈도우11의 모든 기능을 사용할 수 없습니다. 추후 사용중인 라이젠5 1600X를 3600으로 업그레이드를 예정 중 이지만, 가장 사용률이 높은 랩톱인 Surface Pro 4의 경우...(생략)

감사합니다!