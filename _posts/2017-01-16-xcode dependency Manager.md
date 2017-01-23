---
layout: post
title: "CocoaPods이란 무엇인가?"
date: 2017-01-16 22:43:58
image: '/assets/img/'
description: 'xcode library dependancy'
main-class: 'xcode'
color: '#1D91F5'
tags:
- xcode
categories:
twitter_text:
introduction: 'xcode dependency manager에대해 알아보자.'
---

[cocoapods] xcode dependency manager에대해 알아보자.
====


xcode Dependancy manager
---
바야흐로 2012년 xcode를 처음 접했을 때이다. 

처음 ios를 개발한답시고 책을 사서 접했을 때 외부 라이브러리 연동은 파일을 직접 라이브러리에 임포트하는 형식으로 사용되었다.

추가되어있는 많은 라이브러리를 어떻게 쉽게 관리하지? 

프로젝트가 바뀌거나 협업할땐 어떻게해야될까?

위와같은 의문점을 가지고 알아봣던게 바로 `cocoapods'이다


cocoapods?
---

> CocoaPods is a dependency manager for Swift and Objective-C Cocoa projects. It has over 27 thousand libraries and is used in over 1.6 million apps. CocoaPods can help you scale your projects elegantly.

현재 `cocoapods` 홈페이지에서 설명하고있는 `what is cocoapods'이다. 

말그대로 라이브러리 의존성 관리 매니저이고 수많은 xcode 프로젝트 라이브러리들이 cocoapods으로 관리되어진다. 

안드로이드에서의 gradle과 같은 역할을 해준다고 보면 생각하기 쉽다.

ios/mac app개발시 필수적으로 널리 이용되고있다.

install cocoapods?
---

1. cocoapdos을 설치해보자.

~~~
	sudo gem install cocoapods
~~~

설치가 참 쉽다. 

이제 코코아팟을 사용할 준비가 됬다.

2. 다음 할일은 `Podfile`을 만드는 일이다.

팟파일은 어떤 라이브러리가 해당 앱에 종속되는지. 그리고 버전은 무엇인지. 새로운 버전의 라이브러리가 존재할경우 어떻게 설치할건지.(기존 버저전을 유지하거나, 새로운 버전을 다운받는등의.. 버전설정) 등에대한 정보를 입력한다.
(node를 접해본사람이라면 package.json과 비슷하단 느낌이 들것이다. 바로 그것이다!)

~~~
//Podfile
platform :ios, '8.0'
use_frameworks!

target 'MyApp' do
  pod 'AFNetworking', '~> 2.6'
  pod 'ORStackView', '~> 3.0'
  pod 'SwiftyJSON', '~> 2.3'
end
~~~

위와같이 팟파일을 만들어 주던가, `Pod init`을 통해 만들어도된다.

3. 이제 실제 팟파일을 가지고 기존 xcode 프로젝트와 연결해보자.

xcode기존 프로젝트의 디렉토리에서 

~~~
	pod install
~~~

위의 명령어를 입력한다.

보이는가? 새로운 확장자의 xcode 프로젝트가 하나더 생성될것이다. 바로
`xcworkspace` 이다. 

이제 앞으로 작업을할때 흰색의 아이콘을가진 `app.xcworkspace`을 클릭하고 원하는 라이브러리를 마음껏 임포트하여 사용하면된다. 

해당 프로젝트를 실행해보면 내부에 프로젝트가 `pods 프로젝트`가 하나더 있는것을 확인할 수 있다. 

바로 그 `pods 프로젝트` 에서 모든라이브러리를 관리해준다. 

정말 편하다. 

graldle이 나오기 한참전부터 cocoapods은 라이브러리 관리를 해주었다.(jspiner님 잘보시길..)

이외에도 `pod update`등 명령어를 통해 라이브러리 관리를 보다 효율적으로 할 수 있다.

느낀점
---

라이브러리의 관리는 정말 중요하다.  

프로젝트와 라이브러리를 독립적으로 따로관리되어진다는 측면이 굉장히 매력적이다

필자가 개인적으로 느낀바로는 팀작업할때 이러한 dependacy 매니저가 더욱 큰 역할을 하는 것 같다.

같은 팀원과 라이브러리 종류 그리고 버전등을 지속적으로 체킹할 필요도없고, 오직 podfile하나로 서로의 작업환경을 확인 및 빌드 할 수 있다. 

너무나 깔끔하다.


























