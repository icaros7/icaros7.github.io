---
title:  "[HackCTF] So Easy? 풀이"
categories:
    - Forensics
tags:
    - ctf.j0n9hyun.xyz
toc: true
published: true
---
[HackCTF] So Easy? 풀이

# Environments

|Name|Config|
|:---:|:---|
|CPU|Apple M1|
|Architecture|ARM64|
|OS|Microsoft Windows 11 on ARM|
|Tools|- Mh-Nexus HxD Editor<br/>- Bandisoft Bandizip|

# 문제
URL: [https://ctf.j0n9hyun.xyz/challenges#So%20easy?](https://ctf.j0n9hyun.xyz/challenges#So%20easy?)

![](/assets/HCTF/so_easy-0.png)

목표: 위 사진(Screenshot) 으로부터 Flag 찾기

접근 방법: 정상적으로 열리는 `jpg` 파일 이므로 파일 헤더 및 구조를 확인

# 풀이
![](/assets/HCTF/so_easy-1.png)

우선 정상적인 `jpg` 파일 인 것을 jpg signatures 를 통해 확인. [JPEG File Interchange Format](https://en.wikipedia.org/wiki/JPEG_File_Interchange_Format) 문서상의 표를 참고하면 `EOI`도 쉽게 찾을 수 있음. 정상적인 파일이라면 파일 후미에 무언가 붙어 있을 확률이 높다 판단.

![](/assets/HCTF/so_easy-2.png)

`JPG`상의 EOI (End Of Image) 를 뜻하는 `FF D9` 뒤에 수상한 무엇가 붙음. 다만 `HackCTF{He_s0ggazzi_long}` 에서 알 수 있다싶이 "**힝 소갓찌 롱**"... 틀린 Flag가 명백하지만 일딴 시도. 역시나 실패. 그 뒤에 있는 `hidden.txt` 를 눈여겨 보았음.

![](/assets/HCTF/so_easy-3.png)

[List of file signatures](https://en.wikipedia.org/wiki/List_of_file_signatures)를 통해 위 가짜 Flag 뒤에 오는 `50 4B 03 04`를 찾음. ZIP File이나 그 기반 파일들의 Signature Header

![](/assets/HCTF/so_easy-4.png)

해당 부분 ZIP File 로 저장 시, 위와 같은 `hidden.txt` 확인 가능 및 Flag 발견

# 후기
사실 이 직전에 Welecome_Forensics 문제도 풀었지만 이는 설명 할 것이 없어 생략 하였다. 아직까지는 처음 푸는 Forensics 문제들이 무척 재미있다. 특히 일부 과감한 키워드로 검색을 하여도 문제 해결법이 나오지 않는 다는 점이 무척 마음에 든다.