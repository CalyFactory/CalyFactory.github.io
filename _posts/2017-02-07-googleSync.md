---
layout: post
title: "googleCalednarAPI Sync에 대해 알아보자"
date: 2017-02-07 22:43:58
image: '/assets/img/'
description: 'googleCalednarAPI Sync'
main-class: 'googleAPI'
color: '#1D91F5'
tags:
- googleOAuth
- python
categories:
twitter_text:
introduction: '구글 캘린더 sync는 어떻게 알고 맞출수있을까'
---

# Google calendar Synchronization

<br>

### 왜?

유저의 캘린더 변화 감지까지는 pushNotification을 통해 알수 있다.(이전포스트)

그렇다면 유저가 어떤 변화를 했기에 push가 왔을까?

어떻게 하면 기존것과의 차이를 알아차고 sync를 맞출수있을까?



### Synchronize Resources 


구글 캘린더 api 에서는 위와같은 싱크를 처리하는 [가이드](https://developers.google.com/google-apps/calendar/v3/sync)를 만들어놓았다. 

구글 가이드의 sync 컨셉의 포인트는 last sync를 이용해 현재상태에서의 변화값을 알아내는 방식이다. 

즉 현재상태와 과거 변하기전의 상태를 비교하여 sync를 맞춰주는 것을 의미하고, 이러한 과정을 보장하기위해 매 상태마다 고유 syncToken을 가지게 한다.

syncToken이라는 값은 `현재 상태에 대한 고유 값`으로 생각하면 쉽다.

<br>

### Sync example


예를 들어 이벤트 추가 sync를 맞춰본다고 가정하자.

1. yenos라는 캘린더의 이벤트 리스트를 google api를 통해 호출한다.
2. 이벤트 리스트의 속성중 "nextSyncToken"을 기억한다.
3. 이벤트를 추가한다.
4. 이벤트 pushNoti에서 변화를 감지한다.
5. 감지한 캘린더의 이벤트 리스트를 호출한다. 이때 파라미터로 syncToken을 넣는데 이때의 syncToken은 2번에서 저장한 nextSyncToken을 넣어준다. 
6. 5번의 리스트를 호출하면 결과가 1번의 리스트 기준으로 변화된 것(이벤트 추가된것)만의 결과가 나온다. 


위의 순서에서 볼 수 있듯이 과거 syncToken을 가지고 현재와 어떻게 변화가 있는지를 확인 할 수 있게된다. 

마치 깃에서 커밋 해시로 과거 기록을 비교하는것과 같은 형태인 것이다.

순서대로 한번 실행해보자.

<br>

### 1. 최초 일정 리스트 호출


일정 리스트 api를 호출해보자.

리스트 api를 호출하기위해 어떤 url로 어떤 파리미터가 required인가를 확인해야하는데 이를 확인할 수 있는 가장 빠른방법은 [googleAPIExplorer](https://developers.google.com/apis-explorer/#search/calendar/calendar/v3/)이다.

여기서 oauth2를 이용하여 api를 테스트 해볼 수 있다. 

명확한 요청을 자신의 웹서버로 하기전에 이와같은 api 테스터에서 테스트를 해보는것은 개발 중 버그를 줄일 수 있는 가장 명확한 방법 중 하나이다.

위에서 테스트를 하고 실제 코드를 작성해 요청을 보내본다.
~~~
GET:https://www.googleapis.com/calendar/v3/calendars/yenos/events
headers = {
    	'content-Type': 'application/json',
    	'Authorization' : 'Bearer ' + userAccessToken
    }    
~~~

위와같은 조건으로 요청을 보내고 리턴을 받아보면,

~~~
{
"accessRole":
"owner",
"defaultReminders":
[],
"etag":
"\"p328*****8ad20g\"",
"items":
[],
"kind":
"calendar#events",
"nextSyncToken":
"ABC=",
"summary":
"cpg0504@gmail.com",
"timeZone":
"Asia/Seoul",
"updated":
"2017-01-22T05:16:41.018Z"
}
~~~

이러한 데이터를 받아볼 수 있을 것이다.

캘린더 리스트에 대한 정보들이 있다. 여기서 주의깊게 봐야할것이 바로 nextSyncToken이다. 

위에서 말했든 저 토큰값이 현재 리스트에대한 고유값이되고 후에 이토큰을 이용해 무엇이 달라졌나 확인해 볼것이다.

<br>

### 2.nextSyncToken


nextSyncToken을 기억해둔다.

여기선 예시로 `ABC=` 라고 저장해 둔다.

<br>
### 3.이벤트 추가


 캘린더에 이벤트를 추가한다. 

 <br>

### 4.resourceChange감지


캘린더 이벤트가 추가되었음으로 정해놓은 콜백 api로 무언가 변화됬다는것이 감지 될것이고 헤더에 아래와 같은 정보들 이 호출될것이다.


~~~
X-Goog-Resource-Id: ys7KY***********28
Content-Length: 0
User-Agent: APIs-Google; (+https://developers.google.com/webmasters/APIs-Google.html)
Connection: Keep-alive
X-Goog-Resource-Uri: https://www.googleapis.com/calendar/v3/calendars/cpg0504@gmail.com/events?maxResults=250&alt=json
Host: {domain}:{port}
X-Goog-Channel-Id: 977********d95b07be367
Accept: */*
X-Goog-Resource-State: exists
X-Goog-Message-Number: 12****9
X-Goog-Channel-Expiration: Sat, 28 Jan 2017 19:22:46 GMT
Content-Type:
Accept-Encoding: gzip,deflate,br
~~~

### 5,6.감지한 캘린더의 이벤트 리스트를 호출한다.


이제 해당 콜백에서 다시 이벤트 요청을 호출하는데 이때 아까 저장해두었던 nextSyncToken을 키값을 syncToken으로 하여 넣어준다.


~~~
GET:https://www.googleapis.com/calendar/v3/calendars/yenos/events
params = {
        'syncToken':{nextSyncToken}
    }        
headers = {
    	'content-Type': 'application/json',
    	'Authorization' : 'Bearer ' + userAccessToken
    }    
~~~

이렇게 request를 보내면 과거 list와 비교하여 추가한 부분이 있다고 아래와 같이 나올것이다.

~~~

{
 "kind": "calendar#events",
 "etag": "\"p******20g\"",
 "summary": "cpg0504@gmail.com",
 "updated": "2017-01-23T05:22:33.269Z",
 "timeZone": "Asia/Seoul",
 "accessRole": "owner",
 "defaultReminders": [
  {
   "method": "popup",
   "minutes": 30
  }
 ],
 "nextSyncToken": "CDE=",
 "items": [
  {
   "kind": "calendar#event",
   "etag": "\"2*****106000\"",
   "id": "bqtprheqacl4bhinko3kfr0ea8",
   "status": "confirmed",
   "htmlLink": "https://www.google.com/calendar/event?eid=Y***********NEBt",
   "created": "2017-01-23T05:22:33.000Z",
   "updated": "2017-01-23T05:22:33.053Z",
   "summary": "d",
   "creator": {
    "email": "cpg0504@gmail.com",
    "displayName": "Choi yenos",
    "self": true
   },
   "organizer": {
    "email": "cpg0504@gmail.com",
    "displayName": "Choi yenos",
    "self": true
   },
   "start": {
    "dateTime": "2017-01-26T07:00:00+09:00"
   },
   "end": {
    "dateTime": "2017-01-26T08:00:00+09:00"
   },
   "iCalUID": "bq*******ea8@google.com",
   "sequence": 0,
   "reminders": {
    "useDefault": true
   }
  }
 ]
}
~~~

위 리스트에서 items에 status 및 updated등 타임을 통해 해당 데이터가 수정, 추가등의 상황을 파악 할수있다.

<br>

## 마치며...


존재하는 대부분의 캘린더는 caldav라는 communication protocal을 사용한다. 

구글역시 caldav로 connection하는 페이지를 제공한다.(조금 부실하다) 

하지만 caldav로 다이렉트로 요청을 날려(이벤트리스트,각종 캘린더 crud...)데이터를 받는것은 인증부터시작해 굉장히 번거롭고 복잡하게 되어있다. 

이는 유저의 캘린더 동기화 이슈도 마찬가지다. 

caldav에선 변화를 콜백으로 주지 않고 지속적으로 폴링하여 비교하라고 이야기한다.(caldav포스트 참고)

다른 캘린더 서비스와 달리 구글은 내부제공 api를 통해 위 와같은 caldav를 통한 어려움들을 직접 접근하지 않아도 사용가능하게 해주고있다.

>구글에게 고맙다.

끝. 
















