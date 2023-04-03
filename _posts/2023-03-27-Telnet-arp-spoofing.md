---
title:  "ARP Spoofing을 이용한 telnet 탈취"
categories:
  - Study
tags:
toc: true
published: true
---

ARP Spoofing 을 통해 `telnet`의 키 입력 가로채기

# 1. 서론

 우선 이 내용은 암호화되지 않은 평문 통신을 가로챈다는 가정으로 출발 한다. 우선 강의 중의 별도의 언급은 없지만 `Man-in-the-Middle` (MITM) 공격에 대해 간략하게 짚고 넘어간다.

## 1-1. Man-in-the-Middle Attack

> 네트워크 통신을 조작하여 통신 내용을 도청 혹은 조작 하는 기법

 간략히 정리하면 위와 같다. 호스트 A는 호스트 B에 직접적으로 연결 되어 있다고 생각을 하지만 실제로는 다음과 같은 상황일 때를 뜻한다.

<table>
  <tr>
    <th>상황</th>
    <th>연결 구조</th>
  </tr>
  <tr>
    <td>원하는 연결</td>
    <td>호스트A &lt-&gt 호스트B</td>
  </tr>
  <tr>
    <td>실제 상황</td>
    <td>호스트A &lt-&gt <b>중간자</b> &lt-&gt 호스트B</td>
  </tr>
</table>

이를 막기 위해서 여러가지 방어 기법이 존재하지만 여기선 짚지 않겠다.

## 1-2. Address Resolution Protocol (ARP)

> 논리적으로 할당된 Internet Protocol (IP) 주소를 기반으로 Media Access Control (MAC) 주소를 해석하는 프로토콜

쉽게 말해 IP주소로 MAC주소를 구하는 프로토콜이고, 반대의 경우 Reserve ARP (RARP) 이다. 간단한 예시를 들어보겠다. 

- 목적: 호스트A (`10.0.0.2`)는 라우터 상에 있는 호스트B (`10.0.0.3`)와 연결하고 싶다.

1. 호스트 A는 호스트B와 연결하고 싶어 네트워크 상 모든 호스트에게 `10.0.0.3`이 존재에 대해 broadcast 방식으로 질의 (**ARP Request**)
2. 게이트웨이 등 네트워크 장비는 본인이 알고 있는 MAC 주소 기록 테이블에서 `10.0.0.3`을 가진 호스트가 있는지 확인
   - 호스트 B의 MAC 주소를 알고 있다면, 호스트A에게 호스트 B의 MAC을 알려주고 통신을 시작
3. 호스트 B의 MAC 주소를 모른다면 네트워크 장비는 다시 호스트 B 까지 모두에게 질의를 시작
   - 호스트 B가 수신한 요청에 목적지 주소가 자신이라면, 자신의 MAC 주소를 포함하여 장비에게 unicast 방식으로 응답 (**ARP Reply**)
   - 어떠한 unicast 응답이 없다면 호스트 A에게 없다는걸 다시 알림
4. 얻은 ARP Reply 를 기반으로 통신을 시작하거나 중단

 여기서 중요한 점은 4번까지의 과정을 거쳐 호스트B의 MAC을 알았다면, 호스트A는 이와 같은 복잡한 과정을 다시 거치지 않기 위해 잠시동안 호스트B의 MAC 정보를 보관 한다. 이를 **ARP Cache**라고 한다. 이 ARP Cache 를 위조하여 호스트B로 연결하는 것이 아닌 MITM 에게 연결 하여 도청을 하는 것이다. (MITM은 다시 도청 이후 호스트B에게 데이터를 넘겨준다면 호스트A는 안심하고 계속 통신을 한다)

## 1-2. 통신 상황

<table>
	<tbody>
		<tr>
			<th>Attacker</th>
			<th>Target 1</th>
			<th>Target 2</th>
		</tr>
		<tr>
			<td>IP: 192.168.xxx.15<br>NIC: eth0<br>AA:BB:CC:DD:A0:AF</td>
			<td>192.168.xxx.17<br>NIC: eth0<br>AA:BB:CC:DD:10:B9</td>
			<td>192.168.xxx.18<br>NIC: ens33<br>AA:BB:CC:DD:88:50</td>
		</tr>
    <tr>
      <td><img src="/assets/2023-03-27-Telnet-arp-spoofing/0.png"/></td>
      <td><img src="/assets/2023-03-27-Telnet-arp-spoofing/1.png"/></td>
      <td><img src="/assets/2023-03-27-Telnet-arp-spoofing/2.png"/></td>
    </tr>
	</tbody>
