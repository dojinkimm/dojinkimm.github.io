---
layout: post
title: "[CNetwork] Chapter 1. Computer Networks and the Internet"
date: 2018-08-28 17:00:00
author: Dojin Kim
categories: CS
tags: network cs
cover:  "/assets/imgs/network_cover.jpg"
---


<h2>1.1 What Is the Internet</h2>
<h4>Network란?</h4>
- 목적: device들을 연결해서 정보를 전달한다
- 방법:
1) PSTN(Public Switched Telephone Network) - 예전에 정보전달하는 방법중에 가장 컸으나 현재는 Internet이 가장 크다
2) Internet
3) Cable Network - 미국에서는 많이 사용되고 있다
4) N-ISDN(Narrow Band - Integrated Services Digital Net)
5) B-ISDN(Broad Band - Integrated Services Digital Net)
--> 종합 정보 통신망. 하나를 잘 만들어 놓고 미래 service까지 cover하겠다는 취지에서 나오게 되었지만, 너무 복잡해서 많이 사용이 안되게 되었다.


<h5>네트워크는 3가지 부분으로 나뉘어져있다고 볼 수 있다.</h5>

1) host = end system, 즉 단말
ex) PC, 서버, 모바일 등이 속한다
host에서는 network 응용 프로그램을 실행한다

2) Communication link
둘 사이 혹은 1:N host 사이를 **직접적**으로 연결하는데 사용되는 물리적 매체이다.
ex) 핸드폰과 기지국은 직접적으로 링크되어있지만 핸드폰으로 다른 핸드폰에 연락을 하는 것은 기지국을 통해서 이루어지기 때문에 communication link가 아니다. 여러개를 링크를 거쳐가는 을 우리는 **path**라고 부른다.


3) Routers(혹은 Switches)
: 항상 연결이 되어있는 것이 아니라 필요할 때 패킷끼리 연결을 시켜준다.

<img src="/assets/imgs/cnetwork/figure1.1.png"/>

(그림에서 Key 들은 host들이고, 검은 선들 하나 하나를 communication link라고 부르고, x 표시가 되어있는 것들을 router라고 부른다)

<h5>Internet vs internet</h5>

1) internet
- 소문자로 되어있는 internet은 inter-network의 약어이다.
- 개별(독립)적인 네트워크들을 연결해서 큰 네트워크를 만든다.
- 계층적 구조가 엄격하게 나뉘어져있지 않다.

2) Internet
- Protocol이 맞아야 하는 인터넷 표준을 지칭한다
- 인터넷 표준은 IETF(Internet Engineering Task Force) 에 RFC(Request for Comments)로 정의 되있다.
예) <RFC ....> 이렇게 쓰여져 있는 것들은 인터넷 표준인 것이다. 하지만 모든 RFC가 인터넷 표준은 아니다.
- 인터넷은 분산된 응용 서비스가 되게 구조를 제공한다.
예) 웹, 이메일, 게임 등등
- 어플리케이션에 2가지의 서비스를 제공한다.
a) Connectionless unreliable --> UDP
b) Connection-oriented reliable --> TCP


<h5>Protocol이란?</h5>

: 둘 또는 이상의 host사이에서 **정보 교환을 할 때** 준수해야 하는 일련의 규칙과 절차이다.




<br><br><br>
날짜: 2018년 8월 28일
<p>출처: Computer Networking (A Top-Down Approach) written by Kurose, Ross</p>