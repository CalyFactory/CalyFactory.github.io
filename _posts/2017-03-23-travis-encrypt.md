---
layout: post
title: "Travis 키 암호화하기"
date: 2017-03-22 01:07:35
image: '/assets/img/'
description: "Travis 키 암호화하기"
main-class:
color:
tags:
categories:
twitter_text:
introduction: "Travis 키 암호화하기"
---

Travis 키 암호화하기
----

![travis](http://tattoocoder.com/content/images/2015/11/Docker-CI-CD.png)

저번에 Travis를 통해 간단히 slack으로 배포하는걸 해봤었다.

이번엔 빌드및 테스트할때 필요한 다양한 key나 설정파일들을 암호화해서

빌드하는법을 알아보자.


Key 암호화하기
----

우선 로컬에 travis cli를 설치한다.

```script
$ gem install travis
```

암호화할키를 aes로 암호화하며 .travis.yml파일에 지정한다.

```script
$ travis encrypt DB_KEY="db_key_1234" --add
secure: "... encrypted data ..."
```

--add 옵션을 주면 자동으로 .travis.yml파일에 추가된다.

travis에서 빌드할때 지정해놨던 키를 환경변수로 등록해 사용할수있게한다.

```script
travis build script
$ echo($DB_KEY)
db_key_1234
```


File 암호화하기
----

key를 암호화했던것과 같은 방식으로 파일을 암호화 할 수 있다.

다만 한번에 하나의 파일만 암호화할수있어, 여러개의 파일을 사용할경우 압축해서 압축된 파일을 암호화해야한다.

```script
$ travis encrypt-file key.json
encrypting....
```

암호화작업이 끝나게되면 key.json.enc파일이 생성되게된다.

원본파일인 key.json은 .gitignore에 추가해 github에 업로드되지 않도록 하고,
암호화된파일인 key.json.enc만 git add하여 github에 업로드한다.

그럼 아래와같은 스크립트로 복호화하여 원본파일을 빌드환경에서 가져올수있다.

```script
$ openssl aes-256-cbc -K $encrypted_aaaaaaaa_key -iv $encrypted_aaaaaaaa_key -in key.json.enc -out key.json -d
```

key를 암호화했을때와 마찬가지로 --add옵션으로 자동으로 .travis.yml에 추가되도록 할 수 있다.

