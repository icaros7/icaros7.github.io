---
title:  "[HackCTF] Magic PNG 풀이"
categories:
    - Forensics
tags:
    - ctf.j0n9hyun.xyz
toc: true
published: true
---
[HackCTF] Magic PNG 풀이

# Environments

|Name|Config|
|:---:|:---|
|CPU|Apple M1|
|Architecture|ARM64|
|OS|Microsoft Windows 11 on ARM|
|Tools|- Mh-Nexus HxD Editor<br/>- SweetScape 010 Editor|

# Problem
URL: [https://ctf.j0n9hyun.xyz/challenges#Magic PNG](https://ctf.j0n9hyun.xyz/challenges#Magic%20PNG)

목표: `MP__1.PNG` 으로부터 Flag 찾기   
접근 방법: PNG 구조 분석

# Theory Summary
PNG 구조를 간단히 정리하고 시작 하려 한다. 더 상세히 정리 되어 있지만 큰 틀은 아래와 같다.

|이름|설명|
|:---:|:---|
|PNG Signature|12 Byte, Signature Header 정보|
|IHDR|17 Byte, PNG 파일에 대한 정보|
|IDAT|복수의 Chunk로 이루어진 실질적 데이터 공간|
|IEND|8 Byte, File의 끝을 알리는 Chunk|

# Solution
![](/assets/HCTF/magic-png-0.png)
우선 Image Viewer로 열었을 때 정상적으로 열리지 않음. `HxD`로 확인 시 PNG 파일로 추정. 하지만 PNG 파일의 정상적인 Signature Header 랑 다름.

```
# 정상적인 PNG File Signature Header
89 50 4E 47 0D 0A 1A 0A

# MP__1.PNG File Signature Header
89 50 4E 47 2E 0A 2E 0A
```

`1A`는 SUB 표기지만, `2E`는 `.` 표기.

![](/assets/HCTF/magic-png-1.png)

우선 이를 정상적인 Header 로 복구 시 위와 같은 이미지 표출. Width Height 에 맞춰 가로로 데이터를 표시하며 나아가는 PNG 구조 상 처음은 괜찮고 중간 부분의 Chunk가 손상되었음을 알 수 있음.

첫 시도로 `IDAT` Chunks 중 일부 CRC32 값 변조 등을 의심. 지금 나의 단계로서는 CRC32 값으로부터 원본 데이터를 찾는 것 보다는 기존 데이터를 갖고 CRC32 값 복구가 쉬울 것 이라 판단.

![](/assets/HCTF/magic-png-3.png)

Chunks 탐색 도중 의심 가는 구간 발견. 2번째 IDAT Chunk 이름 데이터가 변조되었음을 확인.

```
# 정상적인 Chunk 이름 데이터
49 44 41 54

# MP__1.PNG 상 2번째 Chunk 이름 데이터
69 64 61 74
```

![](/assets/HCTF/magic-png-2.png)

우선 올바른 풀이인가 검증을 위해 010 Editor 로 재차 검증 시도. PNG 파일 구조를 읽어 정리 및 리스트업 해주는 기능인 `Template` 가 PNG 상의 문제가 있다고 보고.

1. Signature Header 를 복구
2. 2번째 IDAT Chunk (전체 Chunk 중 5번째)

위 두가지 작업 사항을 동일하게 적용 시 정상적으로 Template 로드 및 Flag가 포함된 이미지 표시.