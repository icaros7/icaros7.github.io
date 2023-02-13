---
title:  "[Assembly] Implement hello world output method via aarch64 assembly"
categories:
    - Reversing
tags:
    - Assembly
    - aarch64
toc: true
published: true
---
어셈블리어를 통해 화면에 "Hello world!" 출력하기

# 1. 서론

 기초 다지기 시간 두번째 시간이다. `aarch64` 아키텍처에서 "Hello world!" 문자열을 출력하는 프로그램을 어셈블리로 구현해보려 한다.

 `printf()` 함수 구현이 아닌 단순 문자열 출력이므로 각 아키텍처 별로 짚고 넘어가야할 포인트 및 차이점을 보면 된다.

# 2. Do It Yourself

- CPU: Apple M1 (ARMv8-A)
- Device: Apple MacBook Air 2020
- OS: Ubuntu 22.04 LTS
- Kernel: Linux-5.15.0-58-generic

 모든 설명은 Apple M1의 기반인 `ARMv8-A`를 기준으로 작성 되었다.

## 2-1. `.data` section

 우선 `.data` 섹션에 대해 알아보자. `.data` 섹션은 프로그램에 사용되는 데이터가 저장되는 영역이다. 다만 이 섹션은 **R/O** 성격을 갖고 있다. 즉 `const` 형식으로 선언이 되어 값이 바뀔 일 없는 static 한 변수들이 위치한다.

 반대로 `.bss` 섹션은 **R/W** 성격을 갖고, 프로그램 실행 이후 나중에 값이 초기화 혹은 변경이 가능하다. 이는 미리 `0`으로 채워져 있다. 즉 dyanmic 한 변수들이 위치한다.

 우선 우리는 "Hello world!" 라는 문자열을 출력할 것 이고, 이는 프로그램 실행 중 변경 될 일이 없다. 따라서 우리는 `.data` 섹션에 값을 할당 할 것이다.

```assembly
.data	; [1]

str:	; [2]
	.ascii	"Hello world!\n"	; [3]
str_len = . - str						;	[4]
```

 한줄 한줄 분석해보자.

1. 이 섹션이 `.data` 섹션임을 알린다.
3. `str` 이라는 변수에
4.  `ascii` 지시자로 `Hello world!\n` 라는 값을 저장한다.
5. `str_len` 이라는 값에 `str`의 크기를 계산해 길이 정보를 저장한다.

## 2-2. `.text` section

 `.text` 섹션은 실제 프로그램의 소스 코드가 들어 있다. 이 중 `.globl _start` 를 통해 이 프로그램의 진입점 임을 명시 할 수 있다. 이는 리버스 엔지니어링 시 진입점 판단에 사용된다. 하지만 이 프로그램에서는 사용되지 않는다.

## 2-3. `.global` section

 `.global` 섹션은 실제 컴파일러가 프로그램의 코드를 읽는 영역이다. 여기에는 우리가 흔히 아는 `int main()` 함수보다 먼저 실행되는 `_start` 메서드가 정의 된다. 여기서 `syscall`을 통해 우리는 시스템에게 "나는 출력을 할 것이야" 라고 알려 줄 수 있다.

