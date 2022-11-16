---
title:  "2022-06 Project Interom Summary"
categories:
    - iCAROS7 Works
tags:
    - linux
    - kernel
    - vulnerability
    - memory
    - heap
    - exploit
toc: false
published: true
---

# Project Interim Summary

 2022년 6월 말 경부터 시작된 Linux Kernel Vulnerability Detect Project를 진행하며 새롭게 공부하거나 기존 지식을 확장한 몇가지 것들을 적어보았다.

# 1. 커널 내 메모리 관리

## A. Memory Allocator

 현대의 Linux 커널 내 메모리 관리는 x86-64 기준으로 4kb 단위로 paging 되어 관리된다. 이때 paging 이 생기는 이유는 간단하다. paging 이 아닌 물리적 메모리와 1:1 할당이 된다면 잦은 할당과 반환으로 인해 연속적으로 할당이 되지 못하고 파편화 되어 커널의 가장 근본이자 중심적인 부분인 **효율성**에 중대한 영향을 끼킨다. 이를 해결하기 위해 메모리를 큰 PAGE 단위로 할당을 받아 PAGE를 한번더 CHUNK 단위로 세분화 하여 요청한 크기에 할당하며, 다시 남은 CHUNK는 `freelist` 등으로 관리하여 추후 재할당이 가능하다.

> 그럼 왜 Allocator가 별도로 존재하는가?

 사실 메모리를 allocator 없이 사용도 가능하다. 하지만 위에서 언급하였듯이 **효율**적이지 못하다는 것이다. userland 상의 어플리케이션이던 커널 내 다른 프레임워크 레이어던 메모리에 접근하기 위해서는 system call을 이용하여 커널에게 요청을 해야한다. 그렇다면 매번 커널에게 요청시 system call이 호출되는 것이 효율이진 않을 것이다.

 그렇다면 어떤식으로 해결이 가능한지 살펴보자. 우선 system call 호출 빈도를 줄이는 것은 간단하게 생각하자면 **"정말 자주 요청을 안하면 되는 것"** 이다. 그러기 위해서는 위 paging 처럼 한번에 많은 메모리를 할당 받은 뒤 자체적 관리 체계를 통해 재분배 하는 것이 좋을 것 이다.

### i. Slab allocator

- 이전 Linux 시스템에서 사용하던 기본 할당자
- 자주 사용되는 data structor를 사전 할당
- Cache-freiendly를 지향
- 하나 이상의 `slab object`가 연속되게 모여 `slab`으로 구성
  - Slab allocator와는 다름
  - `slab` 관리를 `queue` 기반으로 관리
  - 위 slab의 해더에는 meta data가 포함
  - 이는 object의 align을 방해 및 메모리 가용 공간 확보 시 처리 복잡
- 하기 `list`로 CHUNK를 분류하여 관리
  - full
  - partial
  - empty

### ii. Slub allocator

- 현 Linux 시스템 기본 할당자
- Slab allocator의 queueing 시 overhead가 큰 것을 해결하기 위해, Unqueued allocator를 목표로 탄생
- Simple-freiendly 및 debug-freiendly를 지향
- `slab` object의 metadata를 최적화
- `partital list`로 관리
  - freelist 를 위한 별도의 메모리 할당을 안함

## b. Memory Alignment

 위에서 메모리 관리에 대해서 간략히 정리하게 되었다. 그렇다면 관리는 무엇을 위해서 필요한가? 메모리 정렬을 위한 것 이다. 정렬은 왜 필요한가? Lagacy 시스템을 포함하여 현대 주로 널리 사용되는 `amd64` 및 `intel64`는 모두 8 byte 정렬을 한다. 이 말은 메모리는 8의 배수 단위로 메모리를 Paging 하여 관리하며, 9 byte의 데이터를 가져올 경우 16 byte를 가져온 뒤 필요하지 않은 데이터를 폐기한다.

