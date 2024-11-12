---
title:  "타입 캐스팅"
excerpt: "iOS 프로그래밍을 위한 스위프트 기초"
categories:
  - Yagom
tags:
  - iOS
last_modified_at: 2024-11-12T13:40:02-05:00
---

# 타입 캐스팅
Swift에서 인스턴스의 타입을 확인하거나 특정 타입으로 변환하는 작업.

## 1. 스위프트 타입 캐스팅
- 타입 확인: **is**를 사용해서 인스턴스가 특정 타입인지 확인한다.
- 타입 변환: **as**를 사용하여 부모 클래스나 다른 타입으로 변환한다.
- 형변환: **Double(2)**와 같은 형변환은 타입 캐스팅이 아니라 새로운 값을 생성하는 것이다.

```Swift
class Person {
    var name: String = ""
    func breath() {
        print("숨을 쉽니다")
    }
}

class Student: Person {
    var school: String = ""
    func goToSchool() {
        print("등교를 합니다")
    }
}

class UniversityStudent: Student {
    var major: String = ""
    func goToMT() {
        print("멤버쉽 트레이닝을 갑니다 신남!")
    }
}

var yagom: Person = Person()
var hana: Student = Student()
var jason: UniversityStudent = UniversityStudent()

var result: Bool

result = yagom is Person // true
result = yagom is Student // false
result = yagom is UniversityStudent // false

result = hana is Person // true
result = hana is Student // true
result = hana is UniversityStudent // false

result = jason is Person // true
result = jason is Student // true
result = jason is UniversityStudent // true
```
- **is**를 사용해서 객체의 타입을 확인한다.

```Swift
if yagom is UniversityStudent {
    print("yagom은 대학생입니다")
} else if yagom is Student {
    print("yagom은 학생입니다")
} else if yagom is Person {
    print("yagom은 사람입니다")
} // yagom은 사람입니다
```

## 2. 업 캐스팅
자식 클래스의 인스턴스를 부모 클래스의 타입으로 변환한다.

```swift
var mike: Person = UniversityStudent() as Person
var jina: Any = Person() // as Any 생략 가능
```

## 3. 다운 캐스팅
- 부모 클래스의 인스턴스를 자식 클래스 타입으로 변환한다.
- **as?** 또는 **as!**를 사용한다.
```swift
var optionalCasted: Student?
optionalCasted = mike as? UniversityStudent
optionalCasted = jenny as? UniversityStudent // nil
```

```swift
var forcedCasted: Student
forcedCasted = mike as! UniversityStudent
```

**!**
```swift
var mike: Person = UniversityStudent() as Person

func doSomethingWithSwitch(someone: Person) {
    switch someone {
    case is UniversityStudent:
        (someone as! UniversityStudent).goToMT()  // 강제 다운 캐스팅
    case is Student:
        (someone as! Student).goToSchool()  // 강제 다운 캐스팅
    case is Person:
        (someone as! Person).breath()  // 강제 다운 캐스팅
    }
}

doSomethingWithSwitch(someone: mike as Person)  // 'mike'를 Person로 전달
doSomethingWithSwitch(someone: mike)  // 'mike'를 Person로 전달
```
- mike는 Person 타입으로 선언되어 있으나 실제로는 UniversityStudent 인스턴스이다.
- mike는 Person 타입으로 다뤄지기 때문에 Person 클래스에 정의된 메서드나 속성만 사용할 수 있다.
- 하지만 mike는 실제로 UniversityStudent 객체이기 때문에 타입을 확인할 때는 UniversityStudent로 판단된다.
- mike는 UniversityStudent에서 정의된 메서드나 속성에는 접근할 수 없다.
- 접근하기 위해서 **as!**를 통해서 다운 캐스팅을 진행해주는 것이다.