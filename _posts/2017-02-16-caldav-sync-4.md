---
layout: post
title: "caldav 동기화 구현해보기(4)"
date: 2017-02-16 00:43:02
image: '/assets/img/'
description: "caldav 동기화 구현해보기(4)"
main-class:
color:
tags:
categories:
twitter_text:
introduction: "caldav 동기화 구현해보기(4)"
---

지난번까지 calendar의 정보를 알아봤었다.
이제 드디어 본격적으로 event정보와 동기화를 해보자 

```python

import requests

def request(url, depth, data, auth):
    response = requests.request(
        "propfind",
        url,
        data = data,
        headers = {
            "Depth" : str(depth)
        },
        auth = auth
    )
    return response

auth = (userid, userpw)

XML = (
"""
<c:calendar-query xmlns:d="DAV:" xmlns:c="urn:ietf:params:xml:ns:caldav">
    <d:prop>
        <d:getetag />
        <c:calendar-data />
    </d:prop>
    <c:filter>
        <c:comp-filter name="VCALENDAR" />
    </c:filter>
</c:calendar-query>
"""
)

result = request(
    "https://caldav.calendar.naver.com:443/caldav/useridsample/calendar/11232153/",
    1,
    XML,
    auth
)
print(result.text)


```
아래와같이 multistatus로 event들의 정보를 받아올 수 있다.


```
<?xml version="1.0" encoding="UTF-8"?>
<D:multistatus xmlns:D="DAV:" xmlns:caldav="urn:ietf:params:xml:ns:caldav" xmlns:carddav="urn:ietf:params:xml:ns:carddav" xmlns:cs="http://calendarserver.org/ns/" xmlns:ical="http://apple.com/ns/ical/" xmlns:me="http://me.com/_namespace/" xmlns:navercal="http://calendar.naver.com/">
  <D:response>
    <D:href>/caldav/jspiner/calendar/25443380/</D:href>
    <D:propstat>
      <D:prop />
      <D:status>HTTP/1.1 200 OK</D:status>
    </D:propstat>
    <D:propstat>
      <D:status>HTTP/1.1 404 Not Found</D:status>
      <D:prop>
        <d:getetag xmlns:d="DAV:" />
      </D:prop>
    </D:propstat>
  </D:response>
  <D:response>
    <D:href>/caldav/jspiner/calendar/25443380/1487141506036bkvbh4o0J30s1WJcLStx6r35FIpSbS.ics</D:href>
    <D:propstat>
      <D:prop>
        <D:getetag>"2017-02-15 15:51:46"</D:getetag>
      </D:prop>
      <D:status>HTTP/1.1 200 OK</D:status>
    </D:propstat>
  </D:response>
  <D:response>
    <D:href>/caldav/jspiner/calendar/25443380/1487141502669wZYSHcnD1Mao9Rj4oLOCeEk78dCAR8.ics</D:href>
    <D:propstat>
      <D:prop>
        <D:getetag>"2017-02-15 15:51:42"</D:getetag>
      </D:prop>
      <D:status>HTTP/1.1 200 OK</D:status>
    </D:propstat>
  </D:response>
</D:multistatus>

```

이제 xml에서 필요한 데이터인 eventUrl과 eTag를 가져오면 그것을 통해 
뭐가 바뀌었는지 찾아 동기화를 할 수 있다.


```python
#xml parsing
from xml.etree.ElementTree import *

xmlTree = ElementTree(fromstring(ret.content)).getroot()

for response in xmlTree.iter():
   eventUrl = response.find("href").text(),
   eTag = response.find("propstat").find("prop").find("getetag").text()
	
```


이렇게 etag와 ctag를 가지고 Added/Updated/Removed 된 데이터를 구분 할 수 있다.

```

class DictDiffer(object):
    def __init__(self, past_dict, current_dict):
        self.current_dict, self.past_dict = current_dict, past_dict
        self.set_current, self.set_past = set(current_dict.keys()), set(past_dict.keys())
        self.intersect = self.set_current.intersection(self.set_past)

    def added(self):
        return self.set_current - self.intersect 

    def removed(self):
        return self.set_past - self.intersect 

    def changed(self):
        return set(o for o in self.intersect if self.past_dict[o] != self.current_dict[o])

    def unchanged(self):
        return set(o for o in self.intersect if self.past_dict[o] == self.current_dict[o])



def diffEvent(oldList, newList):
    return DictDiffer(
        eventListToDict(oldList), 
        eventListToDict(newList)
    )

eventDiff = diffEvent(old.eventList, new.eventList)
print("add : " + str(eventDiff.added()))
print("removed : " + str(eventDiff.removed()))
print("changed : " + str(eventDiff.changed()))
print("unchanged : " + str(eventDiff.unchanged()))
```