> 왜 1 byte씩 가져오면 되는 것을 왜 굳이 8 byte 씩 가져올까? 공학자들은 지능이 부족한가?

 답은 아니라는것을 이미 알 것 이다. 이유는 CPU와 Memory의 성능 차에서 온다. CPU는 작업 주기가 동작 주파수가 증가함에 따라 기하급수적으로 올라가고 있지만, 메모리는 아직 그에 미치지 못한다. 근래에는 차가 많이 줄었지만 말이다. 이로 인해 한 system call이 이루어질 때 각 byte를 직접 하나하나 매 작업 주기 별로 가져오는 것 보다 한번에 많은 양을 가져와 처리하는 것이 생긴 것 이다.

 여기서 생각을 해보자. 위에서 메모리는 4 byte의 PAGE 단위로 나누어져 있고, 8 byte 씩 메모리 데이터를 가져온다고 하였다. 그렇다면 9 byte를 가져올 경우 연속된 두 Page를 통해 8 byte를 가져왔지만 다음 8 byte가 엉뚱한 것을 가르키고 있다면 어찌될까? 그렇다면 현대의 시스템은 올바르지 않은 데이터라 판단하며 해당 과정을 취소하고 에러를 반환한다. 물론 그대로 읽어오거나 다시 재 탐색을 통해 총 두번의 가져오기를 통해 값을 반환 하는 architecture도 있다. (두번 가져오면 그만큼 비효율적이다)

 이러한 이유로 인해 메모리 정렬이 필요한 것이다. 다만 이것은 위에서 언급하였듯 아키텍처에 의존하여 동작한다. 이번에 새롭게 알게된 메모리 정렬의 사용 예시는 일부 kernel 기능의 경우 아키텍처를 구분하여 동작하는 방식을 취한다는 것이다. 걔중 일부 과정은 kernel 내 build 정보나 시스템 architecture를 가져오는 복잡한 과정을 거치는 것이 아닌 구조체 및 변수에 할당 된 `size`를 보고 판단하는 간결한 과정을 거친다. 어찌보면 이 역시 **효율적이지 못한 일종의 꼼수** 과정이라 해당 기능의 개발자는 이를 대체하고 싶어한다.

```c
/* this is a dummy structure to find out the alignment requirement for a struct
 * containing all the fundamental data types that are used in ipt_entry,
 * ip6t_entry and arpt_entry.  This sucks, and it is a hack.  It will be my
 * personal pleasure to remove it -HW
 */

struct _xt_align {
	__u8 u8;
	__u16 u16;
	__u32 u32;
	__u64 u64;
};

#define SIZE __alignof__(struct _xt_align)
```

`_xt_align` 이라는 구조체를 생성하고, unsigned 형식의 각 `u8`, `u16`, `u32`, `u64` 을 선언한다. 다음으로 `__alignof__` 라는 GCC 매크로를 통해 컴파일된 환경의 레지스터 bit를 확인 가능하다. 이는 가장 큰 데이터의 값을 기준으로 반환한다.

 이러한 과정이 수행이 되서 어떠한 이득을 취할 수 있는가? 이는 서로 다른 architecture의 구조체나 변수 등을 각각 형변환 할 때 이용이 가능하다. 데이터의 크기가 architecture에서 요구하는 크기가 되지 못한다면 데이터 뒤에 dummy 값을 붙여 (padding) 해당 크기로 만들 수 있다. 간단한 예시로 4 byte를 차지하는 `u32`와 달리 `u8`의 경우 2 byte를 차지하는 것이 아니다. Architecture가 2 byte로 처리하는것이 비효율적이라 판단 하였으므로 `\0`등의 dummy 값 padding을 통해 4 byte로 만든다.

# 2. Heap Feng Shui

## a. Heap spray

 타겟에 ASLR 등의 메모리 보호 기법이 포함되어 있다면 static memory address를 알기 어렵다. 이를 해결하기 위해 Heap 에 `No operation code` + `Shell code`를 스프레이로 뿌리듯 전반적으로 도배(?)하는 것을 heap spray 라고 한다. 이는 결국 추후 exploit 시 확률을 높이기에 신뢰성이 높아진다 판단이 가능 하다.

 물론 이는 Heap 에 접근이 가능한 상황 혹은 JS, AS등과 같이 heap을 통한 메모리 조작이 가능한 상황에 한정 된다.

 또한 heap spray는 다음과 같은 두가지를 고려해야한다.

