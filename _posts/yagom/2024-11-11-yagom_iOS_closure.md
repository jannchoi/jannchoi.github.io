---
title:  "클로저"
excerpt: "iOS 프로그래밍을 위한 스위프트 기초"
categories:
  - Yagom
tags:
  - iOS
last_modified_at: 2024-11-11T13:40:02-05:00
---

## 1. 클로저(Closure)란
- 실행 가능한 코드 블럭.
- 함수와 다르게 이름 정의는 필요하지 않지만, 매개변수 전달과 반환값이 존재할 수 있다.
- 함수는 이름이 있는 클로저이다.
- 일급객체로 전달인자, 변수, 상수 등에 저장 및 전달이 가능하다.

## 2. 기본 클로저 문법
중괄호로 감싸져 있으며 매개변수와 반환값을 적고 **in**키워드를 통해 매개변수와 실행코드를 분리한다.
```swift
{ (매개변수 목록) -> 반환타입 in
    실행 코드
}

let sum: (Int, Int) -> Int = { (a: Int, b: Int) in
    return a + b
}

let result = sum(3, 5)
print(result)  // 8

```

## 3. 함수의 전달인자로서 클로저

함수의 전달인자로 많이 사용된다.
```swift
let add: (Int, Int) -> Int = { (a, b) in return a + b }
let subtract: (Int, Int) -> Int = { (a, b) in return a - b }

func calculate(a: Int, b: Int, method: (Int, Int) -> Int) -> Int {
    return method(a, b)
}

var result: Int
result = calculate(a: 10, b: 5, method: add)
print(result)  // 15

result = calculate(a: 10, b: 5, method: subtract)
print(result)  // 5
```

```swift
result = calculate(a: 10, b: 5, method: { (a, b) in return a * b })
print(result)  // 50
```

## 4. 클로저의 축약 문법
 
### 1. 클로저 표현의 축약
1. 후행 클로저
- 함수의 마지막 인자가 클로저일 경우 해당 클로저를 함수 호출 소괄호 외부에 정의할 수 있다.
```swift
result = calculate(a: 10, b: 10) { (left: Int, right: Int) -> Int in
    return left + right
}
```

2. 반환타입 생략
- 컴파일러가 매개변수와 반환 타입을 추론할 수 있는 경우 반환 타입을 생략할 수 있다. 
```swift
result = calculate(a: 10, b: 10) { (left, right) in
    return left + right
}
```

3. 단축 인자 이름
매개변수 이름이 필요 없는 경우 $0, $1 등으로 간단히 사용할 수 있다.
```swift
result = calculate(a: 10, b: 10) {
    return $0 + $1
}
```

4. 암시적 반환 표현
클로저의 마지막 줄이 반환 값일 경우 return 키워드를 생략할 수 있다.
```swift
result = calculate(a: 10, b: 10) { $0 + $1 }
```