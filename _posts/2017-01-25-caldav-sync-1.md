---
layout: post
title: "caldav 동기화 구현해보기(1)"
date: 2017-01-25 18:45:28
image: '/assets/img/'
description: 'caldav 동기화 구현해보기(1)'
main-class:
color: '#7AAB13'
tags:
- caldav
categories:
twitter_text:
introduction: 'caldav 동기화 구현해보기(1)'
---

# CalDav란?
CalDav는 Calendaring Extensions to WebDAV의 약자로 캘린더의 정보를 서버와 동기화하기 위한 공개 프로토콜이다.

기본적으로 HTTP를 기반으로 iCalendar 포맷을 통해 데이터를 주고받는다. 프로토콜에 대한 상세 정보는 [RFC 4791](https://tools.ietf.org/html/rfc4791)에 정의되어있다.
구글과 네이버 애플을 비롯해 캘린더 서비스를 제공하는 여러 서비스들은 CalDav 혹은 자사 API를 통해 동기화 하도록 되어있다.

# 통신방식 
CalDav는 HTTP 방식으로 통신하며 BASIC Auth를 사용한다.

![Basic Auth](https://media-www-asp.azureedge.net/media/3994479/webapi_auth04.png)

Basic Auth는 아이디와 비밀번호를 Base64로 인코딩해 http 헤더에 넣어 보내는 방식이다. 암호화되지 않은 정보를 보내기 때문에 https 프로토콜을 사용하는것을 권장한다.

CalDav 서버로 요청하고자 하는 자료를 XML형태로 보내게된다. 아래는 캘린더의 principal 자료를 요청하는 xml의 예제다. 

```
<D:propfind xmlns:D='DAV:’> 
    <D:prop> 
        <D:current-user-principal/> 
    </D:prop> 
</D:propfind> 
```


# 주요 리소스 구조 
- principal : Network Resource로 사람이나 컴퓨터의 신원정보로 사용된다.
- calendar-home-set  : 유저가 사용하고있는 여러 캘린더들의 묶음이다.
- calendarId : 캘린더를 구분하는 고유한 id이다. 
- eventId : 이벤트(일정)을 구분하는 고유한 id이다. 
- cTag : sync를 위한 tag이다. 랜덤한 해쉬값일수도있고, 증가하는 숫자일수도있고, 시간일 수도 있다.
- eTag : sync를 위한 tag이다. 랜덤한 해쉬값일수도있고, 증가하는 숫자일수도있고, 시간일 수도 있다.

# sync 과정
캘린더마다 각자 고유한 calendarId를 가지고있고, cTag를 가지고 있다. 캘린더 내에는 많은 일정(event)들이 있고 이 이벤트마다 각자의 eventId와 eTag를 가지고 있다. 캘린더의 일정이 추가/삭제/변경이 될 경우 그 eTag는 변하게되며, 동시에 그 일정이 속한 캘린더의 cTag또한 변하게된다.

즉 동기화과정은 

1. cTag가 변경되었나 확인 
2. cTag가 변경되었다면 캘린더 정보가 변경된것이므로 캘린더에 속한 이벤트들을 불러옴
3. 이벤트들을 불러와서 eTag를 비교해 추가/삭제/변경된 이벤트들을 찾음 
4. 추가/삭제/변경된 이벤트들만 받아와 동기화 

와 같은 과정으로 이루어진다. 

