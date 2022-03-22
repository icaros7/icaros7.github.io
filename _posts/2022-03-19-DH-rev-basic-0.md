---
title:  "[DreamHack.io] rev-basic-0 풀이"
categories:
    - DreamHack.io
tags:
    - dreamhack.io
published: true
---
[DreamHack.io] rev-basic-0 풀이

# 환경
CPU: Apple M1   
OS: Microsoft Windows 11 on ARM   
Tools: Hex-Rays IDA 7.5   

# 문제
URL: [https://dreamhack.io/wargame/challenges/14/](https://dreamhack.io/wargame/challenges/14/)

요약: AMD64 어플리케이션 `chall0.exe` 로부터 `correct` 를 출력 할 수 있는 문자열을 찾기

# 풀이
![](/assets/DreamHack/rev-basic-0-1.png)

우선 `IDA`를 통해 정적 분석을 시도. 잘 알려진 형식이므로 자동으로 main 진입점을 찾아 줌.   
`sub_140001000` 호출을 통해 입려받은 값을 확인 하는 방식. 값을 AND 연산을 통해 동일하지 않다면 `sub_140001166`으로 날라가 틀림을 알 수 있다.

![](/assets/DreamHack/rev-basic-0-2.png)   
이를 확인 하기 위해 확인 서브루틴 호출 부분에 Breakpoint를 걸고 Step into 시 `strcmp` 대상이 확인이 가능 하다.

또한 단순 문자열 체크 이므로 슈도코드에서 유추가 가능 할 것이라 판단.

![](/assets/DreamHack/rev-basic-0.png)

`sub_140001000` 에서 문자열 비교가 이루어지며, 해당 부분에서 `strcmp src` 대상 확인 가능