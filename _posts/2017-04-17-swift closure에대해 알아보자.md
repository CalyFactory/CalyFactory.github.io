---
layout: post
title: "swift - closure"
date: 2017-04-17 12:43:58
image: '/assets/img/'
description: 'swift - closure'
main-class: 'swift'
color: '#1D91F5'
tags:
- swift
categories:
twitter_text:
introduction: 'swift - closure'
---

# swift - closure


<br>	
## closure
Swift는 clousre가 있어 그 언어가 더욱 강력하다.
closure는 변수나 상수에서 참조 혹은 값을 바로 넣고 저장할 수있게됩니다. 
변수나 상수를 closing한다하여 closure라고 한다고 합니다. 



<br>
### 기본클로저

이와같은 함수가 존재한다할경우

~~~
let namesss: [String] = ["win","eric","yenos","jenny"]
func backwards(first: String, second: String) -> Bool {
    
    return first > second
}

let reversed: [String] = namesss.sorted(by: backwards)
~~~

아래와 같이 클로저를 이용해 표현이 가능하다.

~~~
let reversedd: [String] = namesss.sorted(by: { (first: String, second: String) -> Bool in
    return first > second
})
~~~

클로저의 기본형태는 아래와같다

~~~
(매개변수) -> 변환 타입 in
실행코드
~~~

### 후행 클로저

기본 클로저상태에서 보다 축약하여 아래와 같이 사용한다. 
단 마지막 클로저에서만 사용이 가능하다.

~~~
let reverseddBackClouser: [String] = namesss.sorted(){ (first: String, second: String) -> Bool in return first > second }
let reverseddBackClouser2: [String] = namesss.sorted{ (first: String, second: String) -> Bool in return first > second }
~~~


### 타입유추 클로저

아래와 같이 기존 함수의 타입을 준수한다면 굳이 표현하지 않더라도 문제 없이 돌아갑니다.

~~~
let reversedBackClouser3: [String] = namesss.sorted{ (first, second) in
    return first > second
}
~~~

$0,$1으로 파라미터를 생략할 수도있고,
심지어 return 역시 생략이 가능합니다.

~~~
let reversedBackClouser4: [String] = namesss.sorted {
    return $0 > $1
}
//심지어 return도 없앨 수 있다.
let reversedBackClouser5: [String] = namesss.sorted {
    $0 > $1
}
~~~

### 마치며
간단하게 클로저의 개념에 대해 알아봤습니다.
후에 다양한 클로저에 대해 알아보겠습니다.

출처 및 참고 : swift 프로그래밍(yagom지음)[http://www.hanbit.co.kr/media/books/book_view.html?p_code=B5682208459]