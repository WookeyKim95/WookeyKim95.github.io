---
layout: post
title:  "[Embedded] 네트워크 IP 주소"
subtitle:  
date: 2023-11-02 03:41:40 +0900
categories: study
tags: Embedded
comments: true
related_posts:

---

# [Embedded] 네트워크 IP 주소<br/>
<br/>

## ARP<br/>
<Br/>

컴퓨터와 컴퓨터 간의 통신은 IP 주소에서 ARP를 통해 MAC 주소를 찾아서 MAC 주소를 기반으로 통신한다.<br/>

이런 통신을 위해서 ARP는 IP 주소로부터 MAC 주소를 구할 수 있도록 IP와 MAC 주소 사이에서 다리 역할을 한다.<br/>

즉, ARP를 통해 가상 주소인 IP 주소를 실제 주소인 MAC 주소로 변환한다.<br/>
반대로, RARP를 통해 MAC 주소를 가상 주소인 IP 주소로 변환한다.<br/>

예를 들어, 호스트 A가 호스트 B의 MAC 주소를 얻으려면 ARP request라는 특수 패킷을 브로드캐스팅해야 한다.<br/>
ARP request 패킷을 네트워크의 모든 호스트가 수신하지만, 관계없는 호스트는 패킷을 무시하고 호스트 B만 IP 주소가 자신의 IP 주소와 동일함을 인지한다. 따라서 호스트 B는 ARP reply 패킷을 사용해 자신의 MAC 주소를 호스트 A에 회신한다.

데이터를 전송할 때마다 ARP를 사용하여 패킷을 브로드캐스팅하면 네트워크 트래픽이 증가한다.<br/>
따라서 ARP를 사용하는 호스트에서는 가장 최근에 얻은 IP 주소와 MAC 주소 매핑 값을 보관하는 캐시 정보를 이용한다.<Br/>
또한 송신 호스트가 ARP request 패킷을 브로드캐스팅하는 과정에서 패킷을 수신한 모든 호스트는 송신 호스트 IP 주소와 MAC 주소 매핑 값을 자동으로 얻을 수 있다.

이와 같은 방식으로 ARP 트래픽에 의한 네트워크 부하를 최소화할 수 있다.<br/>

브로드캐스트 : 송신 호스트가 전송한 데이터가 네트워크에 연결된 모든 호스트에 전송하는 방식

