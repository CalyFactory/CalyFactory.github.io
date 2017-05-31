---
layout: post
title: "android orm"
date: 2017-05-31 22:29:10
image: '/assets/img/'
description:
main-class:
color:
tags:
categories:
twitter_text:
introduction: "android orm"
---

안드로이드 ORM
---

안드로이드에서 데이터를 저장하는데 여러 방법이있다.

sharedpreference, db, file io, memcache ....

반영구적으로 저장되야 하면서도 지워지면 안되는 중요한 정보들을

db file io로 저장하고 관리하는데

file로 입출력하는 방식은 데이터의 검색/수정/삭제가 용이하지 않고 구조가 클수록 점점 더 복잡해진다. 

결국엔 db를 이용해 저장해야하는데 이를 손쉽게 사용할 수 있는 방법이 바로 ORM이다.

ORM이란 Object Relational Mapping으로 db의 테이블과 매핑되는 객체를 만들어

그 객체에서 db를 관리하는것을 의미한다.

안드로이드엔 수많은 orm라이브러리가 있는데 그 중 하나인 sugar orm사용법을 알아보자

Sugar ORM
---

```java

class AppApplication extends SugarApp {

    @Override
    public void onCreate(){
        super.onCreate();
    }

}
```

Application 구현체를 상속받은 SugarApp클래스를 상속받는 Application 클래스를 만들어 Manifest에 등록시킨다.

```xml

    <meta-data android:name="DATABASE" android:value="caly.db" />
    <meta-data android:name="VERSION" android:value="2" />
    <meta-data android:name="QUERY_LOG" android:value="true" />

```

manifest.xml에 db이름과 버전등을 명시해줄 수 있다.

orm특성상 테이블 구조가 바뀌는 경우엔 version을 바꿔 새롭게 만들거나 바꾸도록 해야한다.

이렇게하면 orm 라이브러리에서 application 안에서 자동으로 테이블을 생성하고 orm을 사용할 준비를 한다.

이제 orm에 사용될 모델을 만들어보자

```java
public class Book extends SugarRecord<Book> {
  String title;
  String edition;

  public Book(){
  }

  public Book(String title, String edition){
    this.title = title;
    this.edition = edition;
  }
}
```

SugarRecord클래스를 상속받은 모델을 만들고 필요한 필드들을 멤버변수로 선언한다.

이제 save, update, delete등을 아래와 같이 쉽게 사용 할 수 있다.


```java
void orm(){

    // save 
    Book book = new Book(ctx, "Title here", "2nd edition")
    book.save();

    // load
    Book book = Book.findById(Book.class, 1);

    // update 
    Book book = Book.findById(Book.class, 1);
    book.title = "updated title here"; // modify the values
    book.edition = "3rd edition";
    book.save(); // updates the previous entry with new values.

    // delete
    Book book = Book.findById(Book.class, 1);
    book.delete();
    
}
```