---
title:  "ETS2/ATS Telemetry 서버 한글판 - 유로트럭2 아메트럭 속도계"
categories:
    - iCAROS7 Works
tags:
    - ets2
    - eurotrucksimulator
    - ATS
    - 유로트럭_속도계 
    - speedometer
    - telemetry
redirect_from:
    - /252
---
다중 모니터 혹은 태블릿/스마트폰에 유로트럭 정보 띄우기

안녕하세요!  

유로트럭2을 오래 하셨던 분이라면 꼭 한번 쯤 보셨을 법한 [**@Funbit**](https://github.com/Funbit) 씨의 [**ETS2/ATS Telemetry Mobile Dashboard**](https://github.com/Funbit/ets2-telemetry-server) 앱이 한참 업데이트 되지 않고 있어, 얼마전부터 한국어와 몇몇 기능들을 더 넣어 보고 있습니다.  

개발자님이 pull request 도 안받으시는 것 보면 연락이 닿질 않는 것 같습니다. 6년전에서 업데이트가 중단되어...

하지만 SCS에서 더이상 유로아메용 Telemetry SDK를 업데이트해주지 않아 한계가 있지만 가능한 내에서는 최대한 가꾸어 보려합니다.

{% include ad_content.html %}

이로 인해 자가 트레일러와 스페셜 트렌스포트 DLC 이용시 제대로 표기가 안되는 상황들이 종종 있습니다.

그럴일은 없지만...해외에서 이용되면 번역 해 주신다면 앱에 추가 해보겠습니다. 아직 스킨이 다국어 지원을 안하고 한국어로만 뜹니다. 데스크탑 앱만 다국어를 지원하고 있습니다만 Javascript 역시 다국어를 지원은 하기에 한번 영어까지는 다시 추가하겠습니다.

### *저는 그게 뭔지 모르겠는데 무엇인가요?*
---

사진 몇장으로 요약 가능합니다!
![](/assets/2018-12-14-ETS2-Dashboard/1.jpg)![](/assets/2018-12-14-ETS2-Dashboard/2.jpg)![](/assets/2018-12-14-ETS2-Dashboard/3.jpg)  
급격한 컨셉잡느랴 힘들었네요ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ

네! 바로 유로트럭의 어드바이저나 계기판의 정보를 브라우저에서 볼수 있게 해줍니다. 듀얼모니터를 쓰시는 경우 바로 옆 모니터부터 스마트폰, 테블릿PC 여러군대 띄우실수 있습니다! 특히 작은 모니터를 이용하신다면 매우 좋을것 같습니다.


### *어떻게 설치하나요?*
---

이는 사실 제가 버전관리를 위해 github에 업로드 해 두고 있어 [해당 페이지](https://github.com/icaros7/ets2-kor-telemetry-server)에 상세히 설명서를 올려두었습니다.

ETS2/ATS Telemetry 웹 서버 한글판(새창)에 들어가시면 자세한 설치 방법부터 맘에들지 않을때 상황까지 염두해 두었습니다.
## [다운로드](https://github.com/icaros7/ets2-kor-telemetry-server)

### *무엇을 볼 수 있나요?*
---
![](/assets/2018-12-14-ETS2-Dashboard/dashboard.jpg)  
위가 가장 기본적인 스킨의 화면 입니다. 저는 이 기본 스킨을 목표로 업데이트를 하는 중 입니다.

- 속도계 (mph 지원)
- RPM
- 미터기
- 크루즈컨트롤
- 리타터
- 기어
- 좌우 방향지시등
- 주차 브레이크
- 기름 게이지 및 평균 연비 (소수점 두자리)
- 수온계
- 메인 에어 게이지
- 손상도
- 현 도로 속도 제한
- 시간, 출발, 목적지, 들어오는 수입, 남은 시간, 내비게이션 남은 시간, 도착 예상 시간
- 화물 및 차량 상태

정도가 있습니다. 원하신다면 직접 스킨을 수정하여 더 많은 정보를 띄을 수 있습니다.