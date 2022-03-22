---
title:  "[DreamHack.io] rev-basic-1 풀이"
categories:
    - DreamHack.io
tags:
    - dreamhack.io
toc: true
published: true
---
[DreamHack.io] rev-basic-1 풀이

# Environments

|Name|Config|
|:---:|:---|
|CPU|Apple M1|
|Architecture|ARM64|
|OS|Microsoft Windows 11 on ARM|
|Tools|- Hex-Rays IDA 7.5<br/>- A.S.L ExeInfo PE 0.0.6.7|

# 문제
URL: [https://dreamhack.io/wargame/challenges/15/](https://dreamhack.io/wargame/challenges/15/)

![](/assets/DreamHack/rev-basic-1-4.png)
```
Get Input
Print Wrong 또는 Correct
```

문제 형식: MSVC++ 2019 Windows 64 bit executable   
요약: MSVC++ AMD64 어플리케이션 `chall1.exe` 로부터 `correct` 를 출력 할 수 있는 문자열을 찾기

# File Format
![](/assets/DreamHack/rev-basic-1-1.png)

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
![](/assets/DreamHack/rev-basic-1-2.png)

우선 `IDA`를 통해 Static Analysis(정적 분석)을 시도. 잘 알려진 형식이므로 자동으로 main 진입점을 찾아 줌.   
`sub_140001000` call을 통해 입려받은 값을 확인 하는 방식. 값을 test(AND 연산)를 통해 동일하지 않다면 `sub_1400013B6`으로 jmp하여 Wrong 메시지 출력.

![](/assets/DreamHack/rev-basic-1-3.png)   
이를 확인 하기 위해 확인 call subroutin 부분에 Breakpoint를 걸고 Step into 시 사용자 Input 값의 모든 자리 수에 대하여 `cmp`를 통해 특정 문자와 비교 하는 것으로 확인 함.

또한 지난 `rev-basic-0`과 마찬가지로 IDA를 통해 디컴파일 된 Pseudo Code를 통해 확인 가능.

![](/assets/DreamHack/rev-basic-1.png)

`sub_140001000` 에서 `a1`의 각 문자열을 if 문으로 각각 체크 하는 것을 확인.