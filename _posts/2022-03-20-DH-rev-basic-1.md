---
title:  "[DreamHack.io] rev-basic-1 풀이"
categories:
    - DreamHack.io
tags:
    - dreamhack.io
published: true
---
[DreamHack.io] rev-basic-1 풀이

# 환경
CPU: Apple M1   
OS: Microsoft Windows 11 on ARM   
Tools: Hex-Rays IDA 7.5   

# 문제
URL: [https://dreamhack.io/wargame/challenges/15/](https://dreamhack.io/wargame/challenges/15/)

![](/assets/DreamHack/rev-basic-1-1.png)

요약: MSVC++ 2019 AMD64 어플리케이션 `chall1.exe` 로부터 `correct` 를 출력 할 수 있는 문자열을 찾기

# checksec

|Name|Status|
|:---:|:---|
|Dynamic Base|Present|
|ASLR|Present|
|High Entropy VA|Present|
|Force Integrity|NotPresent|
|Isolation|Present|
|NX|Present|
|SEH|Present|
|CFG|NotPresent|
|RFG|NotPresent|
|SafeSEH|NotApplicable|
|GS|Present|
|Authenticode|NotPresent|
|.NET|NotPresent|

# 풀이
![](/assets/DreamHack/rev-basic-1-2.png)

우선 `IDA`를 통해 정적 분석을 시도. 잘 알려진 형식이므로 자동으로 main 진입점을 찾아 줌.   
`sub_140001000` 호출을 통해 입려받은 값을 확인 하는 방식. 값을 AND 연산을 통해 동일하지 않다면 `sub_1400013B6`으로 날라가 틀림을 알 수 있음.

![](/assets/DreamHack/rev-basic-1-3.png)   
이를 확인 하기 위해 확인 서브루틴 호출 부분에 Breakpoint를 걸고 Step into 시 모든 자리 수 별 `cmp`를 통해 특정 문자와 비교 하는 것으로 확인

또한 지난 `rev-basic-0`과 마찬가지로 슈도 코드를 통해 유추.

![](/assets/DreamHack/rev-basic-1.png)

`sub_140001000` 에서 위와 같이 문자열 체크 하는 것을 확인