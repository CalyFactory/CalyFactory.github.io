---
layout: post
title: "python - 서버 로그분리하기"
date: 2017-05-29 12:43:58
image: '/assets/img/'
description: 'python -  서버 로그분리하기'
main-class: 'python'
color: '#1D91F5'
tags:
- crawler
categories:
twitter_text:
introduction: 'python -  서버 로그분리하기'
---

<br>
로그분리
================

* 서버는 방대한 양의 로그를 가지고있다. 우리는 이를 로그 파일로 저장하여 관리한다.
* 매일 매일 수천라인이 생기는 로그들을 한파일로 관리하여 로그를 찾는 일은 매우 비효율적이며, 보기도 쉽지않다.
* 파이썬에서 일정시간 단위로 로그를 저장해 보자. 

<br>
logging
------------
* 파이선은 기본  loggingclass가 있다 해당 클래스에서 로그 세팅을 해준다.

~~~
mylogger = logging.getLogger()
~~~
* 로깅 모듈을 세팅해준다.
* 
~~~
mylogger.setLevel(logging.INFO)
~~~
* 내로그가 어느 레벨까지 저장할것인지를 세팅해준다. 아래와같이 세팅한다면 info이상의 로그들만 감지하여 파일에 저장된다.

~~~
rotatingHandler = logging.handlers.TimedRotatingFileHandler(filename='log/'+'log_caly.log', when='midnight', interval=1, encoding='utf-8')
~~~
* 핸들러를 달아주는데 어느위치에, 몇시에, 로그를 나눠 저장할지를 정하는 구간이다. log/log_caly.log 로 저장하고, 매 정각마다 로그파일을 새로 만들도록했다
~~~
fomatter = logging.Formatter('%(asctime)s %(levelname)s: %(message)s')
~~~
* 로그포맷이다 기본적인 포맷에 맞춰 로그가 찍히게된다. 시간과,로그래벨, 메시지가 찍히도록 되어있다.

~~~
rotatingHandler.setFormatter(fomatter)
mylogger.addHandler(rotatingHandler)
~~~
* 위에서 정한 포맷가 핸들러를 달아준다.

<br>
<br>
마치며
===========

* 너무나도 간단하였지만 실제 문서를 보고 찾아세팅하기가 여간 귀찮았다.
* 그래서 이 포스팅은 참 좋은 포스팅이다.
* 로그를 분산하니 로그 보기가 한결 간결하여 너무 편해졌다. 
* 당신도 물론 이렇게 쓰고있겠지..




