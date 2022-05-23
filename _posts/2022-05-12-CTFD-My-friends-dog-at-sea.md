---
title:  "[CTF-d] 제 친구의 개가 바다에서 풀이"
categories:
    - Forensics
tags:
    - ctf-d.com
toc: true
published: true
---
[CTF-d] 제 친구의 개가 바다에서 풀이 풀이

# Environments

|Name|Config|
|:---:|:---|
|CPU|Intel Core i7 7700|
|Architecture|Intel64|
|OS|Microsoft Windows 11|
|Tools|- [incoherency.co.uk/image-steganography/](https://incoherency.co.uk/image-steganography/#unhide)<br>- Cassum StegSolve|

# Problem
URL: [ctf-d.com/challenges#제%20친구의%20개가%20바다에서…](http://ctf-d.com/challenges#제%20친구의%20개가%20바다에서…)

목표: `hidden.jpeg` 로부터 Flag 찾기   
접근 방법: 이미지 밝기 조절을 통한 Flag 찾기

# Solution
![](/assets/CTFD/Friend-dogs-0.png)

문제를 보자마자 밝기가 무척 밝은 것을 알 수 있다. 아마 #FFFFFF 색을 LSB 한 것 같다는 생각이 들었다.

우선 가장 저명한 Tool 인 StegSolve 로 이미지를 열어 보았다. 하지만 정확한 식별이 어려워 다음 도구를 사용

[Image Steganography](https://incoherency.co.uk/image-steganography/#unhide) 온라인에서 간편하게 숨겨진 Bits 를 찾아내 준다.

다만 Flag를 바로 찾을 수는 있으나 식별이 여전히 어렵다. StegSolve 보단 나은 환경이지만 어느정도 감으로 Flag를 짜맞추었다.