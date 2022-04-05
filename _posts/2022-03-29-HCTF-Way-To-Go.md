---
title:  "[HackCTF] 나는 해귀다 풀이"
categories:
    - Forensics
tags:
    - ctf.j0n9hyun.xyz
toc: true
published: true
---
[HackCTF] 나는 해귀다 풀이

# Environments

|Name|Config|
|:---:|:---|
|CPU|Apple M1|
|Architecture|ARM64|
|OS|Microsoft Windows 11 on ARM|
|Tools|- Mh-Nexus HxD Editor|

# Problem
URL: [https://ctf.j0n9hyun.xyz/challenges#나는%20해귀다](https://ctf.j0n9hyun.xyz/challenges#나는%20해귀다)

![](/assets/HCTF/waytogo-0.png)

목표: `waytogo.png` 으로부터 Flag 찾기

접근 방법: png format 비교

# Solution
![](/assets/HCTF/waytogo-1.png)
정상적인 `png` 파일 인 것은 이미지 뷰어와 PNG 파일 Signature Header로 확인 가능

우선 PNG 파일의 구조를 알아야 분석 가능. PNG 파일 구조는 JPG와 Stganography 와 함께 별도 글 분리.

![](/assets/HCTF/waytogo-2.png)

`49 45 4E 44 AE 42 60 82` 인 `IEND` 부분을 검색하면 PNG 파일 IEND Chunk 뒤로 Dummy data 존재 확인.

다만 재미 있는 것이 `49 45 4E 44 AE 42 60 82` 뒤로 바로 `82 60 42 AE 44 4E 45 49`가 있음.

![](/assets/HCTF/waytogo-3.png)

"**최고의 해커!**"

새로운 PNG 구조가 모두 역순으로 dummy data 화 되어 있음. 이를 해결하기 위해 C++로 `fstream`을 활용. [Simple Text Reverser](https://github.com/icaros7/simple_text_reverser) 참고.

```
/*
 * Simple Text Reverser
 *
 * Author: iCAROS7 (hominlab@gmail.com)
 * Date/Time: 2022.03.29 22:14
 */

#include <iostream>
#include <fstream>

int main(int argc, const char * argv[]) {
    std::ifstream is{ "input.txt" };
    std::ofstream os { "output.txt" };
    if (!is || !os) {   // 파일 열기 실패시
        std::cerr << "파일 오픈에 실패하였습니다." << std::endl;
        exit(1);        // 1과 함께 종료
    }

    is.seekg(0, std::ios::end);  // 커서 위치를 가장 끝으로 이동
    int num = is.tellg();   // 현재 커서 위치를 파일의 크기로 간주하고 저장
    char c;
    
    for (int i = num - 1; i >= 0; i--) {
        is.seekg(i, std::ios::beg);
        is.get(c);
        os << c;
    }
    return 0;
}
```

이 후 다시 재정렬 된 hex 값 저장시 flag 확인 가능