- `Shell code`가 들어있는 메모리 주소에 다른 pointer가 올 경우 해당 주소로 흘러가므로 실패 확률이 증가
- Heap이 늘 연속되게 비어있는 상황이 아니므로 기존 데이터 혹은 재할당된 데이터로 인해 빈틈으로 인한 실패 확률을 고려

 위 사항을 종합하여 생각한다면 조금이라도 exploit 성공 확률을 증가시키기 위해선 연속된 영역에 연속되게 spray 하여야 한다.

## b. Heap feng shui

 Heap feng shui는 위에서 언급한 여러 고려사항을 생각해야한다. 여기서 더욱 더 exploit 가능한 상황을 만들고자 heap의 레이아웃을 공격자가 원하는 방향으로 배치하여 확률을 높일 수 있다.

 Heap은 chunk 단위로 관리 되며 해당 chunk 레이아웃을 공격자가 바꾸는 것이다. 이는 상당히 오랜기간 존재해왔던 기법으로 어플리케이션의 흐름을 바꾸는데 이용 된다. 특히 이전 java script에서 수많은 취약점을 나으며 화두되었다.

 사실 이 부분은 아직 더 공부가 필요한 부분이다. 다만 이번에 알게된 점은 Heap feng shui의 목표는 Heap spray를 통해 모든 heap을 가득채워 새로운 heap을 할당받게 한 뒤 해당 heap과 기존 heap을 즐겁게 놀아준다는 것이다.

 또한 Stack pivoting 역시 heap feng shui의 확장의 영역이라는 것도 알게 되었다. 이에 관해서는 별도로 정리를 해야겠다.

 # 3. GFP Kernel Flag

GFP flag는 `kmalloc()` 사용시 인자로 넘길 수 있다. 이는 kernel이 해당 인자에 맞춰서 용도와 규칙을 프리셋에 맞춰 메모리를 할당한다.

1. `GFP_ATOMIC`
   - Kernel timer, schedule 등 Interupt 과정에서 주로 사용
   - 전반적인 Interupt 및 `SoftIRQ` 의 과정에서 sleep 되는 것을 방지
   - sleep을 방지하지 못할 경우 할당에 실패. 이로 인한 의도치 않은 흐름을 방지 해야할 때 사용
   - 영단어 `atomic`에서 원자력 발전소는 쉬지 못하는 것 (sleep)에서 유래
2. `GFP_KERNEL`
   - Kernel 내 타 메서드등의 할당에서 주로 사용
   - `ZONE_NORMAL`, lower zone 등 실제 물리적 메모리와 1:1 Mapping을 위함
   - 이로 할당 될 경우 직접 회수가 가능하다는 차이점
3. `GFP_KERNEL_ACCOUNT`
   - `GFP_KERNEL`과 동일하게 동작하지만 `kmemcg`에 의해 제어 (Kermel Memory Control Groups)
4. `GFP_NOWAIT`
   - `GFP_ATOMIC`와 비슷한 역할
   - 할당 작업 중단시 의도치 않은 결과를 초례할 수 있는 작업시 사용
   - 메모리 회수, 물리적 I/O 작업시, 파일 시스템 callback 등에서 사용
5. `GFP_NOIO`
   - Direct reclaim 알고리즘을 통해 초기화 등의 과정이 필요 없는 할당시 사용
     - 이는 할당 과정 중 어떠한 물리적 I/O 처리를 하지 않음을 뜻함
   - 이로 인하여 원치않는 `deadlock` 혹은 재귀가 발생 할 수 있음
     - 따라서 직접 사용보다는 `memalloc_noio_*` 등을 통해 적절한 주석과 함께 사용 권장 
   - 모든 할당 요청 call은 `GFP_NOIO`를 암시적으로 상속받음
6. `GFP_NOFS`
   - Direct reclaim 알고리즘을 통해 파일 시스템 call 등이 필요 없는 할당시 사용
     - 이는 할당 과정 중 어떠한 파일 시스템 call 이 처리 되지 않음을 뜻함
   - 이로 인하여 원치 않는 `deadlock` 혹은 재귀가 발생 할 수 있음
     - 따라서 직접 사용보다는 `memalloc_nofs_*` 등을 통해 적절한 주석과 함께 사용 권장
   - 모든 할당 요청 call은 `GFP_NOFS`를 암시적으로 상속받음
