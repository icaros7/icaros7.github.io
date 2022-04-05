---
title:  "[HackCTF] 세상에서 잊혀진 날 찾아줘! 풀이"
categories:
    - Forensics
tags:
    - ctf.j0n9hyun.xyz
toc: true
published: true
---
[HackCTF] 세상에서 잊혀진 날 찾아워! 풀이

# Environments

|Name|Config|
|:---:|:---|
|CPU|Apple M1|
|Architecture|ARM64|
|OS|Microsoft Windows 11 on ARM|
|Tools|- Mh-Nexus HxD Editor|

# Problem
URL: [https://ctf.j0n9hyun.xyz/challenges#세상에서%20잊혀진%20날%20찾아줘!](https://ctf.j0n9hyun.xyz/challenges#%EC%84%B8%EC%83%81%EC%97%90%EC%84%9C%20%EC%9E%8A%ED%98%80%EC%A7%84%20%EB%82%A0%20%EC%B0%BE%EC%95%84%EC%A4%98!)

![](/assets/HCTF/find_me-0.png)

목표: `pdf.pdf` 으로부터 `Flag 찾기

접근 방법: PDF 구조 분석

# Solution
![](/assets/HCTF/find_me-1.png)
정상적인 `pdf` 파일 인 것은 해당 파일을 HxD로 열어봄으로서 확인.

우선 PDF 파일의 구조를 알아야 분석 가능. PDF 파일은 크게 4부분으로 이루어 짐

|이름|설명|
|:---:|:---|
|Header|파일의 정보를 담고 있음|
|Body|`obj`~`endobj` 사이에 실질적 데이터가 들어있음|
|xref Table|복수의 Table로 이루어진 Body 상의 Object 위치나 유효성 정보 기록|
|Trailer|xref Table의 위치나 Object 정보 수록|

![](/assets/HCTF/find_me-2.png)

xref Table 에서 총 오브젝트 개수를 알 수 있음.

```
xref // xref 시작 지점
0 36 // 0번으로 시작해, 총 36개 obj
0000000000 65535 f // 위치 고유번호 표시여부(yes/no = n/f)
0000000017 00000 n
...
```
위 분석을 통해 표시가 되고 있는 obj에 한해 obj를 찾기 시작. 이 쯔음부터 `pdf.pdf` 파일 내 표시되던 것은 이미지 라고 판단하여 obj 중 이미지와 관련 된 것을 찾기 시작.

![](/assets/HCTF/find_me-3.png)

```
/Type/XObject/Subtype/Image/Width 620/Height 289/ColorSpace/DeviceRGB/BitsPerComponent 8/Filter/DCTDecode/Interpolate false/Length 13240
```
7번째 obj가 위와 같고 0x06BA~C를 통해 JPG 이미지 임을 확인.

```
xrerf
0 36 // 0번 부터 시작
0000000000 65535 f
0000000017 00000 n
0000000364 00000 n
0000000420 00000 n
0000000619 00000 n
0000001119 00000 n
0000001297 00000 n
0000001557 00000 f // jpg file
0000014977 00000 n
0000015180 00000 n
```

xref Table에서 7번째 (0번부터 시작 하므로 8번째 줄) 표시 여부를 `f`로 고쳐주면 flag 확인 가능