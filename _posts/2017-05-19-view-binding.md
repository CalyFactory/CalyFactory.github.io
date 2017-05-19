---
layout: post
title: "ButterKnife로 깔끔하게 뷰 바인딩하기"
date: 2017-05-19 17:44:07
image: '/assets/img/'
description:
main-class:
color:
tags:
categories:
twitter_text:
introduction: "ButterKnife로 깔끔하게 뷰 바인딩하기"
---

안드로이드에서 뷰를 가져다가 이벤트를 달거나 속성을 바꿔주려면 뷰를 바인딩해야합니다.

기본적으론 아래와 같은 패턴을 사용하게 됩니다.

```xml
<TextView 
android:id="@+id/tv_main_title" 
android:layout_width="match_parent" 
android:layout_height="wrap_content" 
android:gravity="center" 
android:text="TextView!" 
android:textSize="20sp" 
/> 

<Button android:id="@+id/btn_main_intent" 
android:layout_width="wrap_content"
android:layout_height="wrap_content" 
android:layout_gravity="center"
android:text="Button!" 
/> 

```

이런 xml이 있다고 하면 아래와같이 findViewById메소드로 뷰를 가져오게 됩니다.

```java

public class MainActivity extends Activity {

    TextView tvTitle;
    Button btnIntent;

    @Override
    void onCreate(Bundle savedInstanceState){
        super.onCreate(savedInstanceSTate);
        setContentView(R.layout.activity_main);

        tvTitle = ((TextView)findViewById(R.id.tv_main_title));
        btnIntent = ((Button) findViewById(R.id.btn_main_intent));

        btnIntent.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onCreate(View view){
                Log.d(TAG, "clicked!");
            }
        });
    }

}


```

뷰가 한두개이고 이벤트도 몇개 없다면 위와같은 방식으로 뷰들을 불러와도 문제가 없겠지만

보통 어플리케이션에선 수십개의 뷰들이 있고, 그것들을 매번 findViewById 메소드로 불러오다보면

코드가 복잡해지게 됩니다.

그래서 나온게 뷰를 쉽게 바인딩하게 해주는 [ButterKnife](http://jakewharton.github.io/butterknife/) 라이브러리 입니다.

gradle에 아래와같이 dependency를 추가하고

```gradle

dependencies { 
    compile 'com.jakewharton:butterknife:8.6.0' 
}

```

위의 예제와 같던 코드는 아래와 같이 java의 annotation을 이용해 간결하게 사용할 수 있습니다.


```java

public class MainActivity extends Activity {

    @BindView(R.id.tv_main_title)
    TextView tvTitle;

    @BindView(R.id.btn_main_intent)
    Button btnIntent;

    @Override
    void onCreate(Bundle savedInstanceState){
        super.onCreate(savedInstanceSTate);
        setContentView(R.layout.activity_main);

        ButterKnife.bind(this);
    }

    @OnClick(R.id.btn_main_intent)
    void onIntentButtonClick(){
        Log.d(TAG, "clicked!");
    }

}


```

와! 깔끔하게 findViewById를 매번 호출할 필요 없이 ButterKnife.bind(this); 메서드로 한번에 바인딩해주고,

여러 이벤트들도 @OnClick과 같은 어노테이션으로 가져올 수 있습니다.

뿐만아니라 리소스들도 아래 메서드로 바인딩 할 수 있습니다.

```java
@BindString(R.string.title) String title;
@BindBool(R.bool.isTed) boolean isTed;
@BindInt(R.integer.myinteger) int myinteger;
@BindDrawable(R.drawable.graphic) Drawable graphic;
@BindColor(R.color.red) int red;
@BindDimen(R.dimen.spacer) Float spacer;
@BindDimen(R.dimen.intvalue) int intvalue;
```
[출처](http://gun0912.tistory.com/2)

어뎁터 패턴에서는 ViewHolder를 이용해 아래와 같이 뷰를 바인딩 할 수 있습니다.

```java

class ViewHolder{

    @BindView(R.id.tv_main_title)
    TextView tvTitle;

    @BindView(R.id.btn_main_intent)
    Button btnIntent;    

    public ViewHolder(View view){
        ButterKnife.bind(this, view);
    }

}

```