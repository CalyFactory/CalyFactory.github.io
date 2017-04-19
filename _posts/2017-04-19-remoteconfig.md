---
layout: post
title: "원격으로 앱을 변경하는 remoteConfig"
date: 2017-04-19 14:21:43
image: '/assets/img/'
description: "원격으로 앱을 변경하는 remoteConfig"
main-class:
color:
tags:
- firebase
- google
- remoteConfig
categories: "firebase"
twitter_text:
introduction: "원격으로 앱을 변경하는 remoteConfig"
---

Firebase의 서비스 중 RemoteConfig라는것이 있다. 앱을 재배포하지 않고도 앱 내의 변수들을 변경하여 실시간으로 업데이트 할 수 있도록 지원해주는 서비스이다.

[![Video Label](http://img.youtube.com/vi/_CXXVFPO6f0/0.jpg)](https://youtu.be/_CXXVFPO6f0?t=0s)

 Remote Config를 활용하면 아래와 같은 일들을 할 수 있다고 한다.

 1. 새 버전 출시 없이 업데이트
 2. 알맞은 사용자에게 알맞은 콘텐츠 제공
 3. A/B 테스트 실행 및 점진적 출시

 Remote Config 자체는 단순한 Key-Value 형태의 데이터 공간이고 그걸 서버에서 관리해 앱에서 다운받아 사용할 뿐이다. 하지만 Remote Config가 강력한 이유는 2/3번과 같이 사용자 군을 나누어 값을 다르게 설정할 수 있고, 그에 따라 A/B테스트를 하면서 점진적 개선을 할 수 있다는 점이다.

Remote Config는 아래와 같은 flow로 데이터를 서버와 동기화 하는데
![terminal](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/jspiner/remote_1.png?raw=true)
[이미지 출처](https://medium.com/@hitherejoe/exploring-firebase-on-android-ios-remote-config-3e1407b088f6)

일반적 구현했던으로 잘 변하지 않는 설정값들을 서버에서 받아와 캐싱하는 과정과 유사하다.

서버에서 key-value타입으로 데이터를 설정하면 어플리케이션은 주기적으로 fetch하여 서버에서 데이터를 받고, 어플리케이션은 그 값을 보여준다. 

그 외에 Active Config와 Default Config라는 개념이 있는데, Default Config는 네트워크 문제 등으로 서버와 통신을 못했거나, 서버에 데이터가 없는경우 기본적으로 보여줄 값을 설정해놓는것이고(클라이언트에서), Active Config는  fetch 받은 데이터들을 로컬 데이터로 복사하는 과정이다.

![terminal](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/jspiner/remote_2.png?raw=true)
[이미지 출처](https://firebase.google.com/docs/remote-config/api-overview)

fetch와 Active Config 기능은 네트워크나 디스크 I/O가 일어나는 만큼 어플리케이션 성능에 저하를 끼칠수 있다. Remote Config에서는 기본적으로 12시간을 기준으로 캐싱하여 불필요한 배터리 및 데이터 낭비를 방지하도록 하고있다.
