---
layout: post
title: "swift - extension."
date: 2017-05-08 12:43:58
image: '/assets/img/'
description: 'swift - extension'
main-class: 'swift'
color: '#1D91F5'
tags:
- swift
categories:
twitter_text:
introduction: 'swift - extension'
---

<br><br>
Extension
====================

* 기능확장을 위한 여러가지들을 알아보자.

<br>
Subscript
================


* 타입의 요소에 접근하는 단축 문법을 subscript라고 합니다.
* 우리는 array[i]를 통해 i 번째 값을 가져올수있습니다. (특정 함수 x)
* 이것이바로 subscript입니다.


<br>
ex1
------------
* school class에 [1](subscript)을 통해  students의 두번째 학생 정보를 가져올 수 있는 코드입니다.
* 해당 번호에 학생을 추가할 수도 있도록 하였습니다.
 
~~~

class School {
    var number: Int = 0
    var students: [Student] = [Student] ()
    
    func addStudent(name: String){
        let student: Student = Student(name:name, number: self.number)
        self.students.append(student)
        self.number += 1
    }
    
    func addStudents(names: String ...) {
        for name in names {
            self.addStudent(name: name)
        }
    }
    //index가 학생수보다 작으면 학생 배열의 값을 넘겨준다.
    subscript(index: Int) -> Student? {
        get{
                if index < self.number {
                    return self.students[index]
                }
                return nil
            }
        
        set{
            guard var newStudent: Student = newValue else { return }
            
            var number: Int = index
            if number > self.number {
                number = self.number
                self.number += 1
            }
            
            newStudent.number = number
            self.students.append(newStudent)
        
        }
    }
}

let highSchool: School = School()
highSchool.addStudents(names: "a", "b", "c")

//3이라는 번호에 학생을 할당
highSchool[3] = Student(name: "ohayo", number: 0)

let aStudent: Student? = highSchool[1]
print("\(aStudent?.number) \(aStudent?.name)")
Optional("a")


~~~

<br><br>
inheritance
================

* 다른언어에 있는 상속과 같은 개념입니다.
* 부모클래스의 메서드를 호출할수있고 프로퍼티,subscript도 사용가능합니다. 
* 당연히 재정의도 가능하구요.
* 상속하지 않은 아무것도 없는 단일클래스를 baseclass라고 합니다.


<br>
ex1
------------

* 기본 상속받는 형태 입니다.

~~~
class name: 상위클래스 {
}
~~~

<br>
ex2
------------
* 기본적인 상속 모양을 봅시다. 
* 상속한 상위 메소드를 사용할 수 있는것을 확인할 수 있습니다.
 
 ~~~
class Person {
    var name: String = ""
    var age: Int = 0
    var introduction: String {
        return "name: \(name) age:\(age)"
    }
    
    func speak() {
        print("abcddd")
    }
}

class Student: Person {
    
    var grade: String = "F"
    func study() {
        print("Study hard")
    }
    
}

let pk: Person = Person()
pk.name = "pk"
pk.age = 10

let pkStudent: Student = Student()
pkStudent.speak()
 ~~~
 

 
<br><br>
Override
============

* 자바와같이 부모로부터 받은 특성을 재정의 할 수 있습니다.
* override 키워드를 써줘야합니다.
* 재정의 전 부모의 함수를 쓰고싶으면 super를 붙여줍니다.
* 아참 subscript도 재정의가 가능하고, super로 마찬가지로 접근이 가능해요.

<br>
ex1
------------
* 위의 코드에서 간단하게 수정하여 speak를 override 했습니다.
* super.speak row를 통해 상위 부모의 클래스가 호출 되는 것을 확인할 수 있습니다.

~~~
class Person {
    var name: String = ""
    var age: Int = 0
    var introduction: String {
        return "name: \(name) age:\(age)"
    }
    
    func speak() {
        print("abcddd")
    }
}

class Student: Person {
    
    var grade: String = "F"
    func study() {
        print("Study hard")
    }
    override func speak() {
        super.speak()
        print("override speack abcddd")
    }
    
}

let pk: Person = Person()
pk.name = "pk"
pk.age = 10

let pkStudent: Student = Student()
pkStudent.speak()
~~~

<br>
ex2
------------

* 함수와 마찬가지로 변수도 override 해줄 수 있습니다.


~~~
class Person {
    var name: String = ""
    var age: Int = 0
    var introduction: String {
        return "name: \(name) age:\(age)"
    }
    
    func speak() {
        print("abcddd")
    }
}

class Student: Person {
    
    var grade: String = "F"
    func study() {
        print("Study hard")
    }
    override func speak() {
        super.speak()
        print("override speack abcddd")
    }
    override var introduction: String {
        return super.introduction + "" + "학점 : \(self.grade)"
    }
    
}

let pk: Person = Person()
pk.name = "pk"
pk.age = 10

let pkStudent: Student = Student()
pkStudent.speak()

pkStudent.introduction

~~~

* 프로퍼티 감시자(didset, willset) 역시 재정의 할수 있습니다. 하지만 생략하죠. 

<br>
ex3
------------

* subscript도 override 할 수 있습니다. (쓰이는 방식만 참고)



~~~

struct Student {
    var name: String
    var number: Int
}


class School {
    var students: [Student]    = [Student]()
    subscript (index: Int) -> Student {
        return students[index]
    }
}

class MiddleScholl: School {
    var middleStudents: [Student] = [Student]()
    override subscript (index:Int) -> Student {
        print("overred")
        return middleStudents[index]
    }
}


var middleSchool: MiddleScholl = MiddleScholl()
middleSchool.middleStudents = [Student(name: "a",number: 10),Student(name: "a",number: 20)]
print(middleSchool[0])
~~~
<br>
ex4
------------

* !재정의는 final 문구를 통해 막을 수 있습니다

~~~
final class Student: Person {
//error!!! final로 막았기 때문
	override var name: String ..
}
~~~



<br>
<br>
마치며
===========

* 상속자체는 다른 언어(java)등과 유사하여 이해하기 상대적으로 쉬웠다.
* 색다른 개념들을 숙지하는데 더욱 열을 내야할 것이다.

<br><br>
참고 & 출처 [스위프트 프로그래밍](http://http://www.hanbit.co.kr/media/books/book_view.html?p_code=B5682208459)
