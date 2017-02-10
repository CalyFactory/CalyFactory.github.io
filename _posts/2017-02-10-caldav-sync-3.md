---
layout: post
title: "caldav 동기화 구현해보기(2)"
date: 2017-02-10 23:03:39
image: '/assets/img/'
description: "caldav 동기화 구현해보기(2)"
main-class:
color:
tags:
categories:
twitter_text:
introduction: "caldav 동기화 구현해보기(2)"
---

지난번 포스팅까진 caldav의 구조와 데이터 요청 방식을 알아봤었다. 이번 포스팅에선 python을 통해 실제로 데이터를 주고받아 볼 것이다. 예제에선 apple calendar를 사용할 것이다.

우선 데이터 요청에 필요한 XML자료들을 미리 변수로 만들어놨다.

```python
## xml data
XML_REQ_PRINCIPAL = (

    "<D:propfind xmlns:D='DAV:'> "
    "<D:prop> "
    "    <D:current-user-principal/> "
    "</D:prop> "
    "</D:propfind> "
)

XML_REQ_HOMESET = (
    "<?xml version='1.0' encoding='utf-8'?>"
    "<ns0:propfind xmlns:C=\"urn:ietf:params:xml:ns:caldav\" xmlns:D=\"DAV\" xmlns:ns0=\"DAV:\">"
    "   <ns0:prop>"
    "       <C:calendar-home-set/>"
    "   </ns0:prop>"
    "</ns0:propfind>"
)

XML_REQ_CALENDARINFO = (
    "<?xml version='1.0' encoding='utf-8'?>"
    "<ns0:propfind xmlns:C=\"urn:ietf:params:xml:ns:caldav\" xmlns:D=\"DAV\" xmlns:ns0=\"DAV:\"  xmlns:cs=\"http://calendarserver.org/ns/\">"
    "   <ns0:prop>"
    "       <ns0:resourcetype/>"
    "       <ns0:displayname />"
    "       <cs:getctag />"
    "   </ns0:prop>"
    "</ns0:propfind>"
)

XML_REQ_CALENDARCTAG = (
    "<?xml version='1.0' encoding='utf-8'?>"
    "<d:propfind xmlns:d=\"DAV:\" xmlns:cs=\"http://calendarserver.org/ns/\">"
    "   <d:prop>"
    "      <cs:getctag />"
    "   </d:prop>"
    "</d:propfind>"
)

XML_REQ_CALENDARETAG = (
    "<?xml version='1.0' encoding='utf-8'?>"
    "<d:propfind xmlns:d=\"DAV:\" xmlns:c=\"urn:ietf:params:xml:ns:caldav\">"
    "   <d:prop>"
    "       <d:getetag />"
    "       <c:calendar-data />"
    "   </d:prop>"
    "   <d:filter>"
    "       <c:comp-filter name=\"VCALENDAR\" />"
    "   </d:filter>"
    "</d:propfind>"
)

```

