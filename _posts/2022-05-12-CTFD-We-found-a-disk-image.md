---
title:  "[CTF-d] 저희는 디스크 이미지를 찾았습니다 풀이"
categories:
    - Forensics
tags:
    - ctf-d.com
toc: true
published: true
---
[CTF-d] 저희는 디스크 이미지를 찾았습니다. 풀이

# Environments

|Name|Config|
|:---:|:---|
|CPU|Intel Core i7 7700|
|Architecture|Intel64|
|OS|Microsoft Windows 11|
|Tools|- Mh-Nexus HxD Edito|

# Problem
URL: [ctf-d.com/challenges#저희는%20디스크%20이미지를%20찾았습니다.](http://ctf-d.com/challenges#저희는%20디스크%20이미지를%20찾았습니다.)

목표: `disk.img` 로부터 Flag 찾기   
접근 방법: 이미지 HEX 값 Reading 으로부터 유추 가능한 파일 찾기

# Theory Summary
잠시 JFIF 파일 구조에 대해 간략하게 짚고 넘어가겠다.

|Hex Value|Description|
|:---:|:---|
|`FF D8`|**SOI**. Start Of Image. JFIF 파일의 시작을 알림|
|`FF E0 ... 4A 46 49 46 ...`|JFIF APP0 영역. **JFIF** 라는 값을 포함. 썸네일도 포함 가능|
|`FF DA`|**SOS**. Start Of Scan. 스캔 데이터 영역|
|`...`|압축된 이미지 데이터 영역|
|`FF D9`|**EOI**. End Of Image. JFIF 파일의 끝을 알림|

여기서 Fake Signature header가 아닌 것은 APP0 Segment 를 보면 알 수 있다.

|Name|Size|Description|
|:---:|:---:|:---|
|Maker|2|APP0의 시작을 알림|
|Length|2|APP0 Maker 를 제외한 Segment 영역의 크기|
|Idenitfier|5|`JFIF`의 아스키 코드를 포함.<br>`00` 으로 끝남|
|Version|2|JFIF의 버전을 뜻함. 첫번째 바이트가 앞, 두번째 바이트가 뒷 버전.<br>`01 01`의 경우 1.01|
|Units|1|픽셀 밀도의 단위.<br>`00`: 유닛이 없으며 픽셀 값 그 자체.<br>`01`: 인치당 픽셀<br>`02`: cm당 픽셀|
|Xdensity|2|X 축 픽셀 밀도. 절대 0이 될 수 없음|
|Ydensity|2|Y 축 픽셀 밀도. 절대 0이 될 수 없음|
|Xthumbnail|1|RGB 썸네일의 X축 픽셀 크기. 절대 0이 될 수 없음|
|Ythumbnail|1|RGB 썸네일의 Y축 픽셀 크기. 절대 0이 될 수 없음|
|Thum Data|3n|압축되지 않은 24bit RGB 썸네일. R G B 각각 하나씩 한 픽셀 데이터를 뜻하므로 3n|

# Solution
몇일 째 HackCTF가 Down 되어있다. 따라서 풀던 문제를 중단하고 CTF-D와 Dreamhack의 War Games를 포스팅 해보려 한다.

![](/assets/CTFD/We-found-a-disk-image-0.png)
우선 아예 문제에서 대놓고 Hint로 "**우리가 가는 곳에는 파일시스템이 필요 없습니다**" 라고 File System 구조적 접근을 하지마라고 알려주는 것 같다.

![](/assets/CTFD/We-found-a-disk-image-1.png)

`HxD Editor`를 통해 열어보는 경우 File System 에 대한 정보를 찾기 어렵긴하다. 문제를 풀고 난 뒤 덧붙이자면 아마 유추로는 `EXTended file system` Series System 인 것 같다는 유추만 간단히 하고 지나간다.

따라서 살아 남아 존재하는 파일 내용들을 읽어보기로 한다. 남아있는 Data는 모두 이미지 가장 끝단 쪽에 존재하였다. 따라서 아래에서 위로 따라 가기로 한다.

![](/assets/CTFD/We-found-a-disk-image-2.png)

가장 먼저 눈에 띈 것은 **읽을** 수 있는 구간 이었던 `EXIF`에 대한 Data 였다. 하지만 정상적인 Header 가 없으며 우리가 일반적으로 아는 구조로 이루어져 있지 않다.

![](/assets/CTFD/We-found-a-disk-image-3.png)

그렇게 더 거슬러 올라가면 찾을 수 있는 `JFIF` File Signature Header가 있다. `0xFF 0xD8`. 우선 정상적으로 Data 가 존재하는 파일임을 확인 하기 위해 End of Image 값인 `0xFF 0xD9` 값을 찾으면 역시 존재 한다. APP0 Segment 의 식별자인 `4A 46 49 00`도 존재하는 것을 미루어 보아 정상적인 JFIF File 인 것으로 추정.

SOI ~ EOI 까지의 Data 를 별도 저장시 Flag 를 흭득 가능 하다.