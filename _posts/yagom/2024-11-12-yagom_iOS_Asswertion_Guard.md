---
title:  "Assertion과 Guard"
excerpt: "iOS 프로그래밍을 위한 스위프트 기초"
categories:
  - Yagom
tags:
  - iOS
last_modified_at: 2024-11-12T13:40:02-05:00
---

## 1. Assertion
assert함수는 디버깅 모드에서만 동작하며, 예상했던 조건을 검증 하고 실패했을 경우 오류 메시지를 출력한다.
```swift
var someInt: Int = 0
assert(someInt == 0, "someInt != 0") // 조건을 만족하므로 지나감

someInt = 1
// assert(someInt == 0) // 조건 불일치로 동작 중지, 검증 실패
// assert(someInt == 0, "someInt != 0") // "someInt != 0" 메시지와 함께 동작 중지
```

```swift
func functionWithAssert(age: Int?) {
    assert(age != nil, "age == nil")
    assert((age! >= 0) && (age! <= 130), "나이값 입력이 잘못되었습니다")
    print("당신의 나이는 \(age!)세입니다")
}
```

## 2. Guard
guard는 조건이 만족되지 않을 경우 즉시 실행을 종료하고 조건이 맞을 떄만 이후 코드를 실행한다. 
else 블록에서 반드시 return, break 등의 종료 지시어가 있어야 한다.

```swift
func functionWithGuard(age: Int?) {
    guard let unwrappedAge = age, unwrappedAge < 130, unwrappedAge >= 0 else {
        print("나이값 입력이 잘못되었습니다")
        return
    }
    print("당신의 나이는 \(unwrappedAge)세입니다")
}
```
- guard 조건에 맞지 않는 경우 실행이 종료된다.

```swift
func someFunction(info: [String: Any]) {
    guard let name = info["name"] as? String else {
        return
    }
    
    guard let age = info["age"] as? Int, age >= 0 else {
        return
    }
    
    print("\(name): \(age)")
}
```

## 3. 옵셔널 바인딩 비교
- **if let**
```swift
if let unwrapped = someValue {
    // do something
}
// if 구문 외부에서는 unwrapped 사용 불가
```

- **guard let**
```swift
guard let unwrapped = someValue else {
    return
}
// guard 구문 이후에도 unwrapped 사용 가능
```