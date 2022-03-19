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
Tools: IDA   

# 문제
URL: [https://dreamhack.io/wargame/challenges/14/](https://dreamhack.io/wargame/challenges/14/)

요약: `chall0.exe` 로부터 `correct` 를 출력 할 수 있는 문자열을 찾기

# 풀이
단순 문자열 체크 이므로 `IDA`를 통해 슈도코드에서 유추가 가능 할 것이라 판단 하였습니다.

![](/assets/DreamHack/rev-basic-0.png)

`sub_140001000` 에서 문자열 비교가 이루어지며, 해당 부분에서 `strcmp src` 대상 확인 가능