7. `GFP_USER`
   - userland 상의 어플리케이션이 메모리 직접 할당을 받아야 하는 경우 사용
     - 이는 커널 또는 하드웨어가 직접적으로 메모리 데이터에 엑세스가 가능 하다는 것을 뜻함
8. `GFP_HIGHUSER`
   - `ZONE_HIGHMEM` 영역에 할당을 해야하는 경우 사용
   - x86 시스템의 경우 1024MB 까지만이 kernel에 할당되어 있음. 이후 구간은 High Memory로 지정
   - amd64 (x86-64)의 경우 충분한 메모리 주소가 있으므로 별도의 제한이 없어 사용되지 않음
9. `GFP_DMA`
   - `ZONE_DMA` 영역에 할당을 해야하는 경우 사용
     - Direct Memory Access가 가능한 PAGE가 존재
   - 기기 드라이버 등에서 주로 사용
   - 역사적 의의를 위해 남겨져 있지만 가급적 사용을 지향
   - arm, arrch64 등의 일부 아키텍처에서는 bit 제한 없이 사용가능, x86의 경우 하위 16MB의 메모리만 사용 가능
     - 32비트 전체 DMA를 위해서는 `GFP_DMA32`를 이용

# 4. SMEP / SMAP / KASLR

 Kernel은 userland 메모리 영역을 접근도, 실행도 하면 안된다. userland 상의 메모리 영역은 사용자가 쉽게 접근이 가능하여 해당 영역에는 무엇이 있을지 알 수 없다. 이 때문에 현대의 x86(arm)와 x86-64(aarch64)는 SMAP (PXN), SMEP (PAN) 이라는 하드웨어적 기능을 통해 read, write, execute를 제한 하였다.

 개념을 알기 전 SMEP와 SMAP가 작동되고 있는지는 `CR4`(Control Regisiter 4) 값을 알면 알 수 있다. `CR4` 레지스터는 각종 extention 등의 정보를 담고 있다. 따라서 `CR4` 레지스터의 값을 보면 어떤 상태인지 알 수 있다. 만일 `CR4` 레지스터가 `0x00000000001006f0`의 값을 갖고 있다면 2진수로 변환시 다음과 같다

```assembly
100000000011011110000 (2)
```

여기서 리틀-엔디안으로 읽어서 20번째 bit가 SMEP, 21번째 bit가 SMAP이다. `0`은 비활성화, `1`은 활성화 상태이다. 위의 경우 SMAP가 enable 된 상태를 의미한다.

## a. Supervisor Mode Execution Protection

> Userland 상의 메모리 영역에서 커널의 코드 실행을 방지

 동작 원리는 간단하다. Userland에 해당하는 현대의 privilege ring  중 3의 메모리 내에 있는 명령어 fetch를 금지하는 것 이다. 물론 Return Oriented Programing을 통해 우회가 가능하다.

 이를 통해 excute를 막을 수 있으나 메모리 내에 있는 데이터에 대한 read/write는 막지 못한다는 점이 있다.

## b. Supervisor Mode Access Prevention

> Userland 상의 메모리 영역의 데이터 참조 방지

 SMEP를 보완하기 위해 나온 access도 차단한 보호 기법이다. 이로 인해 excute 와 더불어 read/write 역시 막을 수 있게 되었다. SMEP와 마찬가지로 privilege ring 3 영역의 데이터에 제한을 둔다. 이는 userland 상의 주소를 참조하는 것을 제한한다. 물론 이 역시 ROP Chanin을 heap spray 이후 stack pivot을 통해 ROP 가능 하다.



