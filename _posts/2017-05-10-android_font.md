---
layout: post
title: "안드로이드 앱 폰트 적용하기"
date: 2017-05-10 23:10:44
image: '/assets/img/'
description: "안드로이드 앱 폰트 적용하기"
main-class:
color:
tags:
categories:
twitter_text:
introduction: "안드로이드 앱 폰트 적용하기"
---

안드로이드의 TextView 객체는 font를 적용할 수 있는 attribute가 없다.
뷰에 폰트를 적용하기 위해선 Typeface객체를 이용해 코드로 폰트를 적용해줘야한다.

assets 폴더에 폰트파일을 넣고 아래와 같이 Typeface객체로 불러와 뷰에 적용시켜준다.

```java

Typeface fontNanum;

void initFont(){
    fontNanum = Typeface.createFromAsset(this.getAssets(), "NanumSquareOTFBold.otf");
}

void setFont(TextView textView){
    textView.setTypeface(fontNanum);
}

```

하지만 화면에 텍스트가 한두개도 아닌데 위와 같은 방법은 매우 비효율적이다.

좀 더 개선된 방법이 Viewgroup을 이용해 Childe View를 찾아가면서 모든 뷰에 적용하는 방법이 있다.

```java

Typeface fontNanum;

void initFont(){
    fontNanum = Typeface.createFromAsset(this.getAssets(), "NanumSquareOTFBold.otf");
}

void setFontAllView(ViewGroup view){

    for(int i=0;i<view.getChildCount();i++){

        View child = view.getChildAt(i);
        if(child instanceof TextView){
            ((TextView)child).setTypeface(fontNanum);
        }
        else if(child instanceof ViewGroup){
            setFontAllView((ViewGroup)view);
        }
    }

}


```

root ViewGroup만 지정해주면 알아서 폰트를 다 지정해 줄 수 있지만, 뷰마다 폰트가 다른경우엔 쓸 수 없다.

 요런 불편함들을 해소해준 여러 라이브러리가 있는데 그 중 하나인 Calligraphy를 살펴보자

 https://github.com/chrisjenx/Calligraphy

 사용법은 매우 쉽다.

 gradle에 아래와 같이 추가하고

 ```gradle

dependencies {
    compile 'uk.co.chrisjenx:calligraphy:2.2.0'
}


 ```

 assets폴더 안에 폰트를 추가한 뒤 TextView에 커스텀 attribute를 추가해주고 Activity의 attachBaseContext()에서 래핑해주기만 하면 된다.

 ```xml

<TextView fontPath="fonts/NanumSquareOTFBold.otf"/>

 ```

Activity
```java
@Override
protected void attachBaseContext(Context newBase) {
    super.attachBaseContext(CalligraphyContextWrapper.wrap(newBase));
}
```

뷰에 attribute로 사용할 수 있어 폰트가 다 다른 경우에도 쉽게 사용할 수 있고,
CalligraphyConfig를 이용해 기본 폰트를 지정해 fontPath를 지정하지 않고도 기본 폰트를 설정할 수 있다.