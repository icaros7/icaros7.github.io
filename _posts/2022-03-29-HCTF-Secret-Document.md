---
title:  "[HackCTF] Secret Document 풀이"
categories:
    - Forensics
tags:
    - ctf.j0n9hyun.xyz
toc: true
published: true
---
[HackCTF] Secret Document 풀이

# Environments

|Name|Config|
|:---:|:---|
|CPU|Apple M1|
|Architecture|ARM64|
|OS|Microsoft Windows 11 on ARM|
|Tools|- Mh-Nexus HxD Editor<br/>- Bandisoft Bandizip|

# 문제
URL: [https://ctf.j0n9hyun.xyz/challenges#Secret%20Document](https://ctf.j0n9hyun.xyz/challenges#Secret%20Document)

![](/assets/HCTF/secret-docu-0.png)

목표: `flag.zip` 으로부터 `flag.txt` 를 압축 풀어 Flag 찾기

접근 방법: ZIP으로 압축된 파일의 Flag 확인하기

# 풀이
정상적인 `zip` 파일 인 것은 해당 파일을 열어봄으로서 확인 하였다. 이 중 `flag.txt`는 암호가 걸려 있어 `*` 가 파일 명 뒤에 붙어 있는 것을 Bandizip으로 확인. Hint를 보면 재미 없을 것 같아 과감하게 생략

사실 접근 방법 자체는 예전 이 관련 세미나를 진행을 하려고 시도 하였던 같은 동아리분이 언질을 해줌

> ZIP파일 에서는 숫자 두개만 고쳐도 암호가 실제로는 없는데 요구를 한다

이 말이 기억이 나서 해당 방법으로 접근 시도. 우선 zip 파일의 구조를 알아야 하므로 [Wikipedia ZIP (file format)](https://en.wikipedia.org/wiki/ZIP_(file_format))을 읽어 파일 구조 파악

`flag.txt`의 Central directory file header 에서 flag 상태를 봐야 하므로 해당 파일의 Central directory file header를 검색

![](/assets/HCTF/secret-docu-1.png)   
`flag.txt` Central directory file head 상의 8번째 Offset인 `General purpose bit flag`를 확인 하면 `0x0009` 임을 확인

![](/assets/HCTF/secret-docu-2.png)   
[https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT](https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT) 문서 상 4.4.4 절을 확인 시 `09`는 사용되지 않는 Flag 인 것을 확인

![](/assets/HCTF/secret-docu-3.png)   
정상적인 암호화가 되지 않은 임의의 `zip` 파일을 만들어 파일의 `Genernal purpose bit flag` 확인 시 `0x0000` 인 것 확인

![](/assets/HCTF/secret-docu-4.png)   
정상적인 `Gerneral purpose bit flag` 로 설정 시 `flag.txt` 를 정상적으로 읽을 수 있음

추가로 혹시 몰라 진짜 암호화 된 파일의 `Gerneral purpose bit flag`를 바꾸어 보았으나 다행히(?) `CRS` 오류가 나거나 정상적으로 압축이 풀리지 않음.

# 후기
가벼운 마음으로 풀기 시작 했는데 초반에 동아리 부원분의 조언이 크게 도움이 되었다.