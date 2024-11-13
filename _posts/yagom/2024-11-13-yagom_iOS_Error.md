---
title:  "에러"
excerpt: "iOS 프로그래밍을 위한 스위프트 기초"
categories:
  - Yagom
tags:
  - iOS
last_modified_at: 2024-11-13T13:40:02-05:00
---

## 1. 오류 처리
스위프트의 오류는 **Error** 프로토콜을 따르는 타입으로 표현된다. **Error** 프로토콜은 요구사항이 없는 빈 프로토콜로, 주로 열거형을 사용해 오류의 종류를 나타낸다.

## 2. 오류 표현
```swift
enum VendingMachineError: Error {
    case invalidInput
    case insufficientFunds(moneyNeeded: Int)
    case outOfStock
}
```

## 3. 오류 던지기
```swift
class VendingMachine {
    let itemPrice: Int = 100
    var itemCount: Int = 5
    var deposited: Int = 0
    
    func receiveMoney(_ money: Int) throws {
        guard money > 0 else {
            throw VendingMachineError.invalidInput
        }
        self.deposited += money
        print("\(money)원 받음")
    }
    
    func vend(numberOfItems numberOfItemsToVend: Int) throws -> String {
        guard numberOfItemsToVend > 0 else {
            throw VendingMachineError.invalidInput
        }
        guard numberOfItemsToVend * itemPrice <= deposited else {
            throw VendingMachineError.insufficientFunds(moneyNeeded: numberOfItemsToVend * itemPrice - deposited)
        }
        guard itemCount >= numberOfItemsToVend else {
            throw VendingMachineError.outOfStock
        }
        
        let totalPrice = numberOfItemsToVend * itemPrice
        self.deposited -= totalPrice
        self.itemCount -= numberOfItemsToVend
        return "\(numberOfItemsToVend)개 제공함"
    }
}
```
- 오류 가능성이 있는 함수는 **throw**를 통해 선언한다.

## 4. 오류 처리 방법
```swift
do {
    try machine.receiveMoney(0)
} catch VendingMachineError.invalidInput {
    print("입력이 잘못되었습니다")
} catch VendingMachineError.insufficientFunds(let moneyNeeded) {
    print("\(moneyNeeded)원이 부족합니다")
} catch VendingMachineError.outOfStock {
    print("수량이 부족합니다")
}
```

```swift
do {
    try machine.receiveMoney(300)
} catch {
    switch error {
    case VendingMachineError.invalidInput:
        print("입력이 잘못되었습니다")
    case VendingMachineError.insufficientFunds(let moneyNeeded):
        print("\(moneyNeeded)원이 부족합니다")
    case VendingMachineError.outOfStock:
        print("수량이 부족합니다")
    default:
        print("알 수 없는 오류: \(error)")
    }
}
```

## 5. try? & try!

1. try?: 오류가 발생하면 **nil**을 반환하고, 그렇지 않다면  옵셔널 타입의 값을 반환한다.
```swift
let result = try? machine.vend(numberOfItems: 2)
print(result) // Optional("2개 제공함") 또는 nil
```
2. try!: 오류가 발생하지 않을 것이 확실할 때 사용하며, 오류 발생 시 런타임 오류로 애플리케이션이 중지된다.
```swift
let result = try! machine.vend(numberOfItems: 1)
print(result) // "1개 제공함"
```

## 6. 추가
- **rethrows**: 클로저로 전달된 함수가 오류를 던질때만 **throws**속성을 추가하는 함수에서 사용된다.
- **defer**: 코드의 끝에 실행될 코드를 정의한다.