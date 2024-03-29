---
title:  "FAT32 시스템 파일 복구 실습"
categories:
    - Forensics
tags:
    - fat32
    - fat16
    - fat
    - file
    - recovery
published: true
toc: true
---
FAT32 시스템 파일 확인 및 복구 실습

안녕하세요. 본 글은 필자의 공부 및 정리 글 입니다. 정확하지 않은 정보가 포함되어있을 수 있으며 해당 내용 발견 시 언제나 피드백 환영 및 감사 합니다.

# 1. FAT32 파일 시스템이란?
FAT32을 알기 위해서는 상위 범주를 알아야 한다. File Allocation Table (파일 할당 테이블, 이하 FAT) 파일 시스템의 종류 중 하나 이다.

1977년 Microsoft Basic에서의 파일 관리를 위해 만들어졌으며 1980년 86-DOS 에 처음 탑제 되었다. 크게 본다면 연결 리스트를 통해 파일을 관리하고, 테이블 구조로 디렉토리 내용을 관리 한다.

FAT32는 Windows 95 OEM Service Release 2 때 처음 도입된 2GB 이상의 디스크를 지원하면서도 파일 하나당 4095Gigabyte (4GB-1)까지의 크기를 지원 한다.

가장 큰 개선 점은 클러스터의 크기가 실제 물리 디스크의 크기에 따라 바뀜으로 인해 이전 버전보다 효율적으로 디스크 관리를 한다는 것이다.

## 1-1. FAT 시스템의 장점
현존하는 대부분의 OS에서 지원을 하므로 범용성이 무척 좋다. 서로 다른 OS간 파일을 주고 받거나 Legacy OS를 위해서도 사용이 가능하다.

또한 FAT 표준이 하위호환을 보장하며 확장되었으므로 호환 문제도 적다. 단순한 파일 시스템 구조 덕분에 안정성도 매우 뛰어나다.

## 1-2. FAT 시스템의 단점
FAT 시스템의 가장 큰 단점은 비효율적인 구조와 현 시대의 대용량 파일을 관리하는 것이 힘들다는 것이다.