> syscall 이란?
>
>  여기서는 간단하게만 짚고 넘어가겠다. syscall은 kernel space 내에 있는 하드웨어에 직접 엑세스(입력, 출력 등) 기능을 메서드 형태로 userland 상의 사용자(프로세스)에게 사용할 수 있게 해주는 것 이다.
>
>  만일 syscall이 존재하지 않는다면 userland 상의 프로세스는 하드웨어에 직접 접근이 불가능하거나, 애초에 직접 접근이 가능하다면 이를 악용하는 수 많은 악성 코드를 양산 할 수 있다.

 `syscall`은 사전 정의된 `int` 타입의 숫자로 소통한다. 필자 역시 모든 `syscall`을 외우진 못하고 자주 쓰이는 것만 외우고 다닌다. 모든 syscall은 [Google Chromium OS Docs/Linux System Call Table/arm64 (64-bit)](https://chromium.googlesource.com/chromiumos/docs/+/master/constants/syscalls.md#arm64-64_bit) 를 참조하면 알 수 있다.

| Dec  | syscall name | Reg 0 (X0)      | Reg 1 (X1)       | Reg 2 (X2)   |
| ---- | ------------ | --------------- | ---------------- | ------------ |
| 63   | read         | unsigned int fd | char * buf       | size_t count |
| 64   | write        | unsigned int fd | const char * buf | size_t count |

```assembly
.text

.global _main
_main:
	mov	x0, #1
	ldr	x1, =str
	ldr x2, =str_len
	mov w8, #64
	svc	#0
	
	mov	x0, #0
	mov	w8, #93
	svc	#0
```

 이 문단은 줄 별이 아닌 순차적으로 보겠다. 우리가 사용할 출력을 담당하는 `write` syscall은 64번이다. 0번 레지스터 (X0)에 표준 출력 file descripter인 `1`을 넣어 우리는 출력을 할 것이다. 따라서 `write syscall`의 첫번째 매개변수 `unsigned int fd`로 `1`을 대입하여 준다.

- `mov x0, #1` - move 하여라, #1 을 x0 으로.

 `x86-64`와 다르게 `aarch64`는 메모리에 데이터를 직접 RW가 불가능하다. 따라서 `LDR` (Load)를 통해 레지스터에 메모리 상의 데이터를 가져와야한다. 1번 레지스터 (X1)에 `const char * buf` 형식으로 출력할 값을 넣어 줄 수 있다. 따라서 우리는 "Hello world!\n"가 존재하는 `str`을 넣을 것 이다. 또한 2번 레지스터 (X2)에 `size_t count` 형식으로 출력할 값의 길이를 전달해야한다. 우리는 미리 계산을 하여 `str_len`에 담아 두었다.

- `ldr x1, =str` - load 하여라, str 값을 x1로
- `ldr x2, =str_len` - load 하여라, str_len 값을 x2로

 32-bit 8번 레지스터 (W8)에 `syscall`의 번호를 넣어 우리가 어떤 것을 수행 할 것인지 알려준다. 이후 `svc` 명령을 통해 32-bit 8번 레지스터에 담겨있는 값과 `sys_call_table` 대조해 해당 `syscall`을 **Supervisor Call**(커널 모드 호출) 한다.

- `mov w8, #64` - move 하여라, #64를 w8로
- `svc #0` - syscall을 호출하여라 (0에도 권한 관련 의미가 있지만 여기선 생략)

 이후 모든 과정이 수행되었다면 우리는 return 값을 return 해야한다. 따라서 `exit` syscall을 호출 해야한다.

| Dec  | syscall name | Reg 0 (X0)     |
| ---- | ------------ | -------------- |
| 93   | exit         | int error_code |

 작성한 흐름에는 (프로그램 혹은 프로그래머가 판단 하기엔) 아무런 문제가 없었으므로 우리는 흔히 말하는 정상 종료인 `0`을 반환하면 된다. 이는 첫번째 레지스터 (X0)에 `0`을 넣어 `exit` syscall을 수행하면 된다.

- `mov x0, #0` - move 하여라, #0을 x0로
- `mov w8, #93` - move 하여라, #93을 w8로
- `svc #0` - syscall을 호출하여라

## 2-4. 정리

 지금까지 작성한 어셈블리 코드를 모두 이어 붙이면 다음과 같다.

```assembly
.data

str:
	.ascii	"Hello world!\n"
str_len = . - str

.text

.global _start
_start:
	mov	x0, #1
	ldr	x1, =str
	ldr x2, =str_len
	mov w8, #64
	svc	#0
	
	mov	x0, #0
	mov	w8, #93
	svc	#0
```

 이제 실제 이 파일을 컴파일 하여 분석 및 실행해보자.

```shell
$ gcc hello.s -c -o hello.o && gcc hello.o -o hello -nostartfiles
$ objdump -xxd hello.o
hello.o:     file format elf64-littleaarch64
hello.o
architecture: aarch64, flags 0x00000011:
HAS_RELOC, HAS_SYMS
start address 0x0000000000000000
private flags = 0x0:

Sections:
Idx Name          Size      VMA               LMA               File off  Algn
  0 .text         00000030  0000000000000000  0000000000000000  00000040  2**3
                  CONTENTS, ALLOC, LOAD, RELOC, READONLY, CODE
  1 .data         0000000d  0000000000000000  0000000000000000  00000070  2**0
                  CONTENTS, ALLOC, LOAD, DATA
  2 .bss          00000000  0000000000000000  0000000000000000  0000007d  2**0
                  ALLOC
SYMBOL TABLE:
0000000000000000 l    d  .text  0000000000000000 .text
0000000000000000 l    d  .data  0000000000000000 .data
0000000000000000 l    d  .bss   0000000000000000 .bss
0000000000000000 l       .data  0000000000000000 str
000000000000000d l       *ABS*  0000000000000000 str_len
0000000000000000 g       .text  0000000000000000 _start



Disassembly of section .text:

0000000000000000 <_start>:
   0:   d2800020        mov     x0, #0x1                        // #1
   4:   580000e1        ldr     x1, 20 <_start+0x20>
   8:   58000102        ldr     x2, 28 <_start+0x28>
   c:   52800808        mov     w8, #0x40                       // #64
  10:   d4000001        svc     #0x0
  14:   d2800000        mov     x0, #0x0                        // #0
  18:   52800ba8        mov     w8, #0x5d                       // #93
  1c:   d4000001        svc     #0x0
        ...
                        20: R_AARCH64_ABS64     .data
  28:   0000000d        .word   0x0000000d
  2c:   00000000        .word   0x00000000

$ file hello
hello: ELF 64-bit LSB pie executable, ARM aarch64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux-aarch64.so.1, BuildID[sha1]=5d5bf8e5cedaabed7a3f769de81e457760083cfd, not stripped

$ ./hello
Hello world!
```

 참고로 우리는 `main()`이 없으므로 gcc에게 알려주어야 한다. 따라서 `-nostartfiles` 옵션을 추가해 컴파일 해야한다. 실제 동작시켜보면 아무 문제 없이 잘 동작하는 것을 확인할 수 있다!

# 3. C언어와 차이점

```c
#include <unistd.h>
#include <stdlib.h>
#include <string.h>

int main() {
	char * s = "Hello world!";
	write(1, s, strlen(s));
	exit(0);
}
```

 위 코드는 우리가 작성한 어셈블리 코드를 C언어로 어느정도 비슷하게 맞추었다. 위 코드를 어셈블리화 시켜서 열어보자.

```shell
$ gcc hello_c.c -S -o hello_c.s
$ cat hello_c.s
    .arch armv8-a
    .file   "hello_c.c"
    .text
    .section    .rodata
    .align  3
.LC0:
    .string "Hello world!"
    .text
    .align  2
    .global main
    .type   main, %function
main:
.LFB6:
    .cfi_startproc
    stp x29, x30, [sp, -32]!
    .cfi_def_cfa_offset 32
    .cfi_offset 29, -32
    .cfi_offset 30, -24
    mov x29, sp
    adrp    x0, .LC0
    add x0, x0, :lo12:.LC0
    str x0, [sp, 24]
    ldr x0, [sp, 24]
    bl  strlen
    mov x2, x0
    ldr x1, [sp, 24]
    mov w0, 1
    bl  write
    mov w0, 0
    bl  exit
    .cfi_endproc
.LFE6:
    .size   main, .-main
    .ident  "GCC: (Ubuntu 12.1.0-2ubuntu1~22.04) 12.1.0"
    .section    .note.GNU-stack,"",@progbits
```

 우리가 작성한 코드와는 살짝 다른 모습을 보이며, 일부 자세한 정보를 포함하고 있다. 조금 자세히 뜯어보자.

```assembly
    .arch armv8-a				; [1]
    .file   "hello_c.c"	; [2]
    .text
    .section    .rodata	; [3]
    .align  3	# [4]
.LC0:
    .string "Hello world!"	; [5]
    .text
    .align  2								; [4]
```

1. 우선 `.arch`를 통해 어느 아키텍처를 기준으로 작성되었는지 명시하여 준다.
2. `.file`을 통해 최초의 C 코드 파일 명이 명시되어있다.
3. 하기 섹션이 **Read Only Data**임을 명시하여 `const`와 같은 효과를 낸다. (쓰기 시도 시 예외 처리)
4. 모든 데이터는 위치 카운터를 기준으로 padding 하게 한다.
5. `LC0`에 ascii 타입이 아닌 string 타입으로 "Hello world!"가 stack 내에 정의되어있다.

```assembly
main:
.LFB6:
    .cfi_startproc
    stp x29, x30, [sp, -32]!	; [1]
    .cfi_def_cfa_offset 32
    .cfi_offset 29, -32
    .cfi_offset 30, -24
    mov x29, sp
    adrp    x0, .LC0				; [2]
    add x0, x0, :lo12:.LC0	; [2]
    str x0, [sp, 24]
    ldr x0, [sp, 24]
    bl  strlen
    mov x2, x0
    ldr x1, [sp, 24]
    mov w0, 1
    bl  write
    mov w0, 0
    bl  exit
    .cfi_endproc
.LFE6:	; [3]
    .size   main, .-main
    .ident  "GCC: (Ubuntu 12.1.0-2ubuntu1~22.04) 12.1.0"
    .section    .note.GNU-stack,"",@progbits
```

1. 모든 데이터 관리를 데이터 직접 대입이 아닌 stack을 기준으로 한다.
2. `LC0`에 저장된 string을 0으로 초기화된 첫번째 레지스터 (X0)에 가르키게 한다. (과정 생략)
3. GCC 컴파일 정보를 저장하고 있다.

 또한 syscall을 직접 호출하고 있지 않고, `bl`을 통해 분기를 행한다.

# 4. 번외

## 4-1. LLVM

 위 상기 모든 코드는 Linux의 GCC 컴파일러를 기준으로 진행을 하였다. 문뜩 Apple LLVM 도 궁금해지기 시작해 컴파일을 해보았다.

- CPU: Apple M1 (ARMv8-A)
- Device: Apple MacBook Air 2020
- OS: macOS Ventura 13.2
- Kernel: Darwin Kernel Version 22.3.0

```assembly
	.section	__TEXT,__text,regular,pure_instructions
	.build_version macos, 13, 0	sdk_version 13, 1
	.globl	_main                           ; -- Begin function main
	.p2align	2
_main:                                  ; @main
	.cfi_startproc
; %bb.0:
	sub	sp, sp, #48
	stp	x29, x30, [sp, #32]             ; 16-byte Folded Spill
	add	x29, sp, #32
	.cfi_def_cfa w29, 16
	.cfi_offset w30, -8
	.cfi_offset w29, -16
	mov	w8, #0
	str	w8, [sp, #12]                   ; 4-byte Folded Spill
	stur	wzr, [x29, #-4]
	adrp	x8, l_.str@PAGE
	add	x8, x8, l_.str@PAGEOFF
	str	x8, [sp, #16]
	ldr	x8, [sp, #16]
	str	x8, [sp]                        ; 8-byte Folded Spill
	ldr	x0, [sp, #16]
	bl	_strlen
	ldr	x1, [sp]                        ; 8-byte Folded Reload
	mov	x2, x0
	mov	w0, #1
	bl	_write
	ldr	w0, [sp, #12]                   ; 4-byte Folded Reload
	bl	_exit
	.cfi_endproc
                                        ; -- End function
	.section	__TEXT,__cstring,cstring_literals
l_.str:                                 ; @.str
	.asciz	"Hello world!"

.subsections_via_symbols
```

 기본적인 ARMv8 aarch64의 어셈블리 명령어는 같지만 컴파일러의 정리 방식이 조금 다름을 알 수 있다. 또한 string 처리 시 `.asciz` 지시자를 사용해 끝에 `0`을 붙이는 것을 알 수 있다.