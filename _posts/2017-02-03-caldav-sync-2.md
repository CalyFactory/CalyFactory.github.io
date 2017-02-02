---
layout: post
title: "caldav 동기화 구현해보기(1)"
date: 2017-02-03 01:44:09
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

# CalDav 동기화 구조 
- 유저는 1개의 인증정보(Principal)을 가지고 있다.
- 인증정보(Principal) 에는 하나의 묶음(homeset)가 있다.
- 하나의 묶음(homeset) 에는 캘린더들이 있다.
- 캘린더에는 여러 이벤트들이 존재한다.

# Principal 요청 
HTTP PROFIND 방식을 사용하며 Basic Auth로 인증한다.

```xml
HTTP 1.1 PROPFIND
url : /
Depth : 0 
Auth : Basic Auth 
Body : 
<?xml version='1.0' encoding='utf-8'?>
<ns0:propfind xmlns:C="urn:ietf:params:xml:ns:caldav" xmlns:D="DAV" xmlns:ns0="DAV:">
    <ns0:prop>
        <ns0:current-user-principal/>
    </ns0:prop>
</ns0:propfind>
```

# HomeSet 요청 
HTTP PROFIND 방식을 사용하며 Basic Auth로 인증한다.

```xml
HTTP 1.1 PROPFIND
url : /
Depth : 0 
Auth : Basic Auth 
Body : 

<?xml version='1.0' encoding='utf-8'?>
<ns0:propfind xmlns:C="urn:ietf:params:xml:ns:caldav" xmlns:D="DAV" xmlns:ns0="DAV:">
    <ns0:prop>
        <C:calendar-home-set/>
    </ns0:prop>
</ns0:propfind>
```

# Calendar 요청 
HTTP PROFIND 방식을 사용하며 Basic Auth로 인증한다.

```
HTTP 1.1 PROPFIND
url : /
Depth : 0 
Auth : Basic Auth 
Body : 

<?xml version='1.0' encoding='utf-8'?>
<ns0:propfind xmlns:C="urn:ietf:params:xml:ns:caldav" xmlns:D="DAV" xmlns:ns0="DAV:">
    <ns0:prop>
        <ns0:resourcetype/>
    </ns0:prop>
</ns0:propfind>
```

# CTag 요청 
HTTP PROFIND 방식을 사용하며 Basic Auth로 인증한다.

```
HTTP 1.1 PROPFIND
url : /
Depth : 0 
Auth : Basic Auth 
Body : 

<?xml version='1.0' encoding='utf-8'?>
<d:propfind xmlns:d="DAV:" xmlns:cs="http://calendarserver.org/ns/">
    <d:prop>
        <d:displayname />
        <cs:getctag />
    </d:prop>
</d:propfind>
```

# ETag 요청 
HTTP PROFIND 방식을 사용하며 Basic Auth로 인증한다.

```xml
HTTP 1.1 PROPFIND
url : /
Depth : 0 
Auth : Basic Auth 
Body : 

<?xml version='1.0' encoding='utf-8'?>
<c:calendar-query xmlns:d="DAV:" xmlns:c="urn:ietf:params:xml:ns:caldav">
    <d:prop>
        <d:getetag />
        <c:calendar-data />
    </d:prop>
    <c:filter>
        <c:comp-filter name="VCALENDAR" />
    </c:filter>
</c:calendar-query>
```

위와 같은 과정을 python으로 구현하여 [오픈소스](https://github.com/CalyFactory/python-caldavclient)화 하였다.

현재 네이버와 애플 caldav 서버에 대해 연동 테스트를 마쳤고, 좀 더 가공해 라이브러리로 배포할 예정이다.

다음 포스팅에서는 python으로 실제로 caldav를 동기화 하는 방법에 대해 알아보자 

