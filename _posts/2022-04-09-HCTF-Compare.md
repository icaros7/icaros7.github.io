---
title:  "[HackCTF] 잔상 풀이"
categories:
    - Forensics
tags:
    - ctf.j0n9hyun.xyz
toc: true
published: true
---
[HackCTF] 잔상 풀이

# Environments

|Name|Config|
|:---:|:---|
|CPU|Apple M1|
|Architecture|ARM64|
|OS|Microsoft Windows 11 on ARM|
|Tools|- SweetScape 010 Editor|

# Problem
URL: [https://ctf.j0n9hyun.xyz/challenges#잔상](https://ctf.j0n9hyun.xyz/challenges#잔상)

목표: `afteriamge_1.jpg`, `afterimage_2.jpg` 로부터 Flag 찾기   
접근 방법: 두 파일 차이점 분석

# Solution
![](/assets/HCTF/compare-0.png)

우선 문제를 받으면 문제 파일이 대놓고 `compare.zip` 이란 이름으로 압축 파일 내 두 파일이 있음.

위 사진으로부터 알 수 있는 정보는 두 파일이 Original Size는 동일하지만 같은 알고리즘으로 Compressed Size가 다른 것으로 보아 파일 내용이 다른 것으로 추정.

![](/assets/HCTF/compare-1.png)

파일 내 이미지는 위 사진과 같음.

![](/assets/HCTF/compare-2.png)

첫 접근 시 TXT 파일이 아닌 JPG 파일 자체에 대해 Whitespace Steganography 로 무언가가 숨겨져 있을꺼라 추정하여 접근. 하지만 어떠한 복호화도 되지 않으며 Original File Size가 동일 하므로 해당 접근 중단.

![](/assets/HCTF/compare-3.png)

Windows 자체 파일 diff 기능인 `FC` 명령을 이용하여 두 파일의 Binary를 비교. 처음엔 아무 생각 없다가 두번째 파일 부분이 6n 번재 코드가 많은 것을 보고 찾았다 직감.

![](/assets/HCTF/compare-4.png)

[ascii.cl/](https://ascii.cl/) 를 참고하여 두번째 파일의 차이점을 ASCII Code 로 치환시 Flag 흭득 가능.

# Comment
HackCTF 내 뒷쪽 문제여서 상당히 어려울줄 알았으나...접근이 좋았는지 너무 빠르게 풀었다. 오히려 Write-up 작성을 위해 중간 중간 스크린샷 찍은 시간이 더 소요된 것 같다.