참고 : [네이버 지식백과](https://terms.naver.com/entry.naver?docId=2271905&cid=51207&categoryId=51207)

<br/>

## 홉바이홉 통신<br/>
<Br/>

IP주소를 통해 통신하는 과정을 홉바이홉 통신이라고 한다.<br/>
여기서 홉이란 영어로 깡총깡총 뛰는 모습을 뜻하며, 통신망에서 각 패킷이 여러 개의 라우터를 건너가는 모습을 표현한 것이다.<br/>

컴퓨터 - 서브네트워크 - 라우팅 테이블 - 서브네트워크 - 라우팅 테이블 - .. - 컴퓨터

경로 형식으로 패킷을 전달한다.<br/>

즉, 통신 장치의 라우팅 테이블의 IP를 통해 시작 주소부터 시작하여 다음 IP로 계속해서 이동하는 라우팅 과정을 거쳐서 패킷이 최종 목적지 까지 도달하는 과정을 말한다.<br/>

<br/>

### 라우팅 테이블<br/>

라우팅 테이블이란, 송신지에서 수신지까지 도달하기 위해 사용되며 라우터에 들어가 있는 목적지 정보들과 그 목적직로 가기 위한 방법이 들어 있는 리스트를 말한다.<br/>
라우팅 테이블에는 게이트웨이와 모든 목적지에 대해 해당 목적지에 도달하기 위해 거쳐야할 다음 라우터의 정보를 가지고 있다.<br/>

<br/>

## IP 주소 체계<br/>
<Br/>

IP 주소는 IPv4와 IPv6로 나뉜다.<br/>

- IPv4 : 32비트를 8비트 단위로 점을 찎어 표기하며, 123.45.67.89와 같은 형식으로 표기한다.
- IPv6 : 64비트를 16비트 단위로 점을 찍어 표기하며, 2001:db8:ff00:42:8329 같은 형식으로 표기한다.

<br/>

## 클래스 기반 할당 방식<br/>
<br/>

대규모 네트워크가 늘어나는 상황에서 IP 주소 클래스가 등장하게 되었다.
한정된 자원인 IP주소를 체계적으로 나눠 쓸 필요가 있다.<br/>

![Alt text](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/_posts/study/Embedded/IP_1.png?raw=true)


### A 클래스<br/>

- 0.0.0.0 ~ 127.255.255.255 까지 를 A클래스라 한다. 
- 네트워크 주소는 1.0.0.0과 126.0.0.0까지로 규정되어 있다. 
- 호스트 갯수는 2^24 - 2

### B 클래스<br/>

- IP주소를 바이너리로 표시할 때 맨 앞자리수가 항상 10인 경우
- 128.0.0.0 ~ 191.255.255.255까지를 B클래스라 한다.
- 네트워크 범위는 10xx xxxx. xxxx xxxx 에서 x가 가질 수 있는 경우의 수이다(2 ^ 14)개
- 호스트 범위는 뒤의  xxxx xxxx. xxxx xxxx 에서 x가 가질 수 있는 경우의 수이다.(2 ^ 16) -2개
- 네트워크및 브로드캐스트 주소사용으로 2개를 호스트 주소에서 제외해야 한다.

### C 클래스<br/>

- IP주소를 바이너리로 표시할 때 맨 앞자리 수가 항상 110으로시작한다.
- 192.0.0.0 ~ 223.255.255.255 까지를 C클래스라 한다.
- 네트워크 범위는 110x xxxx.xxxx xxxx.xxxx xxxx 에서 x가 가질 수 있는 경우의 수이다(2 ^ 21)개
- 호스트 범위는 뒤의  xxxx xxxx 에서 x가 가질 수 있는 경우의 수이다.(2 ^ 8) -2개
- 네트워크및 브로드캐스트 주소사용으로 2개를 호스트 주소에서 제외해야 한다.

<br/>

[자료 출처](https://catsbi.oopy.io/f565e8c1-34c6-4b61-be61-e1ab04fc51fb)


## DHCP

DHCP는 IP 주소 및 기타 통신 매개변수를 자동으로 할당하기 위한 네트워크 관리 프로토콜이다. 이를 통해 네트워크 장치의 IP 주소를 수동으로 설정할 필요 없이 인터넷에 접속할 때 마다 자동으로 IP 주소를 할당할 수 있다.<br/>

많은 라우터와 게이트웨이 장비에 DHCP 기능이 있으며 이를 통해 대부분의 가정용 네트워크에서 IP 주소를 할당한다.<br/>

## NAT

NAT는 패킷이 라우팅 장치를 통해 전송되는 동안 패킷의 IP 주소 정보를 수정해 IP 주소를 다른 주소로 매핑하는 방법이다.

IPv4 주소의 범위 만으로는 많은 주소를 감당하지 못하는데, 이를 해결하기 위해 NAT를 사용하여 공인 IP와 사설 IP를 나눈다.<br/>

192.168.0.xxx의 공인 IP 주소가 있고, 여러 호스트가 이 공인 IP 주소를 사용하여 통신할 수 있다. 다시 말해, 인터넷 회선 하나를 개통하고 인터넷 공유기를 달아서 여러 PC를 연결하여 사용할 수 있는데, 공유기에 NAT 기능이 있기 때문이다.<br/>

NAT를 이용하면 내부 네트워크의 IP 주소와 외부 네트워크의 IP 주소가 달라서 보안이 어느정도 가능하다.<br/>

단, 여러명이 동시에 접속하기 때문에 속도가 느려질 수 있다.<br/>