필자 역시 이와 같은 문제를 [Windows 설치 파일 4GB 미만으로 압축하여 FAT32에 넣기](https://minnote.net/팁_tip/Split-Install/) 글에서 언급한 적이 있다.

본 글은 실습을 위주로 작성되었으므로 더 자세한 내용은 생략 하겠다.

# 2. 실습 환경 준비
## 2-1. 환경 안내

|Name|Config|
|:---:|:---|
|CPU|Apple M1|
|Architecture|ARM64|
|OS|Microsoft Windows 11 on ARM|
|Tools|- Mh-Nexus HxD Editor|

또한 1GB 정도 가량의 FAT32 파티션을 생성 하였다.

![](/assets/2022-03-27-FAT32-File-Recovery/1.png)

## 2-2. 예시 파일 준비

|Name|Status
|:---:|:---|
|exam.zip|Exist|
|example_jpg.jpg|Deleted|
|qwer.jpg|Exist|

![](/assets/2022-03-27-FAT32-File-Recovery/2.png)

# 3. 실습 요약 이론
![](/assets/2022-03-27-FAT32-File-Recovery/3.png)   
[Design of the FAT file system](https://en.wikipedia.org/wiki/Design_of_the_FAT_file_system)

FAT 32 시스템은 크게 4개의 영역으로 구성되어 있다.

- Reserved Sector (Boot Sector, FAT SYSTEM Information Sector and etc. )
- FAT Area (FAT #1, FAT #2, ...)
- Root Directory
- Data Area

우리는 Reserved Sector 에서 디스크에 대한 구조를 파악하고, FAT Size 와 Root Directory를 통해 파일을 찾으려 한다.

다른 것 보다 금일 실습에서 보아야 할 곳들을 형광팬으로 표기 하였다.

```
FAT Area = Reserved Sector Count + MBR Size = 4110 + 0 = 4110
Root Directory = FAT Area + FAT SIZE 32 = 4110 + 2041 = 6151
Data Area = Fat Area + (FAT SIZE 32) * 2) = 4110 + 2041 * 2 = 8192
```

# 4. Data Area 탐색
이로서 데이터 복구 실습에 중요한 요소들이 다 모였다. 우리의 데이터는 모두 `Data Area`에 있으므로 해당 위치인 8192 섹터로 이동해 보겠다.

![](/assets/2022-03-27-FAT32-File-Recovery/4.png)

해당 위치로 가본다면 디스크에 내용물을 알 수 있다. 가장 먼저 `NEW VOLUME` 이라는 환경 구성에서 만든 디스크의 이름을 볼 수 있다.

이외에도 FAT32 + Windows 시스템에서 기본적으로 만드는 일부 파일 및 디렉토리가 존재하지만 우리가 찾아야 할 파일들도 보인다.

FAT32 Data Area 에서는 한 파일당 대체로 32바이트를 할당하여 정보를 저장한다. 왜 대체로 인지는 `4-2절` 에서 설명한다.

핑크색의 `exam.zip` 와 회색의 `exmap~1.jpg`를 예시로 들어보겠다.

우선 저 둘의 차이가 보이는가? 가장 먼저 눈에 띄는 것은 파일 이름이 이상하다는 것이다.

## 4-1. 파일 이름 관련
FAT32 에서는 `example_jpg.jpg`와 같이 **삭제** 한 파일은 파일 명 맨 앞글자를 `å (0xE5)` 로 바꾸어 둔다. OS 상에서는 지워진 파일 이지만, 실제 디스크엔 데이터와 정보가 남아 있는 것이다. 이를 이용해 파일 복구가 가능하다. 이와 관련된 내용은 아래에서 더 설명 하겠다.

또한 파일 이름이 정상적이지 않다. FAT32 에서는 파일 이름 정보를 Name Offset 영역에 다 넣어야 하므로 파일 이름을 `~`를 통해 축소하여 저장한다. 이로 인하여 파일 복구 시 완벽한 이름을 복구 할 수 없다. (혹은 필자가 배운 선에서는 아직 불가능하다)

확장자 역시 뒤에 적혀 있지만 이는 바뀔 수 있는 정보라고 한다. 이로 인하여 아래에서 배울 데이터 추출 시 Signature Header나 데이터를 보고 확장자를 다시 맞추어보아야 한다.

## 4-2. 8글자가 넘어가는 파일 이름
방금 바로 위에서 파일 이름이 길 경우 축소된다는 것을 언급 하였다. 대체로 32바이트에 정보를 저장하지만 파일 이름이 긴 경우 32비트를 한번 더 써서 정보를 이어 저장한다.

회색 형광팬 영역을 보자면 `0x4000AB`에 `0F` 라는 데이터가 담겨 있다. `0x4000AA` 까지는 파일 이름이 담겨 있기 때문. 파일 이름 바로 뒤에 `0F`가 오는 경우 "**파일 이름이 너무 길어서 32비트 뒤에 확인해봐**" 라는 의미라고 한다.   
그러므로 `0x4000CB`로 이동을 하면 여기에도 `0F`가 있다. 이번에도 다 담지 못하였으므로 32비트를 더 이동한다.

그러면 이 곳에 우리가 찾던 `exampl~1.jpg`의 이름 정보가 있다!

## 4-3. 파일 크기
파일 크기는 파일 정보에 할당된 32바이트 중 가장 끝단 4바이트에 담겨 있다.

예시로 `exam.zip`의 경우 `0xB4` 이므로 180 바이트다. `qwer.jpg`의 경우 `0xD2A4` 이므로 53,924 바이트 (대략 52.6KB) 인 것 이다.

이 파일 크기를 알아야만 추후 파일 추출이 가능 하다.

## 4-4. Hi Cluster & Low Cluster
파일 정보 중 Hi Cluster 와 Low Cluster 가 있다. 이는 사실 Starting 이 앞에 붙어 있는 클러스터 정보 이다.

파일이 위치한 시작 클러스터의 각각 상, 하위 2바이트를 의미 한다. 우리가 지운 파일인 `exampl~1.jpg`를 기준으로 보자면

|Hi Cluster|Low Cluster|
|:---:|:---:|
|00 00|01 6A|

위와 같다. 이를 이용하여 이제 `5절`에서 파일 복구를 해보겠다.

# 5. 파일 삭제 및 복구 (추출)
사실 단순 파일 복구는 위에서 배운 Name Offset 영역에서 앞글자를 `å (0xE5)`가 아닌 추정되는 파일 이름으로 고칠 경우 복구가 된다. 파일 시스템이 지운 파일이 아니라고 인식을 하기 때문. 파일 삭제의 경우는 반대로 앞글자를 고쳐주면 된다.

## 5-1. 파일 이름을 통한 파일 삭제 및 복구
먼저 한번 해보겠다.

![](/assets/2022-03-27-FAT32-File-Recovery/5.png)

위 사진 처럼 멀쩡한 `qwer.jpg` 파일의 앞글자를 `0xE5`로 바꿔 지운 파일 처럼 인식 시키고 저장을 하였다.

![](/assets/2022-03-27-FAT32-File-Recovery/6.png)

그럴 경우 위와 같이 있던 `qwer.jpg`가 없어졌다. 반대로 복구의 경우에도 한번 해보길 권장한다.

## 5-2. 파일 데이터 추출을 통한 복구
이번에는 파일 데이터를 추출하여 복구를 해보려 한다. 위에서 알게된 Starting Hi/Low Cluster 를 통해 실제 파일의 데이터 위치를 알 수 있다.

위의 지운 파일인 `qwer.jpg` 의 데이터를 복구 하여보자.

```
파일 데이터의 시작 위치
= Data Area + (("HI Cluster" + "Low Cluster") - Root Directory Cluster) * 8
= 8192 + (0x00010178 - 2) * 8
= 8192 + (65910 * 8) = 535472
```

중간에 주의 할 것이 `("Hi Cluster" + "Low Cluster")` 부분이다. 이는 덧샘 연산이 아닌 이어 붙이는 연산이다.

예시로 위에서는 `0x0001`과 `0x0178`을 이어 붙여 `0x00010178`이 되어 10진수로 변환시 `65910` 라는 값을 얻은 것 이다.

이제 데이터의 시작 위치로 가보자.

![](/assets/2022-03-27-FAT32-File-Recovery/7.png)

우리가 흔히 본 JPG의 Signature Header 가 보인다. 제대로 찾아 온 것이고, 파일 확장자가 jpg가 맞았다.

![](/assets/2022-03-27-FAT32-File-Recovery/8.png)

이제 Hex Editor의 `Select Block` 기능을 이용하여 파일 크기만큼 블록을 지정하여 복사하고, 해당 블록을 새 파일에 저장하여 확장자를 jpg로 설정을 한다면 파일이 복구 된다. 필자는 10진수로 메모를 해두었으므로 `Length`를 `Dec` 값으로 `53924` 만큼 블록을 지정해 저장 하겠다.

![](/assets/2022-03-27-FAT32-File-Recovery/9.png)

파일 이름까지 맞춘다면 완벽하지만 필자는 구분을 위해 `방금 저장했음.jpg`로 하였다.

# 6. 응용
그렇다면 해당 파일의 시작 지점에 가서 파일 크기만큼 `00` 으로 덮어 씌운다면? 복구가 불가능한 파일이 된다.

이 작업을 디스크 전체에 행한다면 이것은 `Low Format` 이라 불리우는 모든 데이터를 날려 복구가 불가능하게 하는 Format 이다.

# 7. 후기
역시 혼자 하는 것은 쉽지만 남에게 설명 하는 것은 늘 어려운 일이다. 이것이 되야지 완벽한 내 머리 속에 들어온 정보 이지만 아직 부족한 것 같다. 설명하지 못하는 것들이 많다.

지속적으로 글을 추가 및 수정 해야겠다.