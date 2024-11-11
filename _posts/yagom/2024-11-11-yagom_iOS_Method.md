---
title:  "구조체와 클래스 - 인스턴스 메서드 VS 타입 메서드, - 가.불변 프로퍼티와 가.불변 인스턴스"
excerpt: "iOS 프로그래밍을 위한 스위프트 기초"
categories:
  - Yagom
tags:
  - iOS
last_modified_at: 2024-11-11T13:40:02-05:00
---

# 1. 인스턴스 메서드 VS 타입 메서드
## 1. 인스턴스 메서드(Instance Method)
- **인스턴스 메서드**는 클래스나 구조체의 **인스턴스**에서 호출되는 메서드.
- 이 메서드는 **인스턴스의 프로퍼티와 상태**에 접근할 수 있고, 인스턴스를 통해 호출된다.
- 'self'를 사용하여 자신이 속한 인스턴스를 참조할 수 있다.

```swift
```swift
class Student {
    var name: String = "unknown"

    // 인스턴스 메서드
    func introduce() {
        print("저는 \(name)입니다.")
    }
}

let student = Student()
student.name = "John"
student.introduce()  // "저는 John입니다."
```

## 2. 타입 메서드(Type Method)
- **타입 메서드**는 클래스나 구조체 자체에서 호출되는 메서드.
- 타입 메서드는 **static** 또는 **class** 키워드를 통해 정의한다.
  - **static**: 클래스 상속 시 재정의가 불가능한 메서드
  - **class**: 클래스 상속 시 재정의가 가능한 메서드
- 타입 메서드는 인스턴스의 프로퍼티에 접근할 수 없으며 클래스나 구조체의 타입에 속한 데이터만 사용할 수 있다.

```swift
class Student {
    var name: String = "unknown"
    var `class`: String = "Swift"
    
    // 타입 메서드
    class func selfIntroduce() {
        print("학생타입입니다")
    }
    
    // 인스턴스 메서드
    func selfIntroduce() {
        print("저는 \(self.class)반 \(name)입니다")
    }
}

// 타입 메서드 호출
Student.selfIntroduce() // 학생타입입니다

// 인스턴스 생성 및 메서드 호출
var yagom: Student = Student()
yagom.name = "yagom"
yagom.class = "스위프트"
yagom.selfIntroduce() // 저는 스위프트반 yagom입니다

let jina: Student = Student()
jina.name = "jina"
jina.selfIntroduce() // 저는 Swift반 jina입니다
```

**!**
- 인스턴스 메서드는 객체의 상태를 변경하거나 객체와 관련된 작업을 할 때 사용한다.
- 타입 메서드는 객체와 관계없이 클래스나 구조체 자체에서 필요한 작업을 할 때 사용한다.



# 2. 가.불변 프로퍼티와 가.불변 인스턴스
**!**
- 구조체는 값 타입으로 인스턴스가 복사될 때 값이 복사된다.
- 클래스는 참조타입이기 때문에 인스턴스가 참조된다.

1. 가변 프로퍼티 : **var**로 선언된 프로퍼티로 값을 수정할 수 있다.
#### 클래스
```swift
class Sample {
    var mutableProperty: Int = 100 // 가변 프로퍼티
}

var sample = Sample()
sample.mutableProperty = 200 // 값 변경 가능
```

#### 구조체
```swift
struct Sample {
    var mutableProperty: Int = 100 // 가변 프로퍼티
}

var sample = Sample()
sample.mutableProperty = 200 // 값 변경 가능
```
- 클래스와 구조체 모두 에서 **var**로 선언된 프로퍼티는 값을 변경할 수 있다.
- 구조체에서는 인스턴스 자체가 **var**로 선언되어야 프로퍼티를 수정할 수 있다.
- 클래스에서는 인스턴스를 참조하는 변수나 상수에서 프로퍼티 값을 수정할 수 있다.
<br><br>

2. 불변 프로퍼티: **let**으로 선언된 프로퍼티로 값을 수정할 수 없다.

#### 클래스
```swift
class Sample {
    let immutableProperty: Int = 100 // 불변 프로퍼티
}

let sample = Sample()
// sample.immutableProperty = 200 // 컴파일 오류: 불변 프로퍼티는 값을 변경할 수 없습니다.
```

#### 구조체
```swift
struct Sample {
    let immutableProperty: Int = 100 // 불변 프로퍼티
}

var sample = Sample()
// sample.immutableProperty = 200 // 컴파일 오류: 불변 프로퍼티는 값을 변경할 수 없습니다.

```
- 클래스와 구조체 모두에서 **let**으로 선언된 프로퍼티는 수정할 수 없다.
<br><br>

3. 가변 인스턴스: 인스턴스를 **var**로 선언한 경우.

#### 클래스
```swift
class Sample {
    let immutableProperty: Int = 100 // 불변 프로퍼티
    var mutableProperty: Int = 200 // 가변 프로퍼티
}

var sample = Sample() // 가변 인스턴스
sample.immutableProperty = 300 // 컴파일 오류: 불변 프로퍼티는 수정할 수 없음
sample.mutableProperty = 400 // 값 수정 가능

```

#### 구조체
```swift
struct Sample {
    let immutableProperty: Int = 100 // 불변 프로퍼티
    var mutableProperty: Int = 200 // 가변 프로퍼티
}

var sample = Sample() // 가변 인스턴스
sample.immutableProperty = 300 // 컴파일 오류: 불변 프로퍼티는 수정할 수 없음
sample.mutableProperty = 400 // 값 수정 가능

```
- 구조체와 클래스에서 가변 인스턴스인 경우 불변 프로퍼티를 수정할 수 없다.
<br><br>

4. 불변 인스턴스: 인스턴스를 **let**으로 선언한 경우
#### 클래스
```swift
class Sample {
    var mutableProperty: Int = 100
    let immutableProperty: Int = 200
}

let sample = Sample() // 불변 인스턴스
sample.mutableProperty = 300 // 값 수정 가능 (참조 타입이므로)
sample.immutableProperty = 400 // 컴파일 오류: 불변 프로퍼티는 수정할 수 없습니다

```

#### 구조체
```swift
struct Sample {
    var mutableProperty: Int = 100
    let immutableProperty: Int = 200
}

let sample = Sample() // 불변 인스턴스
sample.mutableProperty = 300 // 컴파일 오류: 불변 인스턴스는 프로퍼티 값을 수정할 수 없습니다
sample.immutableProperty = 400 // 컴파일 오류: 불변 프로퍼티는 수정할 수 없습니다

```
- 구조체에서 불변 인스턴스를 만들면 모든 프로퍼티를 수정할 수 없다.
- 클래스에서 불변 인스턴스를 만들면 가변 프로퍼티를 수정할 수 있다.