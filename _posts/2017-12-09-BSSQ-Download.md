---
title:  "BSSQ 메인보드 LU6500 / SU880 / KU8800 관련 자료 모음 (Custom rom/kernel/Root Kit for BSSQ device)"
categories:
    - iCAROS7 Works
tags:
    - bssq
    - 옵티머스q2
    - 옵티머스q
    - 옵티머스ex
    - lu6500
    - star
    - su880
    - ku8800
    - lu2300
    - Optimus
redirect_from:
    - /249
---
LG 옵티머스Q2/EX (LG-LU6500/SU880/KU880) bssq 보드 관련 자료 모음집

# 한국어
**English Version available on end of post**  
블로그에는 해외 BSSQ 폰 사용자들이 유입이 있어 영문 버전도 같이 기재합니다.

## 다운로드 서버
1. [https://drive.google.com/open?id=0BzVgoYUbLRYAbGlZWGtuaTRzYkE](https://drive.google.com/open?id=0BzVgoYUbLRYAbGlZWGtuaTRzYkE)
2. **[http://bssq.minnote.net](http://bssq.minnote.net)** (비밀번호 : dlzkfhtm / 한국어로 치면 이카로스 입니다) (Powered by harukasan)

{% include ad_content.html %}

해외망이 느리시다면 2번 이용하시면 좋습니다.  
둘다 사용하시는 인터넷 회선이 기가비트급 이상 이시라면 최대 80MB/s 까지 빠른 속도로 다운로드 가능합니다.

모든 파일은 존재하지 않습니다. 주로 제 관련 파일 중 일부가 존재합니다.  
지인분이 소장하고 계신 파일도 있다는데 받는데로 같이 올려두겠습니다.  
여기 목록에 없는 소장하고 계신 자료들 [hominlab@gmail.com](mailto:hominlab@gmail.com) 으로 행아웃이나 메일 주시면 정말 감사하겠습니다...!

디렉토리 별 정리 파일용도 간략한 입니다. 정확하지 않을 수 있습니다.  
사용 설명등은 네이버 옵티머스EX/Q2 카페 내 글 혹은 본 블로그 글 찾아 참고해주시면 정말 감사하겠습니다.

아래에서 언급되는 닉네임은 제 편의를 위해 기타 다른 정보를 제거하였습니다.
**베르제브(ph4913@naver.com)**님 / **백원만(jungil0502@naver.com)**님 정말 죄송합니다!
그 외에도 파일엔 도움을 주셨는데 별도로 기재되지 않은 모든 분들께 머리 숙여 사과드립니다.
별도로 첨언이 되지 않은 파일은 아마...제가 만들거나 수정한 파일일껍니다.

설명앞에 @가 붙은 파일은 리커버리에서 사용가능한 업데이트zip 파일 입니다.
설명에서 도구라 함은 윈도우 환경에서 작동되는 실행/배치 파일 입니다.
설명에서 ICS라 함은 아이스크림샌드위치 4.0을 뜻합니다.

많이들 찾으실 것 같은 자료는 **굵게** 넣었습니다.

| 경로                   | 파일 이름                                 | 설명                                                                                                                                             |
|------------------------|------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| \BSSQ                  |                                                | 최상위 루트입니다. 옵티머스Q2/SKT EX/KT EX 구분 없이 사용 가능한 파일들                                                                          |
|                        | **Icaros BSSQ One Tool Box.zip**               | 옵티머스Q2/EX ICS펌웨어 공용 루팅툴                                                                                                              |
|                        | **icaros_vold_patch_enabler.zip**              | @ ICS펌웨어 내장패치(외장SD카드 경로와 내장SD카드 경로 맞바꾸기) 활성화 파일                                                                     |
|                        | icaros_vold_patch_disabler.zip                 | @ 위 업데이트의 비활성화 업데이트                                                                                                                |
|                        |                                                |                                                                                                                                                  |
| \BSSQ\LU6500           |                                                | LG 옵티머스Q2 전용 파일                                                                                                                          |
|                        | LG_GB_KeyBoard.zip                             | 옵티머스Q2 128펌웨어에 있던 키보드.131/137펌웨어 사용가능. apk는 일반적 설치하시고, lib는 /system/lib에 퍼미션 맞게 넣어주세요.                  |
|                        |                                                |                                                                                                                                                  |
| \BSSQ\LU6500\롬        |                                                | LG 옵티머스Q2 전용 롬 파일                                                                                                                       |
|                        | **cm7wifi.zip**                                | @ 베르제브님의 CM7 마지막 빌드본                                                                                                                 |
|                        | **cm-unofficial-lu6500-Alpha01.zip**           | @ CM10 알파1 입니다. 최종 빌드본 아님.                                                                                                           |
|                        | framework-res.apk_cm10쿼티인식X.apk            | CM10에서 쿼티키보드를 비활성 파일. framework-res.apk 로 이름 바꾸시고 /system/framework 에 퍼미션 맞게 넣으시면 됩니다.                          |
|                        | **icaros_officical_lu6500_Modx_2.1_Stable.zip* | @ 137펌웨어 기반 커스텀롬                                                                                                                        |
|                        | ICS137_rooted_update.zip                       | @ 137펌웨어 순정에 루팅만 한 롬                                                                                                                  |
|                        | LeWaOS_LU6500.zip                              | @ CM7 기반으로 포팅했던 롬. 포팅하고 부팅까지 했던 기억나네요.                                                                                   |
|                        | MIUI.us_lu6500_2.3.16_Kor_Deo_ZipA_Signed.zip  | @ CM7 기반으로 포팅했던 롬. 부팅여부는 모르겠습니다.                                                                                             |
|                        | Provision4_unofficial_lu6500_alpha02.zip       | @ CM10기반 프로비전4 알파2 롬.                                                                                                                   |
|                        | run.cmd_Provision4.cmd                         | 프로비전4 알파1이라는 별도의 설치파일이 존재하는 이전 버전에서 run.cmd 중 일부 문제를 해결했다고 적혀있네요. 위 프로비전4 알파2와는 무관한 파일. |
|                        |                                                |                                                                                                                                                  |
| \BSSQ\LU6500\루팅툴    |                                                | LG 옵티머스Q2 전용 루팅툴                                                                                                                        |
|                        | Berrzebb OptimusQ2 Tools.zip                   | 베르제브님의 도구. ZIP파일인데 어디에 쓰였는지 모르겠습니다.                                                                                     |
|                        | icaros half root.zip                           | 137펌웨어에서 구 옵티머스Q2라이프 카페의 한 분의 불필요 앱 리스트를 참고해 만들었던 루팅을 하지 않고 시스템 앱을 지우는 도구                     |
|                        | Icaros One Windows 1.3.zip                     | 128펌웨어 루팅 도구                                                                                                                              |
|                        | LOQHLG.zip                                     | 131펌웨어 루팅 도구                                                                                                                              |
|                        | one power v1.3.1.zip                           | 137펌웨어 루팅 도구 (위 BSSQ 폴더 내의 공용 루팅툴을 더욱 추천드립니다)                                                                          |
|                        |                                                |                                                                                                                                                  |
| \BSSQ\LU6500\리커버리  |                                                | LG 옵티머스Q2 전용 리커버리 관련                                                                                                                 |
|                        | ics_recovery_v61.img                           | CWM리커버리 6.0...몇 버전 q2용 이미지 입니다. nvflash 로 플래싱 해주세요.                                                                        |
|                        | **oq_cwm_v6.1.zip**                            | 위 리커버리 설치 도구 입니다.                                                                                                                    |
|                        | **q2recovery.apk**                             | Q2 리커버리 진입 어플리케이션.                                                                                                                   |
|                        |                                                |                                                                                                                                                  |
| \BSSQ\LU6500\커널      |                                                | LG 옵티머스Q2 전용 커널들                                                                                                                        |
|                        | cmicaroskernel_build20_lu6500_update03.zip     | 제 커널 구버전의 CM10용 커스텀 버전                                                                                                              |
|                        | **icaros_extrakernel_ghost_touch.zip**         | 제 커널 구버전의 터치드라이버 제외 버전 (터치 안됨. 귀신터치가 너무 심하셔서 실사용이 불가능 하신 분들이 물리 자판으로 사용하시고 싶으실때 용)   |
|                        | icaroskernel_build10.zip                       | 제 커널 구버전                                                                                                                                   |
|                        | icaroskernel_build20_lu6500_update04.zip       | 제 커널 구버전                                                                                                                                   |
|                        | icaroskernel_official_lu6500_update05.zip      | 제 커널 최신 버전...일껍니다. 제 기억에는요.                                                                                                     |
|                        | 백원만_1.4Ghz.rar                              | 백원만님의 128펌웨어용 1.4Ghz 오버클럭 커널 일껍니다. 137인지 가물가물 하네요.                                                                   |
|                        |                                                |                                                                                                                                                  |
| \BSSQ\LU6500\테마      |                                                | LG 옵티머스Q2 전용 테마들                                                                                                                        |
|                        | icaros optimus ui 2.5 beta 1.zip               | LG UI 3.0을 128펌웨어용으로 제작하다가 접은 테마. 직접 하나하나 권한 맞게 넣어주셔야 합니다.                                                     |
|                        | icaros_orange_v8.1.zip                         | @ 137펌웨어용 제가 좋아했던 오렌지색으로 떡칠하고 밝기 슬라이더를 추가한 테마.                                                                   |
|                        | icaros_orange_v8.zip                           | @ 위와 같은데 제 기억에는 밝기 슬라이더 위치가 서로 다릅니다.                                                                                    |
|                        |                                                |                                                                                                                                                  |
| \BSSQ\LU2300_옵티머스Q |                                                | (2번 서버 한정 업로드) LG 옵티머스Q 관련 파일들                                                                                                  |
|                        | LU230058_00.kdz                                | 프로요 58펌웨어 Fastboot 활성 KDZ입니다. 안드로이더스의 한 분의 파일 입니다.                                                                     |
|                        | LU230057_00.kd                                 | 프로요 57펌웨어 순정 KDZ                                                                                                                         |


# English
**한국어는 본 페이지 상단에 위치해 있습니다**
I'm so sorry for bad english.

## Downlaod Server
1. https://drive.google.com/open?id=0BzVgoYUbLRYAbGlZWGtuaTRzYkE
2. https://nws.harukasan.org:5001/sharing/nj6GegyxO (Password : dlzkfhtm)

It's not ALL FILE of LU6500/SU880/KU8800.

Description by directory

Some people at the bottom, are as follows  
**베르제브 ph4913@naver.com**  
**백원만 jungil0502@naver.com**  
Also didn't memory many people who give me help  
Unless otherwise noted thats my works.  

@ Mark in description means, It's a update script zip. You can flash on Custom Recovery.  
'Tool' in description means, it's a Windows Batch/Execute tool kit.  
'ICS' in description means, IceCreamSandwitch Android 4.0.  

**Bold on often find file.**  

| Path                   | File Name                                      | Description                                                                                                                                    |
|------------------------|------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| \BSSQ                  |                                                | root dir. common file for LG Optimus Q2 (LU6500) / EX (SU880/KU8800)                                                                           |
|                        | **Icaros BSSQ One Tool Box.zip**               | LG Optimus Q2 & EX common ICS firmware root tool                                                                                               |
|                        | **icaros_vold_patch_enabler.zip**              | @ zip for ICS firmware Internal Storage Patch(Swap Internal SD and External SDcard) enable                                                     |
|                        | icaros_vold_patch_disabler.zip                 | @ disabler for Upper                                                                                                                           |
|                        |                                                |                                                                                                                                                |
| \BSSQ\LU6500           |                                                | File only for LG Optimus Q2 (LU6500)                                                                                                           |
|                        | LG_GB_KeyBoard.zip                             | Korean Keyboard that on 128(2.3 Firmware) for other Firmware (like 137). apk file => install / lib => push /system/lib with collect permission |
|                        |                                                |                                                                                                                                                |
| \BSSQ\LU6500\롬        |                                                | ROM only for LG Optimus Q2 (LU6500)                                                                                                            |
|                        | **cm7wifi.zip**                                | @ 베르제브's last CM7 build file                                                                                                               |
|                        | **cm-unofficial-lu6500-Alpha01.zip**           | @ CM10 Alpha 01. it's  not last version                                                                                                        |
|                        | framework-res.apk_cm10쿼티인식X.apk            | CM10's framework-res.apk for disable hardware keyboard                                                                                         |
|                        | **icaros_officical_lu6500_Modx_2.1_Stable.zip* | @ my custom rom based on ICS 137 firmware                                                                                                      |
|                        | ICS137_rooted_update.zip                       | @ rooted ICS 137 stock firmware                                                                                                                |
|                        | LeWaOS_LU6500.zip                              | @ Bootable LeWaOS Based on CM7                                                                                                                 |
|                        | MIUI.us_lu6500_2.3.16_Kor_Deo_ZipA_Signed.zip  | @ MIUI based on CM7. I don't know boot availability                                                                                            |
|                        | Provision4_unofficial_lu6500_alpha02.zip       | @ Provision 4 based on CM10 Alpha 02                                                                                                           |
|                        | run.cmd_Provision4.cmd                         | fix run.cmd for Provision 4 Alpha 01 Installer. It's not for Alpha 02!                                                                         |
|                        |                                                |                                                                                                                                                |
| \BSSQ\LU6500\루팅툴    |                                                | Root Tool only for LG Optimus Q2 (LU6500)                                                                                                      |
|                        | Berrzebb OptimusQ2 Tools.zip                   | 베르제브's tool. But I don't know what it is                                                                                                   |
|                        | icaros half root.zip                           | Remove some system app without root for 137 F/W                                                                                                |
|                        | Icaros One Windows 1.3.zip                     | Root kit for GingerBread 128 firmware                                                                                                          |
|                        | LOQHLG.zip                                     | Root kit for unofficial leaked ICS 131 firmware                                                                                                |
|                        | one power v1.3.1.zip                           | Root kit for ICS 137 firmware. (not recommend. recommend \BSSQ\Icaros BSSQ One Tool Box.zip)                                                   |
|                        |                                                |                                                                                                                                                |
| \BSSQ\LU6500\리커버리  |                                                | Recovery file only for LG Optimus Q2 (LU6500)                                                                                                  |
|                        | ics_recovery_v61.img                           | Clockworkmod Recovery 6.0.x.x for LU6500. need nvflash                                                                                         |
|                        | **oq_cwm_v6.1.zip**                            | ics_recovery_v61.img installer tool                                                                                                            |
|                        | **q2recovery.apk**                             | Easy boot into recovery application for LU6500                                                                                                 |
|                        |                                                |                                                                                                                                                |
| \BSSQ\LU6500\커널      |                                                | Kernel only for LG Optimus Q2 (LU6500)                                                                                                         |
|                        | cmicaroskernel_build20_lu6500_update03.zip     | Old version of my kernel for CM10                                                                                                              |
|                        | **icaros_extrakernel_ghost_touch.zip**         | Old version of my kernel without touch driver (only for guys who suffer by ghost touch. this kernel disable your touch screen)                 |
|                        | icaroskernel_build10.zip                       | Old version of my kernel                                                                                                                       |
|                        | icaroskernel_build20_lu6500_update04.zip       | Old version of my kernel                                                                                                                       |
|                        | icaroskernel_official_lu6500_update05.zip      | Least version of my kernel. May be...                                                                                                          |
|                        | 백원만_1.4Ghz.rar                              | 백원만's 1.4Ghz Overclock kernel for 128 firmware. May be for 128...may be...                                                                  |
|                        |                                                |                                                                                                                                                |
| \BSSQ\LU6500\테마      |                                                | Themes only for LG Optimus Q2 (LU6500)                                                                                                         |
|                        | icaros optimus ui 2.5 beta 1.zip               | Incomplete theme. LG UI 3.0 for 128 Firmware.                                                                                                  |
|                        | icaros_orange_v8.1.zip                         | @ Orange color and bright control slider theme for 137 firmware                                                                                |
|                        | icaros_orange_v8.zip                           | @ It's same theme upper that. But difference bright control slider layout                                                                      |
|                        |                                                |                                                                                                                                                |
| \BSSQ\LU2300_옵티머스Q |                                                | (Only available server 2) File for LG Optimus Q (LU2300)                                                                                       |
|                        | LU230058_00.kdz                                | Froyo 58 firmware kdz with Fastboot. someone's file from http://cafe.naver.com/androiders                                                      |
|                        | LU230057_00.kd                                 | Froyo 57 stock firmware kdz                                                                                                                    |

# 체인지로그 / ChangeLog
## 17-12-09 21:25
- 2번 서버 주소 리디렉션 / Redirection server 2 url

## 19-01-07
- 디렉토리 목록을 테이블화 / Make a dir list via table