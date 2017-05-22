---
layout: post
title: "python log를 찍어보자."
date: 2017-02-20 22:43:58
image: '/assets/img/'
description: 'python logging 패키지 이용해보기'
main-class: 'python'
color: '#1D91F5'
tags:
- python
- logging
categories:
twitter_text:
introduction: 'python logging 패키지 이용해보기'
---

python log를 찍어보자.
=====

<br>

왜?
---

개발을 하면 항상 로그를 찍게된다. 

해당 로그들은 개발자의 개발 편의를 위해, 현재 서비스 상태를 나타내는 등 다양한 경우에 사용된다.

기본 print만을 이용한 로그는 개발편의용임으로  실제 production에선 필요없는 부분일 수있다.

기본 로깅 패키지엔 leveling을 주어 해당 로그가 특정한 환경일때만 보이도록 설정할 수있다.


사용하기
---

* 먼저 로그를 찍기위해 다음과 같은 로그 레벨을 정해준다.(패키지 기본로그들을 매핑하는 것이다..)

~~~
import logging
import optparse
 
LOGGING_LEVELS = {'critical': logging.CRITICAL,
                  'error': logging.ERROR,
                  'warning': logging.WARNING,
                  'info': logging.INFO,
                  'debug': logging.DEBUG}

~~~



*  원하는 로그 레벨로 run을 할 경우. 기본적으로 해당 로그레벨 이상의 데이터가 나오는데, 그외에 시간과 레벨 그리고 메시지를 편히 보도록 위와 같은 설정을 해준다.

~~~
def init():
  parser = optparse.OptionParser()
  parser.add_option('-l', '--logging-level', help='Logging level')
  parser.add_option('-f', '--logging-file', help='Logging file name')
  (options, args) = parser.parse_args()
  logging_level = LOGGING_LEVELS.get(options.logging_level, logging.NOTSET)
  logging.basicConfig(level=logging_level, filename=options.logging_file,
                      format='%(asctime)s %(levelname)s: %(message)s',
                      datefmt='%Y-%m-%d %H:%M:%S')

~~~



* 실제 run을 해본다.

~~~
logging.debug("디버깅용 로그~~")
logging.info("도움이 되는 정보를 남겨요~")
logging.warning("주의해야되는곳!")
logging.error("에러!!!")
logging.critical("심각한 에러!!")
 
 
python3 caly.py --logging-level=error

~~~

* 위 와 같이 run을 하게되면 실제 error이상의 데이터 값들만 찍히게된다.


<br>

마치며...
---

debug/production 모드의 로그는 기본적으로 사용해봤지만. 

이토록 상세하게 로그관리가 가능하단것을 새롭게 알게 되었다. 

앞으로 더욱 꼼꼼히 사용해야 겠다.

끝.