XML 데이터에 관한 내용은 [이전 포스팅](https://calyfactory.github.io/caldav-sync-2/)을 참고하자

이제 데이터를 보낼 함수를 하나 만들어 총괄적으로 데이터를 전송할 것이다.


```python

import requests

# send data
def request(url, depth, data, auth):
    response = requests.request(
        "PROPFIND",
        url,
        data = data,
        headers = {
            "Depth" : str(depth)
        },
        auth = auth
    )
    return response
```

HTTP 1.1 방식의 PROPFIND 메소드를 통해 데이터를 전송하고, 요청하는 자료마다 depth가 달라진다. 인증은 Basic Auth를 통해 requests 내부에서 base64로 인코딩해서 전송한다.

이제 인증정보를 담을 변수를 하나 만들어 사용할 것이다.

```python
# user auth
auth = ("jspiner@naver.com", "password")
```

각 플랫폼마다 계정이 이메일수도 있고, 아닐수도 있다.

이제 필요한 순서대로 
Principal -> HomeSet -> Calendar -> Event 순으로 데이터를 받을것이다.

 아래와 같이 Principal을 요청하면 
 

```python

## REQUEST PRINCIPAL DATA

result = request(
    "https://caldav.icloud.com",
    0,
    XML_REQ_PRINCIPAL,
    auth
)
print(result.text)

```

caldav 서버에서 아래와 같은 XML 형식으로 데이터를 반환한다.

```xml
<?xml version='1.0' encoding='UTF-8'?>
<multistatus xmlns='DAV:'>
  <response>
    <href>/</href>
    <propstat>
      <prop>
        <current-user-principal>
          <href>/123456789/principal/</href>
        </current-user-principal>
      </prop>
      <status>HTTP/1.1 200 OK</status>
    </propstat>
  </response>
</multistatus>
```
여기서 얻은 principal 주소로 다시 요청을 보내 homeset 주소를 얻을 수 있다.

```python

## REQUEST PRINCIPAL DATA

result = request(
    "https://caldav.icloud.com/123456789/principal",
    0,
    XML_REQ_HOMESET,
    auth
)
print(result.text)
```
```xml
<?xml version='1.0' encoding='UTF-8'?>
<multistatus xmlns='DAV:'>
  <response>
    <href>/123456789/principal/</href>
    <propstat>
      <prop>
        <calendar-home-set xmlns='urn:ietf:params:xml:ns:caldav'>
          <href xmlns='DAV:'>https://p51-caldav.icloud.com:443/123456789/calendars/</href>
        </calendar-home-set>
      </prop>
      <status>HTTP/1.1 200 OK</status>
    </propstat>
  </response>
</multistatus>
```
이제 homeset 주소를 얻었으니 캘린더이름, 정보, tag 등을 가져오고 검색옵션으로 검색할 수 있다.

기본적인 calendar 정보와 이벤트를 불러와보자

(여기서부턴 header에 depth가 1로 들어간다.)


```python

## REQUEST PRINCIPAL DATA

result = request(
    "https://p51-caldav.icloud.com:443/123456789/calendars/",
    1,
    XML_REQ_CALENDARINFO,
    auth
)
print(result.text)
```
```xml
<?xml version='1.0' encoding='UTF-8'?>
<multistatus xmlns='DAV:'>
  <response>
    <href>/123456789/calendars/</href>
    <propstat>
      <prop>
        <displayname>JungSungMin</displayname>
        <resourcetype>
          <collection/>
        </resourcetype>
        <getctag xmlns='http://calendarserver.org/ns/'>FT=-@RU=20036472-f0ed-4053-a666-360a048d70e4@S=112</getctag>
      </prop>
      <status>HTTP/1.1 200 OK</status>
    </propstat>
    <propstat>
      <prop>
        <resourcetype/>
        <getctag xmlns='http://calendarserver.org/ns/'/>
      </prop>
      <status>HTTP/1.1 404 Not Found</status>
    </propstat>
  </response>
  <response>
    <href>/123456789/calendars/home/</href>
    <propstat>
      <prop>
        <displayname>í</displayname>
        <resourcetype>
          <collection/>
          <calendar xmlns='urn:ietf:params:xml:ns:caldav'/>
        </resourcetype>
        <getctag xmlns='http://calendarserver.org/ns/'>FT=-@RU=20036472-f0ed-4053-a666-360a048d70e4@S=112</getctag>
      </prop>
      <status>HTTP/1.1 200 OK</status>
    </propstat>
    <propstat>
      <prop>
        <resourcetype/>
        <getctag xmlns='http://calendarserver.org/ns/'/>
      </prop>
      <status>HTTP/1.1 404 Not Found</status>
    </propstat>
  </response>
  <response>
    <href>/123456789/calendars/notification/</href>
    <propstat>
      <prop>
        <displayname>notification</displayname>
        <getctag xmlns='http://calendarserver.org/ns/'>FT=-@RU=20036472-f0ed-4053-a666-360a048d70e4@S=2</getctag>
        <resourcetype>
          <notification xmlns='http://calendarserver.org/ns/'/>
          <collection/>
        </resourcetype>
      </prop>
      <status>HTTP/1.1 200 OK</status>
    </propstat>
    <propstat>
      <prop>
        <resourcetype/>
        <getctag xmlns='http://calendarserver.org/ns/'/>
      </prop>
      <status>HTTP/1.1 404 Not Found</status>
    </propstat>
  </response>
  <response>
    <href>/123456789/calendars/tasks/</href>
    <propstat>
      <prop>
        <displayname>ë¯¸ë¦¬ ìë¦¼</displayname>
        <resourcetype>
          <collection/>
          <calendar xmlns='urn:ietf:params:xml:ns:caldav'/>
        </resourcetype>
        <getctag xmlns='http://calendarserver.org/ns/'>FT=-@RU=20036472-f0ed-4053-a666-360a048d70e4@S=13</getctag>
      </prop>
      <status>HTTP/1.1 200 OK</status>
    </propstat>
    <propstat>
      <prop>
        <resourcetype/>
        <getctag xmlns='http://calendarserver.org/ns/'/>
      </prop>
      <status>HTTP/1.1 404 Not Found</status>
    </propstat>
  </response>
  <response>
    <href>/123456789/calendars/work/</href>
    <propstat>
      <prop>
        <displayname>ì§ì¥</displayname>
        <resourcetype>
          <collection/>
          <calendar xmlns='urn:ietf:params:xml:ns:caldav'/>
        </resourcetype>
        <getctag xmlns='http://calendarserver.org/ns/'>FT=-@RU=20036472-f0ed-4053-a666-360a048d70e4@S=46</getctag>
      </prop>
      <status>HTTP/1.1 200 OK</status>
    </propstat>
    <propstat>
      <prop>
        <resourcetype/>
        <getctag xmlns='http://calendarserver.org/ns/'/>
      </prop>
      <status>HTTP/1.1 404 Not Found</status>
    </propstat>
  </response>
  <response>
    <href>/123456789/calendars/inbox/</href>
    <propstat>
      <prop>
        <resourcetype>
          <collection/>
          <schedule-inbox xmlns='urn:ietf:params:xml:ns:caldav'/>
        </resourcetype>
        <getctag xmlns='http://calendarserver.org/ns/'>FT=-@RU=20036472-f0ed-4053-a666-360a048d70e4@S=6</getctag>
      </prop>
      <status>HTTP/1.1 200 OK</status>
    </propstat>
    <propstat>
      <prop>
        <displayname/>
        <resourcetype/>
        <getctag xmlns='http://calendarserver.org/ns/'/>
      </prop>
      <status>HTTP/1.1 404 Not Found</status>
    </propstat>
  </response>
  <response>
    <href>/123456789/calendars/outbox/</href>
    <propstat>
      <prop>
        <resourcetype>
          <collection/>
          <schedule-outbox xmlns='urn:ietf:params:xml:ns:caldav'/>
        </resourcetype>
      </prop>
      <status>HTTP/1.1 200 OK</status>
    </propstat>
    <propstat>
      <prop>
        <getctag xmlns='http://calendarserver.org/ns/'/>
      </prop>
      <status>HTTP/1.1 404 Not Found</status>
    </propstat>
  </response>
</multistatus>
```
이렇게 캘린더들의 id와 명칭, ctag를 가져올 수 있다.

같은 방식으로 event의 정보를 조회해볼 수도 있다.자

다음번 포스팅에서는 이벤트의 조회 및 etag ctag로 동기화 과정을 알아보자




