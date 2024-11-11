---
title:  "프로퍼티"
excerpt: "iOS 프로그래밍을 위한 스위프트 기초"
categories:
  - Yagom
tags:
  - iOS
last_modified_at: 2024-11-11T13:40:02-05:00
---

## 1. 프로퍼티의 종류
- 인스턴스 저장 프로퍼티: 각 인스턴스에 고유하게 저장되는 데이터.
- 타입 저장 프로퍼티: 모든 인스턴스가 공유하는 데이터.
- 인스턴스 연산 프로퍼티: 계산된 값을 제공하는 프로퍼티.
- 타입 연산 프로퍼티: 타입 수준에서 계산된 값을 제공.
- 지연 저장 프로퍼티: 처음 접근할 때 초기화되는 프로퍼티.

## 2. 프로퍼티 정의와 사용
- 프로퍼티는 구조체, 클래스, 열거형에 정의될 수 있다. 단, 열거형에는 연산 프로퍼티만 구현 가능하다.
- 연산 프로퍼티는 **var**로만 선언이 가능하며, 읽기 전용(get-ony) 또는 읽기-쓰기(get-set)로 사용할 수 있다.
    - 읽기 전용: get만 구현한다. get 블럭을 생략할 수 있다.
    - 읽기-쓰기: get과 set 블록을 모두 구현한다. 암시적 매개변수 newValue를 사용할 수 있다.

```swift
struct Student {
    // 인스턴스 저장 프로퍼티
    var name: String = ""
    var `class`: String = "Swift"
    var koreanAge: Int = 0

    // 인스턴스 연산 프로퍼티
    var westernAge: Int {
        get {
            return koreanAge - 1
        }
        set(inputValue) {
            koreanAge = inputValue + 1
        }
    }

    // 타입 저장 프로퍼티
    static var typeDescription: String = "학생"

    // 읽기 전용 인스턴스 연산 프로퍼티
    var selfIntroduction: String {
        return "저는 \(self.class)반 \(name)입니다"
    }

    // 읽기 전용 타입 연산 프로퍼티
    static var selfIntroduction: String {
        return "학생타입입니다"
    }
}

// 사용 예시
print(Student.selfIntroduction) // "학생타입입니다"
var yagom = Student()
yagom.name = "yagom"
print(yagom.selfIntroduction) // "저는 Swift반 yagom입니다"

print("제 한국나이는 \(yagom.koreanAge)살이고, 미쿡나이는 \(yagom.westernAge)살입니다.")
// 제 한국나이는 10살이고, 미쿡나이는 9살입니다.
```

## 3. 프로퍼티 응용
```swift
struct Money {
    var currencyRate: Double = 1100
    var dollar: Double = 0

    var won: Double {
        get {
            return dollar * currencyRate
        }
        set {
            dollar = newValue / currencyRate
        }
    }
}

var moneyInMyPocket = Money()
moneyInMyPocket.won = 11000
print(moneyInMyPocket.won) // 11000
moneyInMyPocket.dollar = 10
print(moneyInMyPocket.won) // 11000
```

## 4. 지역변수 와 전역변수
저장 프로퍼티와 연산 프로퍼티의 기능은 한수, 메서드, 클로저, 타입 외부의 전역변수와 지역변수에서도 사용할 수 있다.
```swift
var a: Int = 100
var b: Int = 200
var sum: Int {
    return a + b
}
print(sum) // 300
```

## 5. 프로퍼티 감시자

1. 프로퍼티 감시자 종류
- willSet: 값이 변경되기 직전에 호출, newValue라는 암시적 매개변수 사용하여 새로 설정될 값을 참조한다.
- didSet: 값이 변경된 직후에 호출, oldValue라는 암시적 매개변수를 사용해 이전의 값을 참조한다.
- willSet 또는 didSet 중 하나만 구현할 수 있다.

```swift
struct Money {
    // currencyRate에 대한 프로퍼티 감시자
    var currencyRate: Double = 1100 {
        willSet(newRate) {
            print("환율이 \(currencyRate)에서 \(newRate)으로 변경될 예정입니다")
        }
        didSet(oldRate) {
            print("환율이 \(oldRate)에서 \(currencyRate)으로 변경되었습니다")
        }
    }

    // dollar에 대한 프로퍼티 감시자
    var dollar: Double = 0 {
        willSet {
            print("\(dollar)달러에서 \(newValue)달러로 변경될 예정입니다")
        }
        didSet {
            print("\(oldValue)달러에서 \(dollar)달러로 변경되었습니다")
        }
    }

    // 연산 프로퍼티 won (프로퍼티 감시자 사용 불가)
    var won: Double {
        get {
            return dollar * currencyRate
        }
        set {
            dollar = newValue / currencyRate
        }
    }    
}

// 사용 예시
var moneyInMyPocket = Money()

// currencyRate 변경 시 willSet과 didSet 호출
moneyInMyPocket.currencyRate = 1150
// 출력:
// 환율이 1100.0에서 1150.0으로 변경될 예정입니다
// 환율이 1100.0에서 1150.0으로 변경되었습니다

// dollar 변경 시 willSet과 didSet 호출
moneyInMyPocket.dollar = 10
// 출력:
// 0.0달러에서 10.0달러로 변경될 예정입니다
// 0.0달러에서 10.0달러로 변경되었습니다

// won 계산 (연산 프로퍼티이므로 감시자 사용 불가)
print(moneyInMyPocket.won) // 11500.0
```