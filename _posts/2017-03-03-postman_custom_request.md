---
layout: post
title: "POSTMAN에서 Custom Method로 요청하기"
date: 2017-03-03 05:36:51
image: '/assets/img/'
description: "POSTMAN에서 Custom Method로 요청하기"
main-class:
color:
tags:
categories:
twitter_text:
introduction: "POSTMAN에서 Custom Method로 요청하기"
---

POSTMAN에서 Custom Method로 요청하기
----
Postman은 http test clinet의 끝판왕이라고 불릴정도로 

REST/HTTP API를 개발할때 많은 도움을 주는 프로그램이다.

![postman](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/jspiner/postman_1.png?raw=true)

기본적인 http 요청 전송부터 시작해 json/xml pretty, POST/GET/PUT/DEL 등의 다양한 Method와 테스트기능까지 있다.

POSTMAN에서는 http1.1 method인 `GET` `POST` `PUT` `PATCH` `DELETE` `HEAD`와 WEBDAV method인 `PROPFIND` `PURGE` 등을 지원하고있다.
하지만 API를 설계하다보면 Custom method를 직접 구현할때도있고, 타 서비스와 연계할때 Custom method로 요청을 보내야하기도한다.

POSTMAN에선 공식적으론 Custom Method를 지원하진 않지만, javascript을 기반으로 electron으로 만들어져있어 충분히 원하는 method를 추가해 볼 수 있다.

우선 크롬 확장프로그램이 아닌, 데스크탑 버전을 [공식 웹사이트](https://www.getpostman.com)에서 OS에 맞게 다운받자

![postman](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/jspiner/postman_2.png?raw=true)

그 후 압축을 풀어보면 javascript로 된 소스코드를 볼 수 있다.

![postman](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/jspiner/postman_3.png?raw=true)

여기서 사용 가능한 http method들은 app/js/libs/converters/all-converters.js파일 6840line에 명시되어있다.

~~~javascript

http.STATUS_CODES = statusCodes

http.METHODS = [
	'CHECKOUT',
	'CONNECT',
	'COPY',
	'DELETE',
	'GET',
	'HEAD',
	'LOCK',
	'M-SEARCH',
	'MERGE',
	'MKACTIVITY',
	'MKCOL',
	'MOVE',
	'NOTIFY',
	'OPTIONS',
	'PATCH',
	'POST',
	'PROPFIND',
    'REPORT', //this line
	'PROPPATCH',
	'PURGE',
	'PUT',
	'REPORT',
	'SEARCH',
	'SUBSCRIBE',
	'TRACE',
	'UNLOCK',
	'UNSUBSCRIBE'
]
~~~

여기에 살짝 REPORT라는 CALDAV방식의 http method를 추가해보자

별다른 빌드과정 없이, 포스트맨을 다시 실행시키기만하면 아래와같이 추가한 Custom method를 사용할 수 있다.

![postman](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/jspiner/postman_4.png?raw=true)

다만 공식적으로 지원하는 방식은 아니기때문에, 몇가지 문제가 있다.
요청자체는 정상적으로 되지만 내 클라이언트 파일만 수정하기때문에, 공유하기를 할 경우 method 이름만 표시되고 요청시 문제가 생길 수 있다.(body가 비어있다던가)
