---
layout: post
title: "Spring과 Junit"
date: 2017-06-02 10:43:26
image: '/assets/img/'
description:
main-class: 'JAVA'
color: '#1D91F5'
tags: 
- Spring
categories:
twitter_text:
introduction: 'Spring과 테스팅 프레임워크'
---

Junit
===

Spring에서 Junit 테스팅 프레임웤을 적용해보겠다

평소에 자주 쓰이는 애너테이션들은 다음과 같다.

@Test : 실행할 테스트 메서드
@Before/ After : 매 테스트 전/후
@BeforeClass / AfterClass : 테스트 클래스마다 한번

먼저 java 버전과 인코딩 설정을 추가한다.

```
<properties>
  	<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  	<maven.compiler.source>1.8</maven.compiler.source>
  	<maven.compiler.target>1.8</maven.compiler.target>
  </properties>
```

다음으로 JUnit 의존성을 추가한다.

```
<dependencies>
  	<dependency>
  		<groupId>junit</groupId>
  		<artifactId>junit</artifactId>
  		<version>4.12</version>
  		<scope>test</scope>
  	</dependency>
  </dependencies>
```

기존의 디폴트 pom.xml에 추가를 한 전체소스는 다음과 같을 것이다.

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>refgjin.or.connect</groupId>
  <artifactId>spring-practice</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <dependencies>
  	<dependency>
  		<groupId>junit</groupId>
  		<artifactId>junit</artifactId>
  		<version>4.12</version>
  		<scope>test</scope>
  	</dependency>
  </dependencies>
  
  <properties>
  	<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  	<maven.compiler.source>1.8</maven.compiler.source>
  	<maven.compiler.target>1.8</maven.compiler.target>
  </properties>
</project>
```

해당 Maven 프로젝트에 마법사를 이용해서 JUnit Test Case를 실행한다.

JUnit Test Case에는 다음과 같은 항목을 입력한다.

* Source folder : {프로젝트 홈}/src/test/java
* Name : CountTest
* Package : 임의로 지정 (예: or.kr.calyfactory) 

위에 해당하는 CountTest는 다음과 같다.

```
import org.junit.Before;
import org.junit.Test;

public class CountTest {
    private int count = 0;
    @Before
    public void setUp() {
        System.out.print(count++);
    }

    @Test
    public void testPlus() {
        System.out.print(count++);
    }

    @Test
    public void increase (){
        System.out.print(count++);
    }
}
```

결과값이 0101로 찍히고 JUnit 콘솔에 테스트가 통과한 것을 확인 할 수 있다.