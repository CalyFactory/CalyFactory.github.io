---
layout: post
title: "gradle script로 빌드버전 수정하기"
date: 2017-05-24 22:24:00
image: '/assets/img/'
description:
main-class:
color:
tags:
categories:
twitter_text:
introduction: "gradle script로 빌드버전 수정하기"
---

gradle이란 groovy를 이용힌 빌드 배포 도구(build tool)이다.
gradle느 자바에서 많이 사용되어온 Ant, Maven 빌드 툴의 단점은 버리고 장점만 취한 툴이다.
따라서 두 빌드 툴의 기능을 모두 포함하고있다.

현재는 안드로이드 스튜디오에서 기본 빌드툴로 사용되어지고 있다.

gradle로 여러 스크립트를 만들어 다양한 옵션을 줄 수 있는데,
개발/배포 용 빌드버전을 다르게 하여 배포하는 방법을 알아보자.

build.gradle
```gradle
apply plugin: 'com.android.application'

android {
    compileSdkVersion 25
    buildToolsVersion "25.0.2"
    defaultConfig {
        applicationId "net.jspiner.test"
        minSdkVersion 15
        targetSdkVersion 25
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}
allprojects {
    repositories {
        jcenter()
        maven { url "https://jitpack.io" }
    }
}

dependencies {
    ...
}

```

기본적으로 안드로이드 스튜디오에서 프로젝트를 만들면 위와 같이 build.gradle파일이 자동 생성된다.

buildTypes안에 원하는 빌드타입을 직접 지정해 빌드버전을 만들 수 있다.

```gradle


    buildTypes {
        debug {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }

```

`./gradlew clean build' 명령어를 통해 각각의 버전을 동시에 빌드시킬 수 있다.

추가적으로 버전태그에 이름을 붙이거나 속성값을 바꿔줄 수도 있다.


```gradle


    buildTypes {
        debug {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'),
            versionNameSuffix '_dev', 
            resValue "string", "app_name", "디버그버전",
            'proguard-rules.pro'
        }
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 
            resValue "string", "app_name", "디버그버전",
            'proguard-rules.pro'
        }
    }

```