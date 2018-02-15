---
layout: post
title: "swift - map/filter/reduce."
date: 2017-04-24 12:43:58
image: '/assets/img/'
description: 'swift의 고차함수'
main-class: 'swift'
color: '#1D91F5'
tags:
- swift
categories:
twitter_text:
introduction: 'swift - map/filter/reduce.'
---


<br><br>
# GUARD


* if 문과 같이 bool에 따라 동작한다. if의 예외상황은 else로 사용하지만 Guard를 사용하면 보다 명확히 표현 할 수 있다. 
* 예외인 상황만을 처리할 경우 guard를 사용 하면 편하다
* 아래와 같이 생겨먹었다.

<br>
### struct

~~~
guard {booltype} else {
	//예외상황 실행문
}
~~~

<br>
### ex1

* 의도: else일 경우 패스하고 싶다. 


~~~
for i in 0...3 {
    guard i == 2 else {
        continue
    }
    print(i)
}
~~~

<br>
### ex2

* 의도: optional binding

~~~

func greet(_ person: [String: String]) {
    guard let name:String = person["name"] else {
        //nil일 경우 return 한다.
        return
    }

    guard let location:String = person["location"] else {
        print(" I hope the weather is nice near you")
        return
    }
    
}

~~~

<br>
### ex3

* 다만 return,break,continue등이 쓰이지 않는 일반적인 if 분기 등에서는 쓰일 수 없다. 
* 쓰일 필요도 없다.

~~~

let first: Int = 10
let second: Int = 20
guard first > second else {
    //이러한 방법으론 사용할 수 없다.
}
~~~



<br><br>
## Map

* 매개변수로 함수를 갖는 함수를 고차함수라고 한다
* 맵,리듀스,필터 등이 그것이다.

> 컨테이너의 값을 매개변수로 전달된 함수를 실행하여 결과를 반환해주는 고차함수.


* 맵은 데이터를 변형하여 기존 데이터에 넣어준다.


<br>
## ex1


~~~
let numbers: [Int] = [0,1,2,3,4,5]
var doubleNumbers: [Int] = [Int]()
var strings: [String] = [String]()

//기존 for loop
for number in numbers {
    doubleNumbers.append(number * 2)
}

print(doubleNumbers)

//map 사용
doubleNumbers = numbers.map({ (number: Int) -> Int in
    return number * 2
})

strings = numbers.map({ (number: Int) -> String in
    return "\(number)"
})
print (strings)
~~~

<br>
### ex2
 
 * 클로저를 통해 더 간단히 만들어 보자
 
 ~~~
 doubleNumbers = numbers.map({ (number: Int) -> Int in
    return number * 2
})

를 아래와 같이

 doubleNumbers = numbers.map{ $0 * 2 }
 ~~~
 
<br>
### ex3
 
* 클로저를 이용하여 간결하게 map을 사용해 보자

~~~
let evenNumers: [Int] = [0,2,4,6,8]
let odddNumbers: [Int] = [1,3,5,7,9]
let multiplyTow: (Int) -> Int = { $0 * 2 }
let doubledEvenNumber = doubleNumbers.map(multiplyTow)
print(doubledEvenNumber)
~~~

<br><br>
## Filter

> 컨테이너 내부의 값을 걸러서 추출해주는 고차함수.


* 맵과 같이 기존컨텐츠를 변형하는 것이 아니라 조건에 맞춰 걸러낸다.

* 반환값은 bool 인데 해당 조건에 맞는(true)것이면 넣어주고, 아니면 걸러낸다.

<br>
### ex1

~~~
let numbers: [Int] =  [0,1,2,3,4,5]
let evenNumbers: [Int] = numbers.filter { (number:Int) -> Bool in return number % 2 == 0 }
print(evenNumbers)
~~~



## map & filter

* map과  reduce를 동시에 사용해보자

<br>
### ex1
~~~
let numbers: [Int] = [0,1,2,3,4,5]
let mappedNumbers: [Int] = numbers.map{ $0 + 3 }
let evenNumbers: [Int] = mappedNumbers.filter{ $0 % 2 == 0}
~~~

<br>
### ex2

* 한번에 연결하여 보여줄수도 있다.

~~~
let evenNumbers: [Int] = numbers.map{ $0 + 3 }.filter { $0 % 2 == 0 }
~~~



## Reduce


> 합쳐주는 기능을 가진 고차함수.

* 정수배열이라면 모든 값을 다음 전달받은 연산결과로 합쳐준다.
* inital이라는 이름의 매개변수로 전달되는 값의 초깃값을 넣어 줄 수 있다.


<br>
### ex1

~~~
let numbers: [Int] = [1,2,3]
//초기값이 0 모든정수를 더한다.
//초기값이 10이면   16이 나옴.
var sum: Int = numbers.reduce(0,{ (first: Int, second: Int) -> Int in
    print("\(first) + \(second) ")
    return first + second
})
print(sum)

var minus: Int = numbers.reduce(0, { $0 - $1})
print(minus)

let names: [String] = ["a","b","c"]
let sumNames: String = names.reduce("",{ $0 + $1 })
print(sumNames)
~~~


<br>
## 마치며

* 조금 익숙한 문법이다. react에서 본듯하다. 
* 이해가 수월해서 재미있었다.
* 적용할 곳이 상상이가는 파트라 한숨이 조금 적었다. 

<br><br>
참고 & 출처 [스위프트 프로그래밍](http://http://www.hanbit.co.kr/media/books/book_view.html?p_code=B5682208459)