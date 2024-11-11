---
title:  "열거형"
excerpt: "iOS 프로그래밍을 위한 스위프트 기초"
categories:
  - Yagom
tags:
  - iOS
last_modified_at: 2024-11-11T13:40:02-05:00
---

1. 열거형 정의
- 여러 값을 하나의 타입으로 묶어 열거형 자체가 하나의 데이터 타입이 된다.
- 열거형 이름은 대문자 카멜케이스, 각 case는 소문자 카멜케이스로 정의한다.

```swift
enum BoostCamp {
    case iosCamp
    case androidCamp
    case webCamp
}
```
2. 열거형 사용
- 타입이 명확할 경우 열거형의 이름을 생략할 수 있다.
- switch 구문에서 열거형을 자주 사용한다.
```swift
enum Weekday {
    case mon, tue, wed, thu, fri, sat, sun
}

var day: Weekday = .mon  // 타입이 명확하면 열거형 이름을 생략 가능

switch day {
case .mon, .tue, .wed, .thu:
    print("평일입니다")
case .fri:
    print("불금 파티!!")
case .sat, .sun:
    print("신나는 주말!!")
}
```

3. rawValue(원시값)
- rawValue를 통해 각 case에 원시값을 할당할 수 있다. 기본적으로 정수값이 할당되며 값을 직접 지정할 수 있다.
- rawValue는 열거형의 각 case에 고유한 값을 부여하고 그 값은 정수나 문자열(Hashable 프로토콜을 따르는 타입)로 지정할 수 있다.

```swift
enum Fruit: Int {
    case apple = 0
    case grape = 1
    case peach
}

print("Fruit.peach.rawValue == \(Fruit.peach.rawValue)")  // 2

enum School: String {
    case elementary = "초등"
    case middle = "중등"
    case high = "고등"
    case university  // 자동으로 "university"
}

print("School.middle.rawValue == \(School.middle.rawValue)")  // 중등
```

4. 원시값 초기화
- rawValue를 사용하여 열거형 인스턴스를 초기화할 수 있다.
- rawValue가 case에 해당하지 않는 경우 옵셔널 타입으로 반환된다.
```swift
let apple: Fruit? = Fruit(rawValue: 0)  // 정상적인 초기화

if let orange: Fruit = Fruit(rawValue: 5) {
    print("rawValue 5에 해당하는 케이스는 \(orange)입니다")
} else {
    print("rawValue 5에 해당하는 케이스가 없습니다")  // 해당하는 케이스가 없음
}
```
5. 메서드
- 열거형에 메서드를 추가할 수 있다.
```swift
enum Month {
    case dec, jan, feb
    case mar, apr, may
    case jun, jul, aug
    case sep, oct, nov
    
    func printMessage() {
        switch self {
        case .mar, .apr, .may:
            print("따스한 봄~")
        case .jun, .jul, .aug:
            print("여름 더워요~")
        case .sep, .oct, .nov:
            print("가을은 독서의 계절!")
        case .dec, .jan, .feb:
            print("추운 겨울입니다")
        }
    }
}

Month.mar.printMessage()  // 따스한 봄~
```