## c. Kernel Address Space Layout Randomization

 기본적으로는 Address Space Layout Randomization과 동일한 원리이지만 KASL은 kernel 단에서 개입하는 ASLR이라 이해하면 편하다. 프로세스가 매번 실행 될 때마다 할당되는 메모리 영역을 무작위로 할당하여 취약점을 통한 공격을 어렵게 하기 위함이다. KASLR 역시 kernel의 memory base 주소를 무작위로 배치한다.

 허나 ASLR과 마찬가지로 같은 프로세스 내에서 동일한 구조로 사용되는 데이터의 경우 특정 위치로부터 늘 같은 offset을 두고 무작위 배치 된다. 이러한 특징을 이용하여 동일하게 사용되는 라이브러리의 base 주소를 알 수 있다면 static 한 주소의 offset 과 같은 위치에 공격자가 원하는 데이터가 있을 것 이다. 또한 KASLR이 enable 되어있지 않은 `vmlinux`에서 base 주소를 알아내어 offset을 얻을 수 있다. 이후 현재의 base 주소에서 잘 알려진 고정 주소를 통해 offset을 계산하여 얻는다.

# 5. Use-After-Free

 UAF를 직역 하면 "**해제 된 다음 사용**"이다. 위에서 메모리는 중구난방한 크기로 사용되는 것을 막기 위하여 일정 단위 등으로 분배를 한다는 것을 알게 되었다. 하지만 매번 새로운 공간을 찾아 할당 하는 것 보다는 이미 한번 할당 된 크기의 단위를 기억한 다음, 같은 크기의 요청이 들어오면 이전에 사용되었던 위치를 재할당 해주는 것이다. 이는 heap을 병합하거나 새롭게 분할하는 시간을 줄이고자 **효율**을 목적으로 이루어지는 행위이다.

> 그런데 이전 heap을 그대로 할당 받는다면 해당 위치에는 무엇이 들어가 있을까? `0`?

 위 한 문장이 UAF에 대한 간략한 설명을 해준다. 메모리 관리자는 초기화 (initialization)을 해주진 않는다. 따라서 `memset()` 등으로 직접 초기화 이후 사용을 해야한다. 이전의 데이터에 접근이 가능한 상태가 되면 해당 위치에 공격자가 원하는 흐름 또는 데이터를 넣어둔다면 추후 사용이 가능하게 된다. 물론 이는 실시간으로 공격자가 원하는 사이즈만큼의 다른 공간이 해제되어 해당 방면으로 할당이 된다면 의미 없는 이야기가 된다. 또한 Heap은 늘 동적으로 할당 되기에 운이 나쁘다면 공격하는 사이에 새로운 할당이 해당 공간에 생기고 다른 데이터가 들어가게 된다. 이를 방지 하기 위해 실제 세계에서는 UAF 단독으로 무언가를 하기보다는 함께 사용한다.

 일례로 `CVE-2021-22555` 취약점의 한 PoC 중 일부에서 **효율**적인 exploit을 위하여 UAF를 2번으로 나눈다. 이는 SMEP와 SMAP를 고려하여 확률을 높이기 위함이다.

# 6. `msgsnd()`

`msgsnd()`는 Inter Process Communication 에서 주로 사용되는 메세지 queue에 message를 넣는 함수이다. 메시지 큐는 kernel 에서 관리되므로 프로세스가 종료되고서도 남아있다는 특징이 있다. 이러한 특징으로 인해 `msgsnd()`가 exploit 시나리오에 종종 등장한다. 또한 `msgsnd()`의 경우 메시지를 조각으로 나누어 전송하며 이 과정에서 이용되는 struct `msg_msg` 에는 늘 다른 메시지를 가르키는 pointer가 존재한다.

```c
// File: /include/linux/msg.h

/* SPDX-License-Identifier: GPL-2.0 */
#ifndef _LINUX_MSG_H
#define _LINUX_MSG_H

#include <linux/list.h>
#include <uapi/linux/msg.h>

/* one msg_msg structure for each message */
struct msg_msg {
	struct list_head m_list;
	long m_type;
	size_t m_ts;		/* message text size */
	struct msg_msgseg *next;
	void *security;
	/* the actual message follows immediately */
};

#endif /* _LINUX_MSG_H */
```

```c
// File: /tools/include/linux/types.h

struct list_head {
	struct list_head *next, *prev;
};
```

 Linked list를 이용하는 struct  `m_list` 내 포인터를 이용하여 어플리케이션을 여러 방면으로 공격자가 원하는 흐름으로 보낼 수 있다.