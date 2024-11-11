---
title:  "상속"
excerpt: "iOS 프로그래밍을 위한 스위프트 기초"
categories:
  - Yagom
tags:
  - iOS
last_modified_at: 2024-11-11T13:40:02-05:00
---

## 1. 클래스 상속
- 스위프트에서는 클래스만 상속할 수 있으며 단일 상속만 가능하다.

## 2. 상속 예시: 기본 클래스와 서브 클래스
```swift
class Human {
    var name: String = ""
    
    func introduce() {
        print("저는 \(name)입니다.")
    }
}

class Teacher: Human {
    var subject: String = ""
    
    override func introduce() {
        print("저는 \(name)이고, 담당 과목은 \(subject)입니다.")
    }
}
```
- Teacher클래스는 Human의 name 속성과 introduce() 메서드를 상속받는다.
- introduce() 메서드는 override를 사용하여 재정의한다.

## 3. final 키워드로 상속, 오버라이딩 제한
final 키워드를 클래스 앞에 붙이면 더 이상 상속할 수 없고, 메서드나 속성 앞에 붙이면 재정의할 수 없다.

```swift
final class Human {
    var name: String = ""
}

class Teacher: Human {} // 오류 발생, 'Human' 클래스가 final로 선언되었기 때문
```

## 4. 타입 메서드: static vs class
- static: 재정의 불가능한 타입 메서드
- class: 재정의 가능한 타입 메서드

```swift
class Human {
    static func species() -> String {
        return "Homo sapiens"
    }

    class func greeting() -> String {
        return "Hello!"
    }
}

class Teacher: Human {
    override class func greeting() -> String {
        return "Welcome, students!"
    }
}
```

## 5. 오버라이팅
서브 클래스에서 상속받은 메서드, 프로퍼티 등을 재정의할 때 사용하며 
override 키워드를 붙여 컴파일러가 슈퍼 클래스에 동일한 메서드가 존재하는지 확인하도록 한다.

```swift
class Human {
    func sayHello() {
        print("Hello!")
    }
}

class Teacher: Human {
    override func sayHello() {
        super.sayHello()  // 슈퍼 클래스의 메서드 호출
        print("Welcome to the class.")
    }
}
```

## 6. 프로퍼티 오버라이딩
상속 받은 프로퍼티를 재정의하여 getter 및 setter를 수정하거나 프로퍼티 옵저버를 추가할 수 있다.
```swift
class Human {
    var name: String = "Unknown"
}

class Teacher: Human {
    override var name: String {
        get {
            return "Professor \(super.name)"
        }
        set {
            super.name = newValue
        }
    }
}
```


#### 참고
- https://babbab2.tistory.com/126