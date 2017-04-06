---
layout: post
title: "production/develop 배포"
date: 2017-04-03 12:43:58
image: '/assets/img/'
description: 'production/develop 배포'
main-class: '배포'
color: '#1D91F5'
tags:
- 배포
categories:
twitter_text:
introduction: 'production/develop 배포'
---

production/develop 배포
=====


<br>	
#### 왜
서비스를 단단하게 운영하기 위해서는 기본적으로 3가지 서버를 가져간다고한다.

* production
* staging
* develop

실제 서비스가 돌아가는 production 서버,
모든 데이터가 운영과 매핑되있는 staging 서버,
개발테스트가 주로 이루어지는 develop 서버가 그것이다.

caly에서는 develop 과 production 서버를 운용하고있다.
어떻게 이 두 가지의 서버를 이용하는지에 대해 간단히 공유하고자 한다.



<br>
#### 0. Git
caly는 github을 사용하고 있고, master/develop/feature등의 브랜치를 나눠서 사용하고있다.
기본적으로 master에는 정말 release될 코드들만이 올라가며 develop에서는 develop코드들 그리고 feature등 조그마한 가지들에서는 각각 수정될 기능들을 만들어 작업하고있다.

작업 흐름을 따져보면 아래와같다.

* develop에서 pull해 feature or 개인 branch로 기능작업을 한다.
* 기능작업을 마치면 develop에 pullRequest를 날려 develop에 머지시킨다. 
* develop에서 각종 테스트를 하고 최종 release할 코드를 master에  pullrequest를 날려 머지시킨다.
* 자 master에 릴리즈 코드가 올라가게되었다!

대부분 깃을 사용하면 비슷하게 사용할 것이다. 

이제 이렇게 공유된 소스를 가지고 어떻게 배포할지를 생각해보아야한다.



##### 1. 개발서버

모든 개발은 개발서버에서 이루어진다. 
테스트할 것이 있던가, 내부 혹은 외부 api를 연동해야 한다던가. 

실제 개발서버에서의 가장 완성된 코드는 git develop branch의 코드가 된다. 

모든 feature등이 develop에 모이게 되기 때문이다.

개발서버에서의 api가 실제 client(android)와의 개발 테스트가 됨으로 이곳 개발서버에서도 항상 최신성을 유지해야 할 필요가있다.

그렇기에 서버개발자 입장에서는 개발서버내에 두가지 서버가 필요하다

1. 서버개발자가 테스트할 api서버
2. android개발자가 테스트할 api 서버

이를 효율적으로 처리하기위해서 caly에서는 몇 가지 스크립트로 두가지서버를 돌리고있다. 

서버개발자가 일정 기능을 완료해 develop에 pullrequest를 통해 머지를 하고,  

해당 코드가 android개발자에게 적용되길 원한다면 스크립트로 실행하여 해당 안드로이드개발자가 가장 stable한 api서버를 이용 할 수 있도록 하는것이다.




##### 2. production서버

자 develop에서 테스트가 끝났다.

이제 production서버로 서비스를 실행하고 싶어진다. 

어떻게 해야할까?

사실 마찬가지이다. 개발서버에서 master에 올린 코드를 서비스로 돌리면 되는것이다.

다른서버이기 때문에 초기 설치파일 세팅 등 몇가지 어려운점이 있었다.

 

#### 3. `fabric`을 통한 스크립트 설치

서버에 필수적으로 설치해야하는 항목들이 있다. 

mysql/rabbimq/mongodb ... 

이러한 수많은 패키지들을 일일이 수작업으로 만들어 실행하고 있을 수는 없다. (시간은 아까운거니까요)

그래서 많은서버로 쉽게 설치가 가능한 `fabric` 이라는 서비스가 있다. 

`fabric`은 서버에 id/pw등을 통해 접속하고 원하는 스크립트를 쉽게 실행시킬 수 있는 패키지이다. 

caly에서도 `fabric`을 통해 최초 서비스 설치를 해보았고, 실제 develop서버에서도 복잡한 스크립트를 모아서 한번에 실행하도록 관리하고 있다. (android개발자용  api서버 배포할 경우등..)

`fabric`을 통해 배포가되어 서버 세팅이되었다면 master에서 주기적인 서버 업로드가 필요할것이다. 


#### 4. `jenkins`를 이용한 빌드

production서버의 코드는 어디서부터 날라오는 것일까? 

결국엔 develop에서 release할 것들이 master에 올라오게된다. 

우리는 master에서 이 코드를 확인하고 다시 production서버로 옮겨야 할것이다.

이 모든 과정을 단순히 해주고, 빌드테스트까지 해볼 수 있는 서비스가 바로 `jenkins`이다.

`jenkins` 보편적으로 빌드 자동화에 쓰인다고한다.

즉, 테스트 코드등을 돌려보고 해당 서버의 빌드가 깨지지 않는지 자동으로 확인해주는 것이다. 

caly에서는 master에 pullRequest가 올라와 merge되는 순간 `jenkins` webhook으로 해당 코드를 `jenkins`서버(실제론 develop서버)에 가져와 빌드를 하고 테스트를 한다. 

그리고 `jenkins`에서 제공하는 빌드후 조치를 통해 bash shell 명령어로  운영서버에서 git pull을 받고 필요한 패키지를 업데이트 하도록 하고 있다.

실제 스크립트에는 기존 실행되고있는 프로세스를 죽이고 재시작하는 로직을 담고 있고, 모든 서비스의 로직을 죽이지 않고 상황에 따라서는 `jenkins`의 수동빌드를 통해 원하는 프로세스를 재시작하게 만든다.



### 마치며

사실 완벽한 배포가아니다.

로드 밸런싱이나 다중 서버 에대한 구성이없으며, 요즘 핫한 무중단 배포도아니다.

하지만 현재 caly 프로토 서비스를 운영하기엔 충분한 배포상황이며, 이후 서비스 확장시 docker를 활용한 green blue 배포등을 염두해 두고있다.

그런말씀을 하신적이 있다. 모든일을 걱정할 필요는 없다. 빠르게 서비스를 만드는 것이 우선이다.

끝.























#### 마치며