</table>


 같은 네트워크 상에 존재하여 gateway는 속일 필요가 없다

## 1-3. 시나리오

1. 각 기기들은 서로의 MAC 주소를 caching
2. Attacker는 Target1 에게 Target2 의 MAC은 자신의 이더넷 장비의 MAC 주소라 속임
3. Attacker는 Target2 에게 Target1 의 MAC은 자신의 이더넷 장비의 MAC 주소라 속임
4. Attacker는 Target1 - Target2 간 통신을 정상 전달
5. Attacker는 통신 패킷 감청

# 2. 실습

> 본 실습은 동일 네트워크 상에 존재한다는 가정 하에 진행된다

## 2-1. ARP Spoofing

 우선 Target1 에게 "너가 caching 하고 있는 Target2 의 MAC 주소는 앞으로 Attacker의 eth0 MAC 이야" 라고 알린다. 이는 `arpspoof` 를 통해 진행된다.

```shell
$ arpspoof -i eth0 -t 10.200.xxx.17 10.200.xxx.18
```

 다음으로는 Target2 에게 동일한 메커니즘으로 caching 하고 있는 Target1의 MAC을 바꾼다.

```shell
$ arpspoof -i eth0 -t 10.200.xxx.18 10.200.xxx.17
```

 이제 이제 각 Target은 서로 간 통신을 원하는 경우 Attacker 를 향하게 되어있다. 우리는 이 둘이 연결 된 것 처럼 통신을 중계 하여야 한다. `fragrouter`를 통해 탐지 회피를 해보겠다. `-B1` 옵션의 경우 Normal IP forwarding 을 하겠다는 의미 이다.

```shell
$ fragrouter -B1
```

 이후 Wireshark 등을 이용하여 트래픽이 발생된 ARP 패킷을 보면 **< 그림 0 >**과 같이 잘 전송되고 있음을 확인 가능하다.

<img src="/assets/2023-03-27-Telnet-arp-spoofing/3.png" style="zoom:40%;" />

<center><b>&lt 그림 0 &gt</b></center>

 또한 Target 들은 `arp -a` 명령어로 caching 된 MAC을 보면 Attacker 의 MAC과 다른 Target 의 MAC 이 같은것을 **< 그림 1 >**과 같이 확인 가능하다.

<img src="/assets/2023-03-27-Telnet-arp-spoofing/4.png" style="zoom:40%;" />

<center><b>&lt 그림 1 &gt</b></center>

## 2-2. Traffic Generating

 다음으로는 탈취할 정보를 하나 만들어 보겠다. 본 실습에서는 Target의 계정과 비밀번호를 탈취해 보겠다. 따라서 일반 사용자가 `telnet` 서비스를 통해 정상적으로 로그인을 했다는 가정을 한다.

```shell
$ telnet 10.200.xxx.17
```

 그럼 Attacker의 wireshark 에서 telnet 패킷이 감지 된다.

- **< 그림 2 >**와 같이 `login: ` 혹은  `Password: `를 Target1이 Telnet을 통해 접속한 Target2에게 전달
- 이후 패킷은 모두 key event가 발생한 것을 Target1 에게 전달한 패킷 (실제 입력 값)
- **< 그림 3 >**과 같이 `return` 으로 끝나는걸 보아 계정 또는 비밀번호 입력이 완료 됬음을 인지

<table>
  <tr>
    <td><img src="/assets/2023-03-27-Telnet-arp-spoofing/5.png" style="zoom:45%;"></td>
    <td><img src="/assets/2023-03-27-Telnet-arp-spoofing/6.png" style="zoom:45%;"></td>
  </tr>
  <tr>
    <th><center>&lt 그림 2 &gt</center></th>
    <th><center>&lt 그림 3 &gt</center></th>
  </tr>
</table>

 단, 여기서 비밀번호 입력 이후 정상적으로 접속이 되었는지 까지 확인 해야한다. `incorrect` 등의 반환이 되었다면 사용자가 입력 실수를 했음으로 정상적인 비밀번호가 아님을 알 수 있다. 보통의 상황의 경우 shell 이 실행 될 것 이므로 해당 사항을 통해 알 수 있다.

# 3. 결론

 Backtrack 시절 간단한 실습을 했던 기억이 났다. 또한 Wireshark 가 아닌 더욱 더 편한 third-party 도구가 많으므로 해당 도구를 이용 하는 것이 편하다는 것을 마지막으로 실습은 끝났다.
