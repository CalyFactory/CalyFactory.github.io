---
layout: post
title: "비동기 worker처리(celery + rabbitmq)"
date: 2017-03-13 22:43:58
image: '/assets/img/'
description: 'celery를 이용한 비동기 worker처리'
main-class: 'python'
color: '#1D91F5'
tags:
- python
- celery
- async
- worker
categories:
twitter_text:
introduction: 'celery를 이용한 비동기 worker처리'
---

# celery를 이용한 비동기 worker처리를 해보자.

<br>

왜?
---

caly에서는 기본적으로 캘린더를 동기화 한다. 

유저들은 다양한 캘린더를 가지고있으며, 캘린더에 이벤트 수도 각기 다르며 그 양 또한 다양하다.

또한 동기화를 하는 과정은 다소 복잡한 로직이 들어가있고, 외부서버(caldav,google)에 의존적임으로 http요청 및 처리소요시간이 절대적으로 필요하게 된다.

위와 같은 동기화로직을 sync로 처리하는것은

* 네트워크 요청을 지나치게 오래 잡게 되어 비효율적인 서비스를 제공하게되고
* 유저가 다음 운영 액션을 빠르게 접할 수 없는 요인 이 된다.


그러므로 caly에서는 동기화와 같이 오래걸리지만 바로 처리할 필요가없는 lazy한 작업들은 비동기로 처리한다. 


### 무엇을 할까?


caly 비동기 서비스를 효율적으로 서비스하기위해선 아래와같은 기본적인 필수 조건이 필요하다.


>작업 처리 시작 순서가 보장되어야한다.

즉, 동기화 요청들이 수없이 들어올 경우. 먼저 들어온 요청이 먼저 동기화를 시작해야한다는 것이다. 

이를 순차적 처리하기 위해 amqp(Advanced Message Queuing Protocol)를 사용할것이고, 많은 amqp중 python에서 대주억인 rabbitmq를 사용 할 것이다.



> 작업이 밀리지 않도록 병렬처리 되어야한다.

비동기로 동기화작업들이 message queue에 쌓이게되고 이를 처리할 프로세스가 필요하다.

여러개의 프로세스를 만들고, 내부에서 스레드를 돌려 동시다발적으로 동기화처리를 할 수 있게 한다. 

이러한 프로세스를 worker 부른다.

정리하면, queue에 있는 작업을 대기하고있던 worker들이 하나하나 처리하게 되는 것이다. 

우리는 이러한 worker를 celery라는 라이브러리를 통해 만들어 제공할 것이다. 


<br>

### rabbitMQ를 설치하자.



~~~
	sudo apt-get install rabbitmq-server	
~~~
rabbitmq를 설치한다.


~~~
	sudo rabbitmq-plugins enable rabbitmq_management		
~~~
rabbitmq를 관리하기위한 관리자 페이지 플러그인을 활성화 시킨다.

~~~
 sudo rabbitmqctl add_user root 1234
 sudo rabbitmqctl set_user_tags root administrator
~~~

유저를 id:root/pw:1234로 설정해주고,  admin태그를 달아준다. 

이제 domain:1567 포트를 통해 rabbitmq 관리 페이지를 접속이 가능하다.

여기서는 

* 큐 생성
* 큐 설정
* 브로커 설정
* 운용되는 큐의 처리 현황

등의 작업을 할 수 있다.

<br>

### Celery를 설치하자.


~~~
	pip install celery
~~~


celery를 설치하고, 

비동기 요청을 보낼  asyncMain.py.

이를 처리할 worker.py.

worker.py에서 messageQueue설정을 rabbitmq로 해줄 것이다.


worker.py

~~~
from celery import Celery
from celery.signals import worker_init
from celery.signals import worker_shutdown
from celery.bin.celery import result

//rabbitmq를 설정해준다
//celery는 default로 rabbitmq를 브로커(messagequeue)로 사용한다.
//guest는 rabbitmq에서 지원해주는 가장 default 큐다.
app = Celery('tasks', broker='amqp://guest:guest@localhost:5672//')

//워커가 초기화됬을때 
@worker_init.connect
def init_worker(**kwargs):
	print('init')

//워커가 종료됬을때
@worker_shutdown.connect
def shutdown_worker(**kwargs):
	print('shut')

//실제 task들을 받는 부분.
//받은데이터를 thread로 처리한다.
@app.task(name='task')
def worker(data):
	Thread = threading.Thread(target=run, args=(data,))
	Thread.start()

def run(data):
	//working	
~~~


asyncMain.py

~~~
	//워커를 임포트한다.
	import worker
	//worker에 해당 데이터를 전달한다.
	worker.worker.delay(data)
~~~

위와같이 간단한 설정으로 메시지큐/워커 시스템을 구축할 수 있다.


<br>

## 마치며...


messageQueue 와 worker 조합은 정말 여러 경우에 사용된다.

특히 비동기처리를 가장 효율적으로 처리할 수 있는 아주 좋은 로직이다. 

worker는 병렬로 돌아가고, thread로 다시한번 병렬 프로그램이 됨으로 항상 값참조에 각별한 주의를 기울여야 할 것이다.

끝.
