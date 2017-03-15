---
layout: post
title: "Travis로 안드로이드 배포하기(CD)"
date: 2017-03-15 09:35:28
image: '/assets/img/'
description: "Travis로 안드로이드 배포하기(CD)"
main-class: 'DevOps'
color: '#1D91F5'
tags:
- CD
- DevOps
- Deploy
- Android
categories:
twitter_text:
introduction: "Travis로 안드로이드 배포하기(CD)"
---

CD란?
------
`continuous delivery` 혹은 `continous deploy` 즉 지속적 배포다.
짧은 주기로 개발중인 소프트웨어를 배포하고 그 과정을 자동화 하겠다는뜻인데, 왜 이와같은 과정이 필요할까?

![server](https://blog.snap-ci.com/assets/images/screenshots/pm-guide-cd-devops/snap-ci-continuous-delivery.png)
<이미지 출처 : https://blog.snap-ci.com/>
서버를 예로 들어보면 대부분의 서비스는 `DEV`, `STAGE`, `PRODUCTION` 등의 여러대의 서버가 있을것이다. 

한두대의 서버라면 수동으로 배포해도 큰 문제가 없지만 여러대가 되는순간 매우 비효율적이게 된다.

![terminal](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/jspiner/cd_1.jpg?raw=true)

어플리케이션의 예를 보자

모듈테스트를 진행하고 나서도 QA의 테스트는 개발되는 족족 필요할 것이다.

CD를 하지 않고 한다면 QA팀의 기기에 직접 빌드를 해주거나 저렇게 url로 알려주는 형태로 배포가 될것이고, 개발에만 집중하고 싶어도 귀찮은 배포작업을 하게된다.

그래서 나온 개념이 CI를 확장하여 빌드와 테스트가 완료된 후 자동으로 배포까지하는 CD이다.

TRAVIS CI&CD
--------
Travis는 github에서 webhook을 받아 ci를 자동화해주는 서비스이다.

github에서 repository에 webhook을 걸어주고 `.travis.yml` 파일에 환경과 빌드스크립트를 지정해놓으면

자동으로 가상환경을 만들어 프로젝트를 빌드하고 테스트한다.

![travis](http://tattoocoder.com/content/images/2015/11/Docker-CI-CD.png)

아래는 안드로이드 프로젝트를 빌드하는 간단한 예제이다.

```
language: android
jdk: oraclejdk8
android:
  components:
  - tools
  - platform-tools
  - tools
  - build-tools-23.0.2
  - android-23
  - extra-android-m2repository
  - extra-google-m2repository
before_install:
- chmod +x gradlew
- chmod +rx app 
skip_cleanup: true
script: 
  - "sh script/build.sh"
```

빌드가 성공하게되면 이미지형태로 바로 확인할수 있는 기능을 제공한다.


![terminal](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/jspiner/cd_2.png?raw=true)

Slack으로 배포하기
------

빌드가 성공되면 ftp 혹은 git release로 배포할 수 있지만,

이번엔 간단하게 자동으로 슬랙으로 apk를 보내는 스크립트를 만들어보자.

`.travis.yml` 파일에 빌드가 성공될경우 deploy하도록 설정을 바꾸자

.travis.yml
```
language: android
jdk: oraclejdk8
android:
  components:
  - tools
  - platform-tools
  - tools
  - build-tools-23.0.2
  - android-23
  - extra-android-m2repository
  - extra-google-m2repository
before_install:
- chmod +x gradlew
- chmod +rx app 
skip_cleanup: true
script: 
  - "sh script/build.sh"
deploy:
  provider: script
  skip_cleanup: true
  script: "sh script/deploy.sh"
  on:
    branch:
      - dev
      - master
```

deploy옵션으로 `pull request`나 특정 브랜치만 배포하도록 설정할 수 있다.

이제 빌드된파일을 슬랙으로 보내려면

deploy.sh
```
export DEPLOY_BRANCH="$TRAVIS_BRANCH"
export DEPLOY_COMMIT="$TRAVIS_COMMIT"

SLACK_KEY="<슬랙봇KEY>"
SLACK_TEXT="[ \`$DEPLOY_BRANCH\` | \`$DEPLOY_COMMIT\` ] ${TRAVIS_COMMIT_MESSAGE:-none} "

curl \
  -F "token=$SLACK_KEY" \
  -F "channels=deploy-android" \
  -F "initial_comment=$SLACK_TEXT" \
  -F "file=@app/build/outputs/apk/app-debug.apk" \
  https://slack.com/api/files.upload
```

curl로 전송하기만 하면 된다.

이제 `dev`나 `master` 브랜치에 올라오기만하면 
자동으로 빌드 및 테스트를 진행한 후 슬랙으로 apk를 보내주는 CD환경을 구축했다!

![terminal](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/jspiner/cd_3.png?raw=true)

그 이외에도 Travis 자체에서 아래 사진처럼 여러 배포환경을 제공하니 직접 배포해보자

![terminal](https://github.com/CalyFactory/CalyFactory.github.io/blob/master/assets/img/jspiner/cd_4.png?raw=true)
