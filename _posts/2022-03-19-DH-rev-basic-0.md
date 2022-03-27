---
title:  "[DreamHack.io] rev-basic-0 풀이"
categories:
    - Reversing
tags:
    - dreamhack.io
toc: true
published: true
---
[DreamHack.io] rev-basic-0 풀이

# Environments

|Name|Config|
|:---:|:---|
|CPU|Apple M1|
|Architecture|ARM64|
|OS|Microsoft Windows 11 on ARM|
|Tools|- Hex-Rays IDA 7.5<br/>- A.S.L ExeInfo PE 0.0.6.7|

# 문제
URL: [https://dreamhack.io/wargame/challenges/14/](https://dreamhack.io/wargame/challenges/14/)

![](/assets/DreamHack/rev-basic-0-4.png)   
```
Get Input
Print Wrong 또는 Correct
```

요약: MSVC++ 2019 AMD64 어플리케이션 `chall0.exe` 로부터 `correct` 를 출력 할 수 있는 문자열을 찾기

# File Format
![](/assets/DreamHack/rev-basic-0-3.png)

Upx not packed MSVC++ Windows 64 bit executable

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
![](/assets/DreamHack/rev-basic-0-1.png)

우선 `IDA`를 통해 Static Analysis(정적 분석)을 시도. 잘 알려진 형식이므로 자동으로 main 진입점을 찾아 줌.   
`sub_140001000` call을 통해 Input 값을 확인 하는 방식. 값을 test(AND 연산)를 통해 동일하지 않다면 `sub_140001166`으로 jmp 하여 wrong 메시지 출력을 하는 것을 확인.

![](/assets/DreamHack/rev-basic-0-2.png)   
이를 확인 하기 위해 call subroutine 부분에 Breakpoint를 걸고 Step into 시 `strcmp` 대상이 확인이 가능.

또한 단순 strcmp(문자열 체크) 이므로 Pesudo Code에서 확인 가능.

![](/assets/DreamHack/rev-basic-0.png)

`sub_140001000` 에서 문자열 비교가 이루어지며, 해당 부분에서 `strcmp src` 대상 확인 가능