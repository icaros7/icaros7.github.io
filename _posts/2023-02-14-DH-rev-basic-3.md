---
title:  "[DreamHack.io] rev-basic-3 풀이"
categories:
    - Reversing
tags:
    - dreamhack.io
toc: true
published: true
---
[DreamHack.io] rev-basic-3 풀이

 오랜만에 `rev-basic-3`를 다시 풀어보았다.

# Environments

|Name|Config|
|:---:|:---|
|CPU|Apple M1|
|Architecture|ARM64|
|OS|Microsoft Windows 11 on ARM|
|Tools|- Hex-Rays IDA 7.5|

# 문제
URL: [dreamhack.io/wargame/challenges/17/](https://dreamhack.io/wargame/challenges/17/)

```
Get Input
Print Wrong 또는 Correct
```

요약: MSVC++ 2019 AMD64 어플리케이션 `chall3.exe` 로부터 `correct` 를 출력 할 수 있는 문자열을 찾기

# File Format
Upx not packed MSVC++ Windows 64 bit executable

# checksec

|Name|Status|
|:---:|:--:|
|NX|Yes|
|Canary|Yes|
|ASLR|Yes|
|Dynamic Base|Yes|
|High Entropy VA|Yes|
|SEH|Yes|
|SafeSEH|/|
|Force Integrity|Yes|
|Control Flow Guard|No|
|Isolation|Yes|
|Auth|No|

# 풀이
![](/assets/DreamHack/rev-basic-3-0.png)   

 IDA를 통한 분석 시 `sub_7FF7A0FF1000` 으로부터 결과 값을 전달 받아 `jz`를 통해 분기하는 것을 확인 가능. 즉 앞서 언급한 메서드에서 `EAX`가 `0`으로 설정 될 경우 **Worng**을 출력하며 종료된다. 따라서 `jnz` 등으로 patch 시 **Correct**를 받을 수 있지만 목표가 이것이 아니므로 생략한다.

![](/assets/DreamHack/rev-basic-3-1.png)   

 빠른 진행을 위해 disassemble 된 코드를 분석하자면 **특정 배열**과 매개변수로 받은 int 타입을 기준으로 계산식을 통해 `0` 또는 `1`을 반환한다. 한자리라도 일치하지 않으면 `0`을 반환하는 형식이다. 우선 특정 배열 (`byte_7FF7A0FF3000`)을 한번 봐보자.

![](/assets/DreamHack/rev-basic-3-2.png)   

 우선 hex 값이므로 dec 혹은 ascii 값으로 보면 다음과 같다.

```
.data:00007FF7A0FF3000 byte_7FF7A0FF3000 db 'I', '`', 'g', 't', 'c', 'g', 'B', 'f', '€', 'x', 2 dup('i')
.data:00007FF7A0FF3000                 db '{', '™', 'm', 'ˆ', 'h', '”', 'Ÿ', 8Dh, 'M', '¥', 9Dh
.data:00007FF7A0FF3000                 db 'E', 8 dup(0)

.data:00007FF7A0FF3000 byte_7FF7A0FF3000 db 73, 96, 103, 116, 99, 103, 66, 102, 128, 120, 2 dup(105)
.data:00007FF7A0FF3000                 db 123, 153, 109, 136, 104, 148, 159, 141, 77, 165, 157
.data:00007FF7A0FF3000                 db 69, 8 dup(0)

I`gtcgBf€x...
```

![](/assets/DreamHack/rev-basic-3-3.png)   

 정상적이지 않은 문자열과 코드를 보았을 때 계산식을 통해 통과 가능한 문자열을 계산 할 수 있음을 알 수 있다. 사용작 입력 값을 `input`, 도출 해야할 원래의 문자열을 `str` 이라 보고 계산식을 정리한다. 이러면 어떠한 `input`을 넣었을 때 `str`에 만족하는 값이 나오는지 알 수 있다.

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        int arr[] = { 73, 96, 103, 116, 99, 103, 66, 102, 128, 120, 105, 105, 123, 153, 109, 136, 104, 148, 159, 141, 77, 165, 157, 69 };
        ArrayList <String> list = new ArrayList<>();

        for (int i = 0; i < 24; i++) { list.add(Character.toString((arr[i] - ( 2 * i)) ^ i)); }
        for (int i = 0; i < 24; i++) { System.out.print(list.get(i)); }
    }
}
```

 머리와 펜으로 계산 할 수도 있지만 간단하게 컴퓨터에게 맡기기로 했다. 그러면 우리가 입력해야할 문자열 `str`을 알